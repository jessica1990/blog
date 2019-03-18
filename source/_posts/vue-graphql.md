---
title: vue项目引入graphql
date: 2019-03-18 16:00:06
tags: graphql
categories: graphql 
---
### 1. 引入graphql
在入口文件中引入graphql相关依赖

```
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'

import App from './index.vue'
import { ApolloClient } from 'apollo-client'
import { HttpLink } from 'apollo-link-http'
import { InMemoryCache } from 'apollo-cache-inmemory'
import VueApollo from 'vue-apollo'

const httpLink = new HttpLink({
  // You should use an absolute URL here
  uri: 'http://192.168.10.5:3000/admin/graphql'
})

// Create the apollo client
const apolloClient = new ApolloClient({
  link: httpLink,
  cache: new InMemoryCache(),
  connectToDevTools: true
})

const apolloProvider = new VueApollo({
  defaultClient: apolloClient
})

Vue.use(VueApollo)
Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  provide: apolloProvider.provide(),
  components: { App },
  template: '<App/>'
})

```

#### apollo-client
> Apollo Client is the best way to use GraphQL to build client applications. The client is designed to help you quickly build a UI that fetches data with GraphQL, and can be used with any JavaScript front-end.

#### apollo-link-http
> Get GraphQL results over a network using HTTP fetch.

#### apollo-cache-inmemory
> apollo-cache-inmemory is the default cache implementation for Apollo Client 2.0. InMemoryCache is a normalized data store that supports all of Apollo Client 1.0’s features without the dependency on Redux.

#### vue-apollo
> This library integrates apollo in your Vue components with declarative queries.


### 2. gql语句
定义**query**、**mutation**、**subscribtion**等语句以供请求使用

```
import gql from 'graphql-tag'

export const ASSETS = gql`
  query Assets($first: Int,  $after: String) {
    assets(first: $first, after: $after) {
      totalCount
      edges {
        node {
          id
          code
          label
          kind
        }
      }
      pageInfo {
        hasNextPage
      }
    }
  }
`

export const CREATE_ASSET = gql`
  mutation CreateAsset($code: String!, $label: String!, $kind: Int!) {
    createAsset (code: $code, label: $label, kind: $kind) {
      id
    }
  }
`
```

### 3. 在vue components中使用

```
// 引入定义好的gql语句
import { ASSETS, CREATE_ASSET } from './graphql'
...
created () {
  this.$apollo.query({ // 查询
    query: ASSETS
  }).then((res) => {
    console.log(res)
  }).catch(err => {
    console.log(err)
  })
},
methods: {
    submit () { // 新建
      this.$apollo.mutate({
        mutation: CREATE_ASSET,
        variables: {
          id: 'id',
          code: 'code',
          label: 'label'
        }
      }).then(res => {
        console.log(res)
      }).catch(err => {
        console.log(err)
      })
    }
  }
```
也可以在Vue.$apollo上进行相关的定义和使用，详情可参考[官网](https://vue-apollo.netlify.com/guide/apollo/queries.html#simple-query)
