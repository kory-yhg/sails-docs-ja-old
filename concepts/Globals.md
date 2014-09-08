#Globals
###Overview

For convenience, Sails exposes a handful of global variables. By default, your app's models, services, and the global sails object are all available on the global scope; meaning you can refer to them by name anywhere in your backend code (as long as Sails has been loaded).
Nothing in Sails core relies on these global variables - each and every global exposed in Sails may be disabled in sails.config.globals (conventionally configured in config/globals.js.)

###The App Object (sails)

In most cases, you will want to keep the sails object globally accessible- it makes your app code much cleaner. However, if you do need to disable all globals, including sails, you can get access to sails on the request object (req).

###Models and Services

Your app's models and services are exposed as global variables using their globalId. For instance, the model defined in the file api/models/Foo.js will be globally accessible as Foo, and the service defined in api/services/Baz.js will be available as Baz.

###Async (async) and Lodash (_)

Sails also exposes an instance of lodash as _, and an instance of async as async. These commonly-used utilities are provided by default so that you don't have to npm install them in every new project. Like any of the other globals in sails, they can be disabled.

###Disabling Globals

Sails determines which globals to expose by looking at sails.config.globals, which is conventionallly configured in config/globals.js.

To disable all global variables, just set the setting to false:

```
// config/globals.js
module.exports.globals = false;
```

To disable some global variables, specify an object instead, e.g.:

```
// config/globals.js
module.exports.globals = {
  _: false,
  async: false,
  models: false,
  services: false
};
```

###Notes

* Bear in mind that none of the globals, including sails, are accessible until after sails has loaded. In other words, you won't be able to use sails.models.user or User outside of a function (since sails will not have finished loading yet.)