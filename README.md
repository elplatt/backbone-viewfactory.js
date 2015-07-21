# backbone-viewmanager.js
Manage creation, destruction, and persistence of backbone.js Views.

## Why would I use a `ViewManager`? ##
If your application has Views that are only displayed for certain routes, one approach is to create a new instance of the View each time corresponding route is visited. But this approach is the source of some common problems. If you don't remember to manually close the view when the route changes, it will lead to a memory leak as mutliple copies of the View are created. Similarly, when creating a new view each time, the state of DOM elements will always be reset. State such as expanded/hidden elements and partially-entered data will be lost. Finally, you may know which views you want to show, but without knowing which views are already displayed, there is no simple way to know which views need to be removed/added. `ViewManager` is a simple solution to these problems.

## Dependencies ##

* jQuery
* underscore.js
* backbone.js

## Usage ##

Begin by defining some backbone Views.

    var FooView = Backbone.View.extend({ ... });
    var BarView = Backbone.View.extend({ ... });

Create the `ViewManager` and specify the CSS selector for the element you want to contain the Views (the default is `.content`).

    var vm = new ViewManager({ "selector": "#my-content" });

To get an instance of a given View, use `getView(type, options, reuse)`. If this is the first time getView has been called for the View `type` (or if `reuse`) is `true`, a new View will be created by calling the constructor `type` with the given `options`. Otherwise, the previously created View will be returned (and `options` ignored).

    var foo = vm.getView(FooView, {}).cid;
    // < "view75"
    var bar = vm.getView(BarView, {}).cid;
    // < "view76"
    var foo1 = vm.getView(FooView, {}, true).cid;
    // < "view75"
    var foo2 = vm.getView(FooView, {});
    // < "view77"

To append a view to the DOM element associated with the `ViewManager`, without removing existing views, use `addView()`.

    // No views visible 
    vm.addView(foo)
    // foo visible
    vm.addView(bar)
    // foo and bar visible
    
To show a specific set of views, and close any others that are currently displayed, use `showViews()`.

    // No views visible
    vm.showViews([foo, bar]);
    // foo and bar visible
    vm.showViews([foo2]);
    // Only foo2 visible and remove() called for foo and bar
    
Finally, `showView(v)` is a shorthand for `showViews([v])`.
  
