# Web Deployment

~Both the Django API and the Ionic app are configured to be automatically deployed to the web using CircleCI~.

Deploying the REST API and Ionic app is _mostly_ handled by CircleCI.

Currently, the following are automatically deployed:

- Staging Django API (`develop` branch)
- Production Django API (`master` branch)
- Staging Ionic web app (`develop` branch)

Notably, the production Ionic app is not automatically deployed (the `master` branch) so much be manually deployed.

## Git

The easiest way to develop and release new features is to use a ["Gitflow"](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) style approach. Create a new _feature branch_, for example `feature/new-nav`, and work locally within this branch until it is ready to test.

When ready to test on the staging server, you merge this feature branch with the `develop` branch either via a pull request or standard merge. CircleCI will detect this change an run a new build to:

`https://resources.staging.usahello.org`.

Once that has been tested and is OK, then you create another pull request from `develop` into `master`.

`https://resources.usahello.org`

## Django API deployment

### CircleCI config

You can see how CircleCI is configured in the `.circleci/config.yml` file in the repo:

[https://bitbucket.org/findhello/find-hello-api/src/master/.circleci/](https://bitbucket.org/findhello/find-hello-api/src/master/.circleci/)

### Fabric config

There is a second configuration file `.circleci/deploy.py`. This is the [Fabric](http://www.fabfile.org/) deployment script.

Fabric is a Python library that helps make deployments over SSH. In our case, once our CircleCI has created the build, it then runs the Fabric script which in-turn connects to our server and carries out a number of commands to pull the latest code.

A few things to be aware of:

- For CircleCi to run our Fabric deployment script (and therefore connect to our server) it needs to have a public/private key configured. This is currently set up. You will see a `root@circleci` public key within `authorized_keys` on both the production and staging server
- CircleCI needs environment variables to successfully build the API. These are configured via the CircleCI interface
- CircleCI needs to be able to pull the git repo from Bitbucket using password-less SSH access. This is configured via deployment hooks in the Bitbucket and CircleCI interfaces

### Manual deployment

If the builds are not working, you'll need to connect to the server over SSH to see what the issue is.

These are the commands needed to do an equivilant deploy manually on the server:

```sh
cd find_hello_api
workon find_hello
git pull origin develop
pip install -r requirements/production.txt
python manage.py collectstatic --noinput
python manage.py migrate
supervisorctl restart find_hello
```

## Ionic app deployment



This is a little bit more straightfoward than the API. CircleCI will automatically create new builds when the `develop` branch changes. Once CircleCI builds the application it then uses `scp` to transfer the assets files to our production or staging server where they are served directly by Nginx.

To deploy manually, follow the same process. Locally build the app with:

```sh
ionic build --prod --release
```

The built webapp will be placed in a `www` folder which can then be copied over to the production API AWS instance (both the API and Ionic app are hosted on the same instance).
