# Security

Authorization to the resources is based on a simple concept Users Roles. For each controller action is assigned specified
role eg. content editing screen protects role `ROLE_EDIT_CONTENT`. The user can display this page only if he has a role
`ROLE_EDIT_CONTENT`.

## Related links

- [Denying Access, Roles and other Authorization](http://symfony.com/doc/current/security.html#denying-access-roles-and-other-authorization)
- [Using Groups With FOSUserBundle](http://symfony.com/doc/master/bundles/FOSUserBundle/groups.html)

## Access protection with JMSSecurityExtraBundle

In this example I use `@Secure` annotation in controller actions.

- [Security Annotations](http://jmsyst.com/bundles/JMSSecurityExtraBundle/master/annotations)

```php
class UserController extends Controller
{
    /**
     * @Secure(roles="ROLE_EDIT_USER")
     */
    public function editAction(Request $request)
    {
        // ...
    }

    /**
     * @Secure(roles="ROLE_EDIT_USER")
     */
    public function updateAction(Request $request)
    {
        // ...
    }
}
```

## Security Voters

Sometimes it takes something more. Blocking the user can protect by means of role `ROLE_LOCK_USER` but it should not be
possible lock himself. In this case can use Voter Class.

- [How to Use Voters to Check User Permissions](http://symfony.com/doc/current/security/voters.html)

Below is the UserVoter class.

```php
namespace AppBundle\Security\Authorization\Voter;

use AppBundle\Entity\User;
use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
use vSymfo\Core\Security\Authorization\Voter\AbstractVoter;

class UserVoter extends AbstractVoter
{
    const USER_LOCK_ACCESS = 'user_lock_access';

    public function supports($attribute, $subject)
    {
        return $subject instanceof User && in_array($attribute, [
            self::USER_LOCK_ACCESS,
        ]);
    }

    protected function voteOnAttribute($attribute, $entity, TokenInterface $token)
    {
        $user = $token->getUser();

        if (!$user instanceof User) {
            return false;
        }

        if ($attribute === self::USER_LOCK_ACCESS) {
            return $entity->getId() !== $user->getId()
                && !$entity->isLocked()
                && $this->getAuthChecker()->isGranted('ROLE_LOCK_USER');
        }

        return false;
    }
}
```

Now you need register class [as service](http://symfony.com/doc/current/service_container.html).

`services.yml`

```yaml
services:
    app.security.user_voter:
        class: AppBundle\Security\Authorization\Voter\UserVoter
        public: false
        calls:
            - [setContainer, ["@service_container"]]
        tags:
            - { name: security.voter }
```

Now use in your controller.

```php
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Security;

class UserController extends Controller
{
    /**
     * @Security("is_granted('user_lock_access', entity)")
     */
    public function lockAction(User $entity)
    {
        // ...
    }
}
```

Or example without annotation.

```php
class UserController extends Controller
{
    public function lockAction(User $entity)
    {
        $this->denyAccessUnlessGranted('user_lock_access', $entity);
        // ...
    }
}
```
