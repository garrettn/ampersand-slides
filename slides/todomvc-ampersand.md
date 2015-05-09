### TodoMVC: Ampersand Version

<pre><code contenteditable>
var View = require('ampersand-view');
var State = require('ampersand-state');
var Collection = require('ampersand-collection');
var SubCollection = require('ampersand-subcollection');
var Router = require('ampersand-router');
var debounce = require('debounce');
var todoTemplate = require('../templates/todo.jade');
var STORAGE_KEY = 'todos-ampersand';
var ENTER_KEY = 13;
var ESC_KEY = 27;

var TodoView = View.extend({
    // note that Ampersand is extrememly flexible with templating.
    // This template property can be:
    //   1. A plain HTML string
    //   2. A function that returns an HTML string
    //   3. A function that returns a DOM element
    //
    // Here we're using a jade template. A browserify transform
    // called 'jadeify' lets us require a ".jade" file as if
    // it were a module and it will compile it to a function
    // for us. This function returns HTML as per #2 above.
    template: todoTemplate,
    // Events work like backbone they're all delegated to
    // root element.
    events: {
        'change [data-hook=checkbox]': 'handleCheckboxChange',
        'click [data-hook=action-delete]': 'handleDeleteClick',
        'dblclick [data-hook=title]': 'handleDoubleClick',
        'keyup [data-hook=input]': 'handleKeypress',
        'blur [data-hook=input]': 'handleBlur'
    },
    // Declarative data bindings
    bindings: {
        'model.title': [
            {
                type: 'text',
                hook: 'title'
            },
            {
                type: 'value',
                hook: 'input'
            }
        ],
        'model.editing': [
            {
                type: 'toggle',
                yes: '[data-hook=input]',
                no: '[data-hook=view]'
            },
            {
                type: 'booleanClass'
            }
        ],
        'model.completed': [
            {
                type: 'booleanAttribute',
                name: 'checked',
                hook: 'checkbox'
            },
            {
                type: 'booleanClass'
            }
        ]
    },
    render: function () {
        // Render this with template provided.
        // Note that unlike backbone this includes the root element.
        this.renderWithTemplate();
        // cache reference to `input` for speed/convenience
        this.input = this.queryByHook('input');
    },
    handleCheckboxChange: function (e) {
        this.model.completed = e.target.checked;
    },
    handleDeleteClick: function () {
        this.model.destroy();
    },
    // Just put us in edit mode and focus
    handleDoubleClick: function () {
        this.model.editing = true;
        this.input.focus();
    },
    handleKeypress: function (e) {
        if (e.which === ENTER_KEY) {
            this.input.blur();
        } else if (e.which === ESC_KEY) {
            this.input.value = this.model.title;
            this.input.blur();
        }
    },
    // Since we always blur even in the other
    // scenarios we use this as a 'save' point.
    handleBlur: function () {
        var val = this.input.value.trim();
        if (val) {
            this.model.set({
                title: val,
                editing: false
            });
        } else {
            this.model.destroy();
        }
    }
});

var MainView = View.extend({
    events: {
        'keypress [data-hook~=todo-input]': 'handleMainInput',
        'click [data-hook~=mark-all]': 'handleMarkAllClick',
        'click [data-hook~=clear-completed]': 'handleClearClick'
    },
    // Declaratively bind all our data to the template.
    // This means only changed data in the DOM is updated
    // with this approach we *only* ever touch the DOM with
    // appropriate dom methods. Not just `innerHTML` which
    // makes it about as fast as possible.
    // These get re-applied if the view's element is replaced
    // or if the model isn't there yet, etc.
    // Binding type reference:
    // http://ampersandjs.com/docs#ampersand-dom-bindings-binding-types
    bindings: {
        // Show hide main and footer
        // based on truthiness of totalCount
        'model.totalCount': {
            type: 'toggle',
            selector: '#main, #footer'
        },
        'model.completedCount': [
            // Hides when there are none
            {
                type: 'toggle',
                hook: 'clear-completed'
            },
            // Inserts completed count
            {
                type: 'text',
                hook: 'completed-count'
            }
        ],
        // Inserts HTML from model that also
        // does pluralizing.
        'model.itemsLeftHtml': {
            type: 'innerHTML',
            hook: 'todo-count'
        },
        // Add 'selected' to right
        // element
        'model.mode': {
            type: 'switchClass',
            name: 'selected',
            cases: {
                'all': '[data-hook=all-mode]',
                'active': '[data-hook=active-mode]',
                'completed': '[data-hook=completed-mode]',
            }
        },
        // Bind 'checked' state of checkbox
        'model.allCompleted': {
            type: 'booleanAttribute',
            name: 'checked',
            hook: 'mark-all'
        }
    },
    // cache
    initialize: function () {
        this.mainInput = this.queryByHook('todo-input');
        this.renderCollection(app.me.todos.subset, TodoView, this.queryByHook('todo-container'));
    },
    // handles DOM event from main input
    handleMainInput: function (e) {
        var val = this.mainInput.value.trim();
        if (e.which === ENTER_KEY && val) {
            app.me.todos.add({title: val});
            this.mainInput.value = '';
        }
    },
    // Here we set all to state provided.
    handleMarkAllClick: function () {
        var targetState = !app.me.allCompleted;
        app.me.todos.each(function (todo) {
            todo.completed = targetState;
        });
    },
    // Handler for clear click
    handleClearClick: function () {
        app.me.todos.clearCompleted();
    }
});

var Todo = State.extend({
    // Properties this model will store
    props: {
        title: {
            type: 'string',
            default: ''
        },
        completed: {
            type: 'boolean',
            default: false
        }
    },
    // session properties work the same way as `props`
    // but will not be included when serializing.
    session: {
        editing: {
            type: 'boolean',
            default: false
        }
    },
    destroy: function () {
        if (this.collection) {
            this.collection.remove(this);
        }
    }
});

var Todos = Collection.extend({
    model: Todo,
    initialize: function () {
        // Attempt to read from localStorage
        this.readFromLocalStorage();

        // This is what we'll actually render
        // it's a subcollection of the whole todo collection
        // that we'll add/remove filters to accordingly.
        this.subset = new SubCollection(this);

        // We put a slight debounce on this since it could possibly
        // be called in rapid succession.
        this.writeToLocalStorage = debounce(this.writeToLocalStorage, 100);

        // Listen for storage events on the window to keep multiple
        // tabs in sync
        window.addEventListener('storage', this.handleStorageEvent.bind(this));

        // We listen for changes to the collection
        // and persist on change
        this.on('all', this.writeToLocalStorage, this);
    },
    getCompletedCount: function() {
        return this.reduce(function(total, todo){
            return todo.completed ? ++total : total;
        }, 0);
    },
    // Helper for removing all completed items
    clearCompleted: function () {
        var toRemove = this.filter(function (todo) {
            return todo.completed;
        });
        this.remove(toRemove);
    },
    // Updates the collection to the appropriate mode.
    // mode can 'all', 'completed', or 'active'
    setMode: function (mode) {
        if (mode === 'all') {
            this.subset.clearFilters();
        } else {
            this.subset.configure({
                where: {
                    completed: mode === 'completed'
                }
            }, true);
        }
    },
    // The following two methods are all we need in order
    // to add persistance to localStorage
    writeToLocalStorage: function () {
        localStorage[STORAGE_KEY] = JSON.stringify(this);
    },
    readFromLocalStorage: function () {
        var existingData = localStorage[STORAGE_KEY];
        if (existingData) {
            this.set(JSON.parse(existingData));
        }
    },
    // Handles events from localStorage. Browsers will fire
    // this event in other tabs on the same domain.
    handleStorageEvent: function (e) {
        if (e.key === STORAGE_KEY) {
            this.readFromLocalStorage();
        }
    }
});

var Me = State.extend({
    initialize: function () {
        // Listen to changes to the todos collection that will
        // affect lengths we want to calculate.
        this.listenTo(this.todos, 'change:completed add remove', this.handleTodosUpdate);
        // We also want to calculate these values once on init
        this.handleTodosUpdate();
        // Listen for changes to `mode` so we can update
        // the collection mode.
        this.listenTo(this, 'change:mode', this.handleModeChange);
    },
    collections: {
        todos: Todos
    },
    // We used only session properties here because there's
    // no API or persistance layer for these in this app.
    session: {
        activeCount: {
            type: 'number',
            default: 0
        },
        completedCount: {
            type: 'number',
            default: 0
        },
        totalCount:{
            type: 'number',
            default: 0
        },
        allCompleted: {
            type: 'boolean',
            default: false
        },
        mode: {
            type: 'string',
            values: [
                'all',
                'completed',
                'active'
            ],
            default: 'all'
        }
    },
    derived: {
        // We produce this as an HTML snippet here
        // for convenience since it also has to be
        // pluralized it was easier this way.
        itemsLeftHtml: {
            deps: ['activeCount'],
            fn: function () {
                var plural = (this.activeCount === 1) ? '' : 's';
                return '<strong>' + this.activeCount + '</strong> item' + plural + ' left';
            }
        }
    },
    // Calculate and set various lengths we're
    // tracking. We set them as session properties
    // so they're easy to listen to and bind to DOM
    // where needed.
    handleTodosUpdate: function () {
        var total = this.todos.length;
        // use a method we defined on the collection itself
        // to count how many todos are completed
        var completed = this.todos.getCompletedCount();
        // We use `set` here in order to update multiple attributes at once
        // It's possible to set directely using `this.completedCount = completed` ...
        this.set({
            completedCount: completed,
            activeCount: total - completed,
            totalCount: total,
            allCompleted: total === completed
        });
    },
    handleModeChange: function () {
        this.todos.setMode(this.mode);
    }
});

var Router = Router.extend({
    routes: {
        '*filter': 'setFilter'
    },
    setFilter: function (arg) {
        app.me.mode = arg || 'all';
    }
});

window.app = {
    init: function () {
        // Model representing state for
        // user using the app. Calling it
        // 'me' is a bit of convention but
        // it's basically 'app state'.
        this.me = new Me();
        // Our main view
        this.view = new MainView({
            el: document.body,
            model: this.me
        });
        // Create and fire up the router
        this.router = new Router();
        this.router.history.start();
    }
};

window.app.init();
</code></pre>
