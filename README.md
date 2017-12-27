# Meetup tag extension for mediawiki

This [mediawiki extension](https://www.mediawiki.org/wiki/Manual:Extensions) adds a custom `<meetup group="..." />` tag that you can include in your wiki page to incorporate
information about upcoming and past events for the group on meetup.com.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See [Deployment](#Deployment) for notes on how to deploy the project on a live system.

### Prerequisites

You need to have a mediawiki installation in order to properly test any changes you make. Luckily
there is an extremely-handy Docker container available to make the process of running mediawiki easier.

### Installing

#### Running the mediawiki install

This is only required the first time you run:

```shell
docker run \
  --name some-mediawiki \
  -v $PWD/data:/var/www/data \
  -p 8080:80 \
  -d mediawiki
```

Open a browser to `http://localhost:8080` and walk through the mediawiki installer. At the end you
will get a `LocalSettings.php` file that you will need to download and edit as shown in [Deployment](#Deployment).

#### Running mediawiki with the extension

After editing `LocalSettings.php`, you can run mediawiki with the extension using:

```shell
docker run \
  --name some-mediawiki \
  -v $PWD/data:/var/www/data \
  -v $PWD/Meetup:/var/www/html/extensions/Meetup \
  -v $PWD/LocalSettings.php:/var/www/html/LocalSettings.php \
  -p 8080:80 \
  -d mediawiki
```

If Docker complains that the port or name is already in use, you may have another instance already running. Try the following steps to kill and remove the existing container:

```shell
docker kill some-mediawiki
docker rm some-mediawiki
```

## Deployment

### Required steps

See the [official mediawiki instructions for installing extensions](https://www.mediawiki.org/wiki/Manual:Extensions#Installing_an_extension), or:

1. Create a new folder called `Meetup` in `$IP/extensions` on your mediawiki server.
1. Copy the `Meetup` folder contents to `$IP/extensions/Meetup`.
1. Add the following to your `LocalSettings.php` file:
   ```php
   wfLoadExtension( 'Meetup' );
   ```

### Optional steps

1. Add the `meetup` debug log group to get separate logs for the extension:
   ```php
   $wgDebugLogGroups = array(
       'meetup' => '/var/log/' . $IP . '/meetup-debug.log',
   );
   ```
   Make sure to use an appropriate log location for your installation.

## Using the extension

Once the extension has been installed, wiki editors can insert the `meetup` custom tag:

```text
<meetup group="{groupID}" />
```

Replace `{groupID}` with the group ID of a Meetup group.

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details the process for submitting pull requests.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/glb/mediawiki-tag-extension-meetup/tags).

## Authors

See the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

## Acknowledgments

* This is a pretty simple extension based heavily on the [sample](https://www.mediawiki.org/wiki/Manual:Tag_extensions).
* Special thanks to the [Magic:NoCache](https://phabricator.wikimedia.org/diffusion/EMNC/browse/master/MagicNoCache.hooks.php) authors for pointing out how to do the special magic required to disable caching so that the page always displays fresh data from the API.
* ... and also to [@PurpleBooth](https://www.github.com/PurpleBooth) for the template used to kick off this `README` and the `CONTRIBUTING` file as well.
* This extension was written for the OWASP project for use in their [chapter wiki pages](https://www.owasp.org/index.php/OWASP_Chapter). Thanks to Tiffany Long for her detailed requirements statement and helpful review during the initial development stage.
