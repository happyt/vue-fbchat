# vue-fbchat

> Vue fb chat with fb database (older version)

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report

yarn add vue-toasted
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

material icons are included in build

to update several,
const payload = {}
payload['users/abcdefgh/nickname'] = 'john'
payload['messages/123456789/nickname'] = 'john'
payload['messages/123456790/nickname'] = 'john'
payload['messages/123456791/nickname'] = 'john'
database.ref().update(payload)

