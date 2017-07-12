# View Helpers

View helpers are used to consolidate code for creating or modifying HTML, simplifying your views and minimizing redundancy. For example, tasks such as escaping HTML, formatting dates, and creating commonly-used interface components are handled by was of view helpers. For a more complete introduction, read [Zend Framework's documentation](https://docs.zendframework.com/zend-view/helpers/intro/). This page focuses on using Omeka S's view helpers, listed below. Note that Omeka S also uses many of Zend Framework's native view helpers -- consult their documentation for more information.

## Using A View Helper

Helpers are typically called from view scripts. An common example using one of Zend Framework's view helpers is the `Url`, helper, which generates a URL for a route and action:

```php
$this->url(null, ['action' => 'log'], true);
```

Note that while view helper names begin with an uppercase letter, the method invoked from `$this` (the View object) begins with a lowercase letter. Each helper will use its own signature, so consult each helper's documentation for its `__invoke` method.

### From outside a view


## Creating A View Helper In A Module

View Helpers should be placed here in the module's directory structure:

```
MyModule/
  src/
    View/
      Helper/
        MyHelper.php
```

View Helpers inherit from `Zend\View\Helper\AbstractHelper` and implement `Zend\View\Helper\HelperInterface`.

Thus, the basic structure is

```php
namespace MyModule\View\Helper;

use Zend\View\Helper\AbstractHelper;

class MyModuleViewHelper extends AbstractHelper
{
    public function __invoke()
    {
        $view = $this->getView(); // supplied by AbstractHelper
        //return HTML;
}

```

### Config file

For Omeka S to be aware of your View Helper, you must add it to your module's `config/module.config.php` array. That file will contain a great deal more than this information, but this is what will be relevant to the helper:

```php
return [
    'view_helpers' => [
        'invokables' => [
            'myModule' => 'MyModule\View\Helper\ViewHelper',
        ],
    ],
]
```

The `invokables` key signals that the View Helper class can be directly instantiated. Each value in the subsequent array refers to the domain-specific class to refer to.

invokable vs factory

Sometimes, additional data beyond the View object must be passed to the helper. In this case, the View Helper must be created via a factory, rather than an invokable, as defined in the `config.php` file.

To create a factory for your View Helper, put the factory in the following directory:

```
MyModule/
  src/
    Service/
      ViewHelper/
        MyModuleViewHelperFactory.php

```

The file structure for the factory will be akin to:

```php
namespace MyModule\Service\ViewHelper;

use MyModule\View\Helper\ViewHelper;
use Zend\ServiceManager\Factory\FactoryInterface;
use Interop\Container\ContainerInterface;

class MyModuleViewHelperFactory implements FactoryInterface
{
    public function __invoke(ContainerInterface $services, $requestedName, array $options = null)
    {
        $auth = $services->get('Omeka\AuthenticationService');
        $user = $auth->getIdentity();
        return new ViewHelper($user);
    }
}

```

In this example, the View Helper needs information about the currently logged in user, so it passes along that data to the View Helper.

As such, the View Helper's construct method must deal with it:

```php

class ViewHelper extends AbstractHelper
{
    protected $user;

    public function __construct($user)
    {
        $this->user = $user;
    }

    public function __invoke()
    {
        $userRole = $this->user->getRole();
        // return HTML based on the user
    }
}
```




[using partials with helpers]



## Omeka S View Helpers

* Api
* AssetUrl
* BlockAttachmentsForm
* BlockLayout
* BlockShowTitleSelect
* BlockThumbnailTypeSelect
* CkEditor
* DataType
* DeleteConfirm
* helpers.txt
* HtmlElement
* Hyperlink
* I18n
* ItemSetSelector
* ItemSetSelect
* JsTranslate
* Media
* Messages
* NavigationLink
* PageTitle
* Pagination
* Params
* PropertySelector
* PropertySelect
* QueryToHiddenInputs
* ResourceClassSelect
* ResourceSelect
* SearchFilters
* SectionNav
* Setting
* SitePagePagination
* SortLink
* SortSelector
* ThemeSettingAssetUrl
* ThemeSetting
* Trigger
* UploadLimit
* UserIsAllowed