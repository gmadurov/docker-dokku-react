# Docker, Dokku and React 

This project is an example of how to dockerize a react application and to deploy it using Dokku.



## Changing the files

Once you have made an application (or have yet to start one) you need to add the following files 

```
app/
   .. all the react files 
   Dockerfile
   docker-compose.yml
```

### package.json 
in the scripts objects you want to add the following 2 commands:

```
"scripts": {
    ...
    "predeploy": "npm run build",
    "deploy": "serve -s build",
    ...
  }
```

adding pre... make it so that every time that you call deploy, pre-deploy also gets run 

You also want to run:

```
npm install serve 
```

normally you would add a `-g` flag that would install the app without adding it to the package.json file. However, we want to make sure that it gets installed here because we need it in the docker container.


### Dockerfile

```
FROM node
RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

COPY package.json /usr/src/app/

RUN npm install

COPY . /usr/src/app

EXPOSE 3000

CMD [ "npm", "run", "deploy" ]
```

### docker-compose.yml

```
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/usr/src/app
    environment:
      - NODE_ENV=development
    command: npm run deploy
```

### In the Terminal 
These next commands will need to be run on your server and in your terminal. 

```
# on server
dokku apps:create frontend
dokku domains:set frontend URL
```


```
# in your terminal
git remote add production IP_ADDRESS:frontend 
```

```
# on server
dokku builder-dockerfile:set frontend dockerfile-path Dockerfile
dokku config:set frontend DOKKU_PROXY_PORT_MAP=http:80:3000
```

```
# in your terminal
git push production
```

that should be it! now go to the url of the project that is outputted and it should be live

### Development 

you can either use the `docker-compose up` command or just use `npm start`

Hope this helps!!!