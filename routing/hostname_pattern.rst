.. index::
   single: Routing; Matching on Hostname

How to Match a Route Based on the Host
======================================

You can also match on the HTTP *host* of the incoming request.

.. configuration-block::

    .. code-block:: php-annotations

        // src/AppBundle/Controller/MainController.php
        namespace AppBundle\Controller;

        use Symfony\Bundle\FrameworkBundle\Controller\Controller;
        use Symfony\Component\Routing\Annotation\Route;

        class MainController extends Controller
        {
            /**
             * @Route("/", name="mobile_homepage", host="m.example.com")
             */
            public function mobileHomepageAction()
            {
                // ...
            }

            /**
             * @Route("/", name="homepage")
             */
            public function homepageAction()
            {
                // ...
            }
        }

    .. code-block:: yaml

        mobile_homepage:
            path:     /
            host:     m.example.com
            defaults: { _controller: AppBundle:Main:mobileHomepage }

        homepage:
            path:     /
            defaults: { _controller: AppBundle:Main:homepage }

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>
        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd">

            <route id="mobile_homepage" path="/" host="m.example.com">
                <default key="_controller">AppBundle:Main:mobileHomepage</default>
            </route>

            <route id="homepage" path="/">
                <default key="_controller">AppBundle:Main:homepage</default>
            </route>
        </routes>

    .. code-block:: php

        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('mobile_homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:mobileHomepage',
        ), array(), array(), 'm.example.com'));

        $collection->add('homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:homepage',
        )));

        return $collection;

Both routes match the same path ``/``, however the first one will match
only if the host is ``m.example.com``.

Using Placeholders
------------------

The host option uses the same syntax as the path matching system. This means
you can use placeholders in your hostname:

.. configuration-block::

    .. code-block:: php-annotations

        // src/AppBundle/Controller/MainController.php
        namespace AppBundle\Controller;

        use Symfony\Bundle\FrameworkBundle\Controller\Controller;
        use Symfony\Component\Routing\Annotation\Route;

        class MainController extends Controller
        {
            /**
             * @Route("/", name="projects_homepage", host="{project_name}.example.com")
             */
            public function projectsHomepageAction()
            {
                // ...
            }

            /**
             * @Route("/", name="homepage")
             */
            public function homepageAction()
            {
                // ...
            }
        }

    .. code-block:: yaml

        projects_homepage:
            path:     /
            host:     "{project_name}.example.com"
            defaults: { _controller: AppBundle:Main:projectsHomepage }

        homepage:
            path:     /
            defaults: { _controller: AppBundle:Main:homepage }

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>
        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd">

            <route id="projects_homepage" path="/" host="{project_name}.example.com">
                <default key="_controller">AppBundle:Main:projectsHomepage</default>
            </route>

            <route id="homepage" path="/">
                <default key="_controller">AppBundle:Main:homepage</default>
            </route>
        </routes>

    .. code-block:: php

        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('project_homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:projectsHomepage',
        ), array(), array(), '{project_name}.example.com'));

        $collection->add('homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:homepage',
        )));

        return $collection;

You can also set requirements and default options for these placeholders. For
instance, if you want to match both ``m.example.com`` and
``mobile.example.com``, you use this:

.. configuration-block::

    .. code-block:: php-annotations

        // src/AppBundle/Controller/MainController.php
        namespace AppBundle\Controller;

        use Symfony\Bundle\FrameworkBundle\Controller\Controller;
        use Symfony\Component\Routing\Annotation\Route;

        class MainController extends Controller
        {
            /**
             * @Route(
             *     "/",
             *     name="mobile_homepage",
             *     host="{subdomain}.example.com",
             *     defaults={"subdomain"="m"},
             *     requirements={"subdomain"="m|mobile"}
             * )
             */
            public function mobileHomepageAction()
            {
                // ...
            }

            /**
             * @Route("/", name="homepage")
             */
            public function homepageAction()
            {
                // ...
            }
        }

    .. code-block:: yaml

        mobile_homepage:
            path:     /
            host:     "{subdomain}.example.com"
            defaults:
                _controller: AppBundle:Main:mobileHomepage
                subdomain: m
            requirements:
                subdomain: m|mobile

        homepage:
            path:     /
            defaults: { _controller: AppBundle:Main:homepage }

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>
        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd">

            <route id="mobile_homepage" path="/" host="{subdomain}.example.com">
                <default key="_controller">AppBundle:Main:mobileHomepage</default>
                <default key="subdomain">m</default>
                <requirement key="subdomain">m|mobile</requirement>
            </route>

            <route id="homepage" path="/">
                <default key="_controller">AppBundle:Main:homepage</default>
            </route>
        </routes>

    .. code-block:: php

        use Symfony\Component\Routing\RouteCollection;
        use Symfony\Component\Routing\Route;

        $collection = new RouteCollection();
        $collection->add('mobile_homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:mobileHomepage',
            'subdomain'   => 'm',
        ), array(
            'subdomain' => 'm|mobile',
        ), array(), '{subdomain}.example.com'));

        $collection->add('homepage', new Route('/', array(
            '_controller' => 'AppBundle:Main:homepage',
        )));

        return $collection;

.. tip::

    You can also use service parameters if you do not want to hardcode the
    hostname:

    .. configuration-block::

        .. code-block:: php-annotations

            // src/AppBundle/Controller/MainController.php
            namespace AppBundle\Controller;

            use Symfony\Bundle\FrameworkBundle\Controller\Controller;
            use Symfony\Component\Routing\Annotation\Route;

            class MainController extends Controller
            {
                /**
                 * @Route(
                 *     "/",
                 *     name="mobile_homepage",
                 *     host="m.{domain}",
                 *     defaults={"domain"="%domain%"},
                 *     requirements={"domain"="%domain%"}
                 * )
                 */
                public function mobileHomepageAction()
                {
                    // ...
                }

                /**
                 * @Route("/", name="homepage")
                 */
                public function homepageAction()
                {
                    // ...
                }
            }

        .. code-block:: yaml

            mobile_homepage:
                path:     /
                host:     "m.{domain}"
                defaults:
                    _controller: AppBundle:Main:mobileHomepage
                    domain: '%domain%'
                requirements:
                    domain: '%domain%'

            homepage:
                path:  /
                defaults: { _controller: AppBundle:Main:homepage }

        .. code-block:: xml

            <?xml version="1.0" encoding="UTF-8" ?>
            <routes xmlns="http://symfony.com/schema/routing"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation="http://symfony.com/schema/routing
                    http://symfony.com/schema/routing/routing-1.0.xsd">

                <route id="mobile_homepage" path="/" host="m.{domain}">
                    <default key="_controller">AppBundle:Main:mobileHomepage</default>
                    <default key="domain">%domain%</default>
                    <requirement key="domain">%domain%</requirement>
                </route>

                <route id="homepage" path="/">
                    <default key="_controller">AppBundle:Main:homepage</default>
                </route>
            </routes>

        .. code-block:: php

            use Symfony\Component\Routing\RouteCollection;
            use Symfony\Component\Routing\Route;

            $collection = new RouteCollection();
            $collection->add('mobile_homepage', new Route('/', array(
                '_controller' => 'AppBundle:Main:mobileHomepage',
                'domain' => '%domain%',
            ), array(
                'domain' => '%domain%',
            ), array(), 'm.{domain}'));

            $collection->add('homepage', new Route('/', array(
                '_controller' => 'AppBundle:Main:homepage',
            )));

            return $collection;

.. tip::

    Make sure you also include a default option for the ``domain`` placeholder,
    otherwise you need to include a domain value each time you generate
    a URL using the route.

.. _component-routing-host-imported:

Using Host Matching of Imported Routes
--------------------------------------

You can also set the host option on imported routes:

.. configuration-block::

    .. code-block:: php-annotations

        // src/AppBundle/Controller/MainController.php
        namespace AppBundle\Controller;

        use Symfony\Bundle\FrameworkBundle\Controller\Controller;
        use Symfony\Component\Routing\Annotation\Route;

        /**
         * @Route(host="hello.example.com")
         */
        class MainController extends Controller
        {
            // ...
        }

    .. code-block:: yaml

        app_hello:
            resource: '@AppBundle/Resources/config/routing.yml'
            host:     "hello.example.com"

    .. code-block:: xml

        <?xml version="1.0" encoding="UTF-8" ?>
        <routes xmlns="http://symfony.com/schema/routing"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/routing
                http://symfony.com/schema/routing/routing-1.0.xsd">

            <import resource="@AppBundle/Resources/config/routing.xml" host="hello.example.com" />
        </routes>

    .. code-block:: php

        $collection = $loader->import("@AppBundle/Resources/config/routing.php");
        $collection->setHost('hello.example.com');

        return $collection;

The host ``hello.example.com`` will be set on each route loaded from the new
routing resource.

Testing your Controllers
------------------------

You need to set the Host HTTP header on your request objects if you want to get
past url matching in your functional tests.

.. code-block:: php

    $crawler = $client->request(
        'GET',
        '/homepage',
        array(),
        array(),
        array('HTTP_HOST' => 'm.' . $client->getContainer()->getParameter('domain'))
    );
