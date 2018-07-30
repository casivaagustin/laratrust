Middleware
==========

.. _middleware-configuration:

Configuration
^^^^^^^^^^^^^

The middleware are registered automatically as ``role``, ``permission`` and ``ability`` . If you want to change or customize them, go to your ``config/laratrust.php`` and set the ``middleware.register`` value to ``false`` and add  the following to the ``routeMiddleware`` array in ``app/Http/Kernel.php``::

    'role' => \Laratrust\Middleware\LaratrustRole::class,
    'permission' => \Laratrust\Middleware\LaratrustPermission::class,
    'ability' => \Laratrust\Middleware\LaratrustAbility::class,

Concepts
^^^^^^^^

You can use a middleware to filter routes and route groups by permission, role or ability:

.. code-block:: php

    Route::group(['prefix' => 'admin', 'middleware' => ['role:admin']], function() {
        Route::get('/', 'AdminController@welcome');
        Route::get('/manage', ['middleware' => ['permission:manage-admins'], 'uses' => 'AdminController@manageAdmins']);
    });

If you use the pipe symbol it will be an *OR* operation:

.. code-block:: php

    'middleware' => ['role:admin|root']
    // $user->hasRole(['admin', 'root']);

    'middleware' => ['permission:edit-post|edit-user']
    // $user->hasRole(['edit-post', 'edit-user']);

To emulate *AND* functionality you can do:

.. code-block:: php

    'middleware' => ['role:owner|writer,require_all']
    // $user->hasRole(['owner', 'writer'], true);

    'middleware' => ['permission:edit-post|edit-user,require_all']
    // $user->can(['edit-post', 'edit-user'], true);

For more complex situations use ``ability`` middleware which accepts 3 parameters; roles, permissions and options:

.. code-block:: php

    'middleware' => ['ability:admin|owner,create-post|edit-user,require_all']
    // $user->ability(['admin', 'owner'], ['create-post', 'edit-user'], true)

If you want yo use a different guard for the user check you can specify it as an option:

.. code-block:: php

    'middleware' => ['role:owner|writer,require_all|guard:api']
    'middleware' => ['permission:edit-post|edit-user,guard:some_new_guard']
    'middleware' => ['ability:admin|owner,create-post|edit-user,require_all|guard:web']

Teams
-----

If you are using the teams feature and want to use the middleware checking for your teams, you can use:

.. code-block:: php

    'middleware' => ['role:admin|root,my-awesome-team,require_all']
    // $user->hasRole(['admin', 'root'], 'my-awesome-team', true);

    'middleware' => ['permission:edit-post|edit-user,my-awesome-team,require_all']
    // $user->hasRole(['edit-post', 'edit-user'], 'my-awesome-team', true);

    'middleware' => ['ability:admin|owner,create-post|edit-user,my-awesome-team,require_all']
    // $user->ability(['admin', 'owner'], ['create-post', 'edit-user'], 'my-awesome-team', true);

.. NOTE::
    The ``require_all`` and ``guard`` parameters are optional.

Middleware Return
^^^^^^^^^^^^^^^^^

The middleware supports two types of returns in case the check fails. You can configure the return type and the value in the ``config/laratrust.php`` file.

Abort
-----

By default the middleware aborts with a code ``403`` but you can customize it by changing the ``middleware_params`` value.

Redirect
--------

To make a redirection in case the middleware check fails, you will need to change the ``middleware_handling`` value to ``redirect`` and the ``middleware_params`` to the route you need to be redirected. Leaving the configuration like this:

.. code-block:: php

    'middleware' => [
        'register' => true,
        'handling' => 'redirect',
        'params' => '/',
    ],
