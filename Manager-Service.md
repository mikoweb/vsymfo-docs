# The Manager Service

Controller in symfony most frequently operates on four objects ie. _Entity_, _FormBuilder_, _EntityManager_ and _Repository_.
Manager Service helps for controller in this aspect. This makes the code that code of controller is more concise.
In some ways, Manager Service is similar to Model. Manager Service is an important element of [CRUD](CRUD.md).

## How to create a Manager Service

You must create a class that inherits from `ControllerManagerAbstract` or implements `ControllerManagerInterface` then
register it as [a service](http://symfony.com/doc/current/service_container.html).

Below is a sample manager.

```php
namespace App\PanelBundle\Manager;

use App\PanelBundle\Entity\Content;
use App\PanelBundle\Form\Type\ContentFormType;
use App\PanelBundle\Repository\ContentRepository;
use Doctrine\ORM\EntityManager;
use Knp\Component\Pager\Paginator;
use Symfony\Component\Form\FormFactory;
use Symfony\Component\HttpFoundation\Request;
use vSymfo\Core\Entity\EntityFactoryInterface;
use vSymfo\Core\Manager\ControllerManagerAbstract;
use vSymfo\Core\Manager\SortingManager;

class ContentManager extends ControllerManagerAbstract
{
    protected $repository;
    protected $paginator;

    public function __construct(
        EntityManager $manager,
        FormFactory $formFactory,
        EntityFactoryInterface $entityFactory,
        ContentRepository $repository,
        Paginator $paginator
    ) {
        parent::__construct($manager, $formFactory, $entityFactory);
        $this->repository = $repository;
        $this->paginator = $paginator;
    }

    public function getEntityClass()
    {
        return Content::class;
    }

    public function isRightEntity($entity)
    {
        return $entity instanceof Content;
    }

    public function formType()
    {
        return ContentFormType::class;
    }

    public function getPagination(Request $request, $limit)
    {
        $qb = $this->repository->getQueryBuilder();
        $sorting = new SortingManager();
        $sorting->setAllowedColumns(['c.id']);
        $sorting->sort($request, $qb);
        $pagination = $this->paginator->paginate($qb, (int) $request->get('page', 1), $limit);

        return $pagination;
    }
}
```

And this is configuration of the service.

`services.yml`

```yaml
app.manager.content:
    class: App\PanelBundle\Manager\ContentManager
    arguments:
        - "@doctrine.orm.default_entity_manager"
        - "@form.factory"
        - "@vsymfo_core.entity_factory"
        - "@app.repository.content"
        - "@knp_paginator"
```
