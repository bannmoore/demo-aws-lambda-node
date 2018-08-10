# Demo - Serverless

This repo is based on the tutorial [A Crash Course on Serverless APIs with Express and MongoDB](https://hackernoon.com/a-crash-course-on-serverless-apis-with-express-and-mongodb-77774f7730fe), found on HackerNoon.

## Setup

Install `serverless`:

```
npm install --global serverless
```

Set up AWS Credentials:

```
serverless config credentials \
  --provider aws \
  --key $MY_ACCESS_KEY_ID \
  --secret $MY_SECRET_ACCESS_KEY
```

### MongoDB

For this repo, you can create a free-tier cluster on MongoDB Cloud Atlas.

### Secrets

Create a `secrets.json` file in the root directory (use `secrets.example.json` as a template). You'll need to update the Mongo connection string to correspond to your cluster.

## Validate Offline

```
npm run offline
```

To test the endpoints:

```sh
# create a new note
curl -X POST \
  --header "Content-Type: application/json" \
  --data '{"title":"My Note","description":"Something something something"}' \
  http://localhost:3000/api/notes
```

```sh
# get all notes
curl http://localhost:3000/api/notes
```

## Deploy

```sh
serverless deploy
```

### Production

Change the value of `NODE_ENV` in `secrets.json` to deploy to `production`.

```sh
rm -rf ./node_modules && npm install --production
```

Comment out `plugins` in `serverless.yml`.

```sh
serverless deploy
```

Deployment will output service information, including its endpoints:

```
Serverless: Stack update finished...
Service Information
service: sls-express-mongodb
stage: production
region: eu-central-1
stack: sls-express-mongodb-production
api keys:
  None
endpoints:
  ANY - https://t5vmxre0d5.execute-api.eu-central-1.amazonaws.com/production/
  ANY - https://t5vmxre0d5.execute-api.eu-central-1.amazonaws.com/production/{proxy+}
functions:
  app: sls-express-mongodb-production-app
```

### Loadtest

Install `loadtest`:

```sh
npm i -g loadtest
```

To loadtest the GET response 100 times with 10 different users:

```sh
loadtest -n 100 -c 10 https://t5vmxre0d5.execute-api.eu-central-1.amazonaws.com/production/api/notes
```

The output will tell you how the test went.

```
[Fri Aug 10 2018 08:30:48 GMT-0400 (EDT)] INFO Requests: 0 (0%), requests per second:0, mean latency: 0 ms
[Fri Aug 10 2018 08:30:53 GMT-0400 (EDT)] INFO Requests: 16 (16%), requests per second: 3, mean latency: 2076.9 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Target URL:          https://t5vmxre0d5.execute-api.eu-central-1.amazonaws.com/production/api/notes
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Max requests:        100
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Concurrency level:   10
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Agent:               none
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Completed requests:  100
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Total errors:        0
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Total time:          8.3605757 s
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Requests per second: 12
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Mean latency:        818.8 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO Percentage of the requests served within a certain time
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO   50%      334 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO   90%      3053 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO   95%      4648 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO   99%      6438 ms
[Fri Aug 10 2018 08:30:56 GMT-0400 (EDT)] INFO  100%      6438 ms (longest request)
```

## Teardown

```sh
serverless remove
```

