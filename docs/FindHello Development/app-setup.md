# App Setup

The app requires node and `ionic-cli` to run. The easiest way [to install Node is with `nvm` (Node version manager)](https://github.com/creationix/nvm). This allows you to install different versions of Node.

Once nvm is installed, install Node v8.15.0 (this is the version tested with the repo)

```sh
nvm install 12.10.0
```

We then need to install Ionic and Cordova globally:

```sh
npm install -g @ionic/cli
```

(being aware of the versions of each) and then install all dependencies:

```sh
npm install
```

You then need to edit a `environment.ts` file in the `src/environments` of the repo with some environment variables:

```sh
API_URL="http://localhost:8000"
GOOGLE_MAPS_API_KEY (lookup in Google cloud account)
```

Note that in the above file, the API is running on the local machine. You can change this
value to `https://resources.staging.therefugeecenter.org` if you want your local app instance 
to use the staging server instead. Furthremore, you will need to update this value when you 
are building, running or emulating the app as a native mobile app. 

Finally you can run the web app locally with:

```sh
ionic serve
```
