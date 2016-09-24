# Create, read, update and delete

- [Creating a CRUD Controller](#creating-a-crud-controller)
    - [Create](#create)
    - [Read](#read)
    - [Update](#update)
    - [Delete](#delete)
    - [getCrudOptions()](#getcrudoptions)
        - [Options list](#options-list)
    - [CRUD methods options](#crud-methods-options)
- [CRUD Routing Loader](#crud-routing-loader)
    - [Options](#options)
- [Manager Service](#manager-service)

## Creating a CRUD Controller

Your controller should inherit from class `CrudableControllerAbstract` but typically used class in panel is 
`vSymfo\Bundle\CoreBundle\Controller`. It is a flexible solution that allows make your own CRUD class. It is possible
through by override method `getCrudClass()` in your crudable controller, an example in [this link](https://github.com/mikoweb/vsymfo-panel-bundle/blob/master/Controller/Controller.php#L23).

Below is an example controller without unnecessary details.

```php
namespace App\PanelBundle\Controller;

use vSymfo\Bundle\PanelBundle\Controller\Controller;

class ContentController extends Controller
{
    public function getCrudOptions()
    {
        return [
            'route_prefix' => 'panel_content',
            'manager' => $this->getManager(),
            'message_prefix' => 'content.messages',
        ];
    }

    protected function getManager()
    {
        return $this->get('app.manager.content');
    }

    protected function getViewPrefix()
    {
        return 'AppPanelBundle:Content';
    }
}
```

Now you can start implement the CRUD methods. A good idea would be to use interfaces:
[WritableInterface](https://github.com/mikoweb/vsymfo-core/blob/master/Controller/Interfaces/WritableInterface.php#L23),
[ReadableInterface](https://github.com/mikoweb/vsymfo-core/blob/master/Controller/Interfaces/ReadableInterface.php#L23),
[RemovableInterface](https://github.com/mikoweb/vsymfo-core/blob/master/Controller/Interfaces/RemovableInterface.php#L23).

## Create

Use below methods to save new entity.

```php
public function createAction(Request $request)
{
    $data = $this->crudFactory()->create($this)->create($request);

    return $this->renderDocument(':new.html.twig', [
        'form' => $data->getForm()->createView(),
    ]);
}

public function storeAction(Request $request)
{
    return $this->crudFactory()->create($this)->store($request)->getResponse();
}
```

## Read

Use below methods to fetch entities.

```php
public function indexAction(Request $request)
{
    $data = $this->crudFactory()->create($this)->index($request);

    return $this->renderDocument(':list.html.twig', [
        'contents' => $data->getCollection(),
    ]);
}

public function showAction(Request $request)
{
    $data = $this->crudFactory()->create($this)->show($request);

    return $this->renderDocument(':show.html.twig', [
        'content' => $data->getEntity(),
    ]);
}
```

## Update

Use below methods to update entity.

```php
public function editAction(Request $request)
{
    $data = $this->crudFactory()->create($this)->edit($request);

    return $this->renderDocument(':edit.html.twig', [
        'form' => $data->getForm()->createView(),
    ]);
}

public function updateAction(Request $request)
{
    return $this->crudFactory()->create($this)->update($request)->getResponse();
}
```

## Delete

Use below method to destroy entity.

```php
public function destroyAction(Request $request)
{
    $data = $this->crudFactory()->create($this)->destroy($request);

    if ($redirect = $data->getResponse()) {
        return $redirect;
    }

    return $this->renderDocument(':remove.html.twig', [
        'content' => $data->getEntity(),
        'form' => $data->getForm()->createView(),
    ]);
}
```

## getCrudOptions()

These options are used to create CRUD object when you call method $this `$this->crudFactory()->create($this)`.

```php
public function getCrudOptions()
{
    return [
        // CRUD options
    ];
}
```

### Options list

Option | Description | Type | Required | Default
:---: | --- | :---: | :---: | :---:
`flash_prefix` | Used as a prefix in firts argument of `addFlash`. | `string` | 0 | `''`
`message_prefix` | Used as a prefix in translation text of second argument `addFlash`. | `string` | 0 | `''`
`message_domain` | Translation domain for `message_prefix`. | `string|null` | 0 | `null`
`message_parameters` | Translation parameters for `message_prefix`. | `hash` | 0 | `[]`
`route_prefix` | Prefix of all routes used in controller. | `string` | 1 |
`route_params` | Closure used to generate route params. | `function ($entity) { // ... }` | 0 | In brief `'id' => $entity->getId()` |
`manager` | Manager service. | `ControllerManagerInterface` | 1 |

## CRUD methods options

All CRUD methods e.g. `edit`, `update`, `show`, `index` etc. has second optional argument `options`.

```php
$crud->edit($request, [
 // ...
]);
```

Option | Description | Type | Required | Default
:---: | --- | :---: | :---: | :---:
`form_options` | Third argument of `createForm`. | `array` | 0 | `[]`
`form_type` | First argument of `createForm`. | `mixed` | 0 | `null`
`add_flash` | If true then call `addFlash`. | `bool` | 0 | `true`
`redirect_url` | Some actions are doing a redirect after end. CRUD can independently deduced where to redirect. | `string|null` | 0 | `null`
`route_params` | Override `route_params` from `getCrudOptions`. | `callable|null` | 0 | `null`
`events` | See to `DataEvent` class. | `hash` | 0 | `[]`

# CRUD Routing Loader

This is example CRUD routing entry.

`routing.yml`

```yml
panel_content:
    resource: "@AppPanelBundle/Resources/config/routing/content.yml"
    prefix:   /content
    type:     crud
```

`content.yml`

```yml
crud:
    path:                  /
    options:
        route_prefix:      panel_content
        controller:        AppPanelBundle:Content
        index_pagination:  true
        index_sort:        true
        index_sort_params:
            sort: c.id
            direction: asc

# _my_own_route <=> panel_content_my_own_route
# _controller: myOwn <=> AppPanelBundle:Content:myOwn

_my_own_route:
    path:     /my-own-url
    methods:  [GET]
    defaults: { _controller: myOwn }
```

## Options

Option | Description | Type | Required | Default
:---: | --- | :---: | :---: | :---:
`route_prefix` | Prefix for all generated routes names. | `string` | 1 |
`controller` | Prefix controller name for all generated routes. | `string` | 1 |
`except` | List of unused CRUD methods ie. `read`, `index`, `show`, `create`, `update`, `delete`. | `array` | 0 | `[]`
`index_pagination` | If true then index route has pagination params. | `bool` | 0 | `false`
`index_sort` | If true then index route has sort params. | `bool` | 0 | `false`
`index_sort_params` | This is for `index_sort` option. You can set two properties `sort` and `direction`. They define the defaults values. | `hash` | 0 | `[]`

# Manager Service

This relates to `manager` option in `getCrudOptions`. You should know that CRUD does not work without this service.
When you call `create`, `store`, `destroy` and other methods CRUD performs save etities, create a form
and lots of other stuff. These are the Manager Service responsibilities.

- [How create a Manager Service](Manager-Service.md)
