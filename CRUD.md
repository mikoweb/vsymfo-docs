# Create, read, update and delete

- [Creating a CRUD Controller](#creating-a-crud-controller)
    - [Create](#create)
    - [Read](#read)
    - [Update](#update)
    - [Delete](#delete)
- [CRUD Routing Loader](#crud-routing-loader)

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
`message_parameters` | Translation parameters for `message_prefix`. | `array` | 0 | `[]`
`route_prefix` | Prefix of all routes used in controller. | `string` | 1 |
`route_params` | Closure used to generate route params. | `function ($entity) { // ... }` | 0 | In brief `'id' => $entity->getId()` |
`manager` | Manager service. | `ControllerManagerInterface` | 1 |

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
