# Get started

- [With Docker Compose](#with-docker-compose)
- [With Docker](#with-docker)
- [With Helm](#with-helm)
- [Without Docker](#without-docker)
- [With Heroku](#with-heroku)
- [With Render](#with-render)

## With Docker Compose

### 1. Create the configuration

Create a [`.env` file](https://www.npmjs.com/package/dotenv) in the root of this project to store all environment variables in one file.

```
ACKEE_USERNAME=username
ACKEE_PASSWORD=password
```

Only those two are required to run Ackee with the existing `docker-compose.yml`. Take a look at the [options](Options.md) for a detailed explanation.

### 2. Run Ackee

Run this command in the root of the project to use the predefined `docker-compose.yml`. It contains everything you need, including MongoDB and Ackee.

```sh
docker-compose up
```

> 💡 Add the `-d` flag to the Docker command to run the services in the background.

### 3. Open Ackee

Ackee will output the URL it's listening on once the server is running. Visit the URL with your browser and complete the finial steps using the interface.

### 4. Get Ackee online

Ackee now runs on port `3000` and is only accessible from you local network. It's recommended to use a reverse proxy in front of Ackee. The following guides will help you through this steps.

- [SSL and HTTPS](SSL%20and%20HTTPS.md)
- [CORS headers](CORS%20headers.md)

## With Docker

### 1. Setup MongoDB

Ackee requires a running MongoDB instance. The easiest way to install MongoDB is by using [Docker](https://www.docker.com). Skip this step if you have MongoDB installed or visit the [website of MongoDB](https://www.mongodb.com) for alternative setups.

```sh
docker run -p 27017:27017 --name mongo mongo
```

For persistent storage, mount a host directory to the container directory `/data/db`, which is identified as a potential mount point in the mongo Dockerfile. When starting a new container, Docker will use the volume of the previous container and copy it to the new container, ensuring that no data gets lost.

```sh
docker run -p 27017:27017 -v /path/to/local/folder:/data/db --name mongo mongo
```

> 💡 Add the `-d` flag to the Docker command to run MongoDB in the background.

Explanation:

- `-p` makes port `27017` available at port `27017` on the host
- `-v` mounts `/path/to/local/folder` to `/data/db` of the container
- `--name` sets the container name to `mongo`
- `mongo` is the name of the image

### 2. Run Ackee

```sh
docker run -p 3000:3000 -e ACKEE_MONGODB='mongodb://mongo:27017/ackee' -e ACKEE_USERNAME='username' -e ACKEE_PASSWORD='password' --link mongo --name ackee electerious/ackee
```

> 💡 Add the `-d` flag to the Docker command to run Ackee in the background.

Explanation:

- `-p` makes port `3000` available at port `3000` on the host
- `-e` sets [environment variables](Options.md) required by Ackee
- `--link` links Ackee with the `mongo` container
- `--name` sets the container name to `ackee`
- `electerious/ackee` is the name of the image

### 3. Open Ackee

Ackee will output the URL it's listening on once the server is running. Visit the URL with your browser and complete the finial steps using the interface.

### 4. Get Ackee online

Ackee now runs on port `3000` and is only accessible from you local network. It's recommended to use a reverse proxy in front of Ackee. The following guides will help you through this steps.

- [SSL and HTTPS](SSL%20and%20HTTPS.md)
- [CORS headers](CORS%20headers.md)

## With Helm

### 1. Create `values.yaml`

Based on the [default `values.yaml`](https://github.com/suda/ackee-chart/blob/master/values.yaml) create your own with overrides as needed.

### 2. Deploy with `helm`

```
helm repo add ackee https://raw.githubusercontent.com/suda/ackee-chart/master
helm repo update
helm install ackee-release ackee/ackee-chart -n ackee -f values.yaml
```

If you're using the `ingress-nginx`, enabling the ingress will set the necessary annotations to enable CORS.

## Without Docker

### 1. Install dependencies

Ackee dependents on …

- [Node.js](https://nodejs.org/en/) (v14 or newer)
- [yarn](https://yarnpkg.com/en/)
- [MongoDB](https://www.mongodb.com) (v4.0.6 or newer)

Make sure to install and update all dependencies before you continue. The installation instructions for the individual dependencies can be found on the linked websites.

### 2. Create the configuration

Configure Ackee using environment variables or create a [`.env` file](https://www.npmjs.com/package/dotenv) in the root of the project to store all variables in one file.

```
ACKEE_MONGODB=mongodb://localhost:27017/ackee
ACKEE_USERNAME=username
ACKEE_PASSWORD=password
```

Only those three are required to run Ackee. Take a look at the [options](Options.md) for a detailed explanation.

The [MongoDB connection string](https://docs.mongodb.com/manual/reference/connection-string/) is used to connect to your MongoDB. It should also contain the username and password of your MongoDB instance when required.

The username and password variables are used to secure your Ackee interface/API.

### 3. Install Ackee

Install all required dependencies.

```sh
yarn
```

### 4. Run Ackee

Ackee will output the URL it's listening on once the server is running. Visit the URL with your browser and complete the finial steps using the interface.

```sh
yarn start
```

### 5. Get Ackee online

Ackee now runs on port `3000` and is only accessible from you local network. It's recommended to use a reverse proxy in front of Ackee. The following guides will help you through this steps.

- [SSL and HTTPS](SSL%20and%20HTTPS.md)
- [CORS headers](CORS%20headers.md)

## With Heroku

### 1. Deploy to Heroku

Simply deploy to Heroku by clicking this button:

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/electerious/Ackee)

### 2. Configure Ackee

- You need to have a MongoDB instance running, either hosting it yourself or using a (paid) add-on like [ObjectRocket MongoDB](https://elements.heroku.com/addons/ormongo). This is as simple as typing `heroku addons:create ormongo:2-wt --app <YOUR_APP_NAME>` using the CLI, or using the web dashboard; more details at the [official documentation](https://devcenter.heroku.com/articles/managing-add-ons). You'll need to provide connection details to Ackee dyno, either from the web dashboard or via command line, e.g. `heroku config:add "ACKEE_MONGODB=mongodb://<host>:<port>/<db>"`

- Ensure that you're using the correct CORS headers by setting [`ACKEE_ALLOW_ORIGIN`](CORS%20headers.md#heroku-or-platforms-as-a-service-configuration).

### 3. Updating Ackee

The easiest way to update Ackee once hosted on Heroku is to clone the repo down, pull the latest changes from Ackee, and then push them back up to Heroku. You'll need the Heroku CLI and Git for this to work.

In your application view, you'll find instructions under the `Deploy` tab on how to clone down the project. It should look something like below:

```sh
heroku login
heroku git:clone -a <your ackee applicaton name>
```

You'll then want to add the Ackee repo as origin, pull the latest changes, and push it back up to Heroku.

```sh
git remote add origin https://github.com/electerious/Ackee.git
git pull origin master
git push heroku master
```

After your application re-deploys you'll have the latest version of Ackee!

## With Render

You can use [Render's](https://render.com/) one-click deploy button to get up and running with Ackee in minutes.

Click **Deploy to Render** below and follow the prompts to set up Ackee on Render.

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/render-examples/ackee)

Once your deploy has finished, you are ready to start using Ackee! Visit the URL for your service to login. You can get your login credentials from the `ACKEE_USERNAME` and `ACKEE_PASSWORD` environment variables in the **Environment** tab of your service. By default, your username will be `render` and your password will be a randomly generated string.
