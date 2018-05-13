# [Build-a-GraphQL-Server](https://egghead.io/courses/build-a-graphql-server)

* [x] 1.  Create a GraphQL Schema
* [x] 2.  Use GraphQL Primitive Types
* [x] 3.  Use GraphQL's Object Type for Basic Types
* [x] 4.  Use GraphQL's List Type for Collections
* [x] 5.  Serve a GraphQL Schema as Middleware in Express
* [x] 6.  Write a GraphQL Schema in JavaScript
* [x] 7.  Use Arguments in a GraphQL Query
* [x] 8.  Use GraphQLNonNull for Required Fields
* [x] 9.  Use GraphQLList with GraphQLObject Types
* [x] 10. Write a GraphQL Mutation
* [x] 11. Create an Input Object Type for Complex Mutations
* [x] 12. Add an Interface to a GraphQL Schema
* [ ] 13. Add a Relay Node Interface to a GraphQL Schema
* [ ] 14. Convert GraphQL List Type to a Relay Connection Type
* [ ] 15. Use Relay's Input Object Mutations

## 1. Create a GraphQL Schema

> In this video, we’ll take a look at the GraphQL Language and write out our first GraphQL Schema. We’ll use the graphql package available to us through npm to parse our graphql language file and resolve our initial query.

* `index.js`

```js
'use strict';

const { graphql, buildSchema } = require('graphql');

const schema = buildSchema(`
type Query {
  foo: String
}

type Schema {
  query: Query
}
`);

const resolvers = {
  foo: () => 'bar',
};

const query = `
query myFirstQuery {
  foo
}
`;

graphql(schema, query, resolvers)
  .then(result => console.log(result))
  .catch(error => console.log(error));
```

```shell
$ node index.js
{ data: { foo: 'bar' }
```

> What we've actually created here is a description of the capabilities of our GraphQL server. We've created a type called Schema, which is a definition of queries, mutations, and some descriptions. In this case, we're just going to be calling the query field. We've also created the Query type, which defines a field called foo, which is of type String.

> What this will allow us to do is to query our Schema for foo, and it will give back a value of a String. The way that GraphQL knows how to return a value or what value to return, is through the idea of something called a resolver

## 2. Use GraphQL Primitive Types

> GraphQL’s Type System has support for a number of Primitive Types that we can use in our GraphQL Schema definitions. In this video, we’ll take a look at String, Boolean, Int, Float, and ID and how to incorporate them into a GraphQL Schema Definition.

* `index.js`

```diff
'use strict';

const { graphql, buildSchema } = require('graphql');

const schema = buildSchema(`
type Query {
-  foo: String
+  id: ID,
+  title: String,
+  duration: Int,
+  watched: Boolean
}

type Schema {
  query: Query
}
`);

const resolvers = {
-  foo: () => 'bar',
+  id: () => '1',
+  title: () => 'bar',
+  duration: () => 180,
+  watched: () => true,
};

const query = `
query myFirstQuery {
-  foo
+  id
+  title
+  duration
+  watched
}
`;

graphql(schema, query, resolvers)
  .then(result => console.log(result))
  .catch(error => console.log(error));
```

```shell
$ node index.js
{ data: { id: '1', title: 'bar', duration: 180, watched: true } }
```

## 3. Use GraphQL's Object Type for Basic Types

> We can create the most basic components of our GraphQL Schema using GraphQL's Object Types. These types allow us to group related fields together under a specific type, such as a Video or a User, and then allows us to fetch these types when we query our schema. In this video, we'll learn how to write GraphQL Object Types in GraphQL's Schema language, as well as how to create resolvers for them, and ultimately how to query them.

* `index.js`

```diff
'use strict';

const { graphql, buildSchema } = require('graphql');

const schema = buildSchema(`
+type Video {
+  id: ID,
+  title: String,
+  duration: Int,
+  watched: Boolean
+}

type Query {
-  id: ID,
-  title: String,
-  duration: Int,
-  watched: Boolean
+  video: Video
}

type Schema {
  query: Query
}
`);

const resolvers = {
-  id: () => '1',
-  title: () => 'foo',
-  duration: () => 180,
-  watched: () => true,
+  video: () => ({
+    id: '1',
+    title: 'Foo',
+    duration: 180,
+    watched: true,
+  }),
};

const query = `
query myFirstQuery {
-  id
-  title
-  duration
-  watched
+  video {
+    id,
+    title,
+    duration,
+    watched
+  }
}
`;

graphql(schema, query, resolvers)
  .then(result => console.log(result))
  .catch(error => console.log(error));
```

```shell
$ node index.js
{ data: { video: { id: '1', title: 'Foo', duration: 180, watched: true } } }
```

## 4. Use GraphQL's List Type for Collections

> In order to handle collections of items in a GraphQL Schema, GraphQL has a List Type. In this video, we’ll learn the syntax for specifying a List of items in a GraphQL Schema.

* `index.js`

```diff
'use strict';

const { graphql, buildSchema } = require('graphql');

const schema = buildSchema(`
type Video {
  id: ID,
  title: String,
  duration: Int,
  watched: Boolean
}

type Query {
  video: Video
+  videos: [Video]
}

type Schema {
  query: Query
}
`);

+const videoA = {
+  id: 'a',
+  title: 'Create a GraphQL Schema',
+  duration: 120,
+  watched: true,
+};

+const videoB = {
+  id: 'b',
+  title: 'Ember.js CLI',
+  duration: 240,
+  watched: false,
+};

+const videos = [videoA, videoB];

const resolvers = {
  video: () => ({
    id: '1',
    title: 'Foo',
    duration: 180,
    watched: true,
  }),
+  videos: () => videos,
};

const query = `
query myFirstQuery {
-  video {
+  videos {
    id,
    title,
    duration,
    watched
  }
}
`;

graphql(schema, query, resolvers)
  .then(result => console.log(result))
  .catch(error => console.log(error));
```

```shell
$ node index.js
{ data: { videos: [ [Object], [Object] ] } }
```

## 5. Serve a GraphQL Schema as Middleware in Express

> If we have a GraphQL Schema expressed in terms of JavaScript, then we have a convenient package available to us that let’s us easily serve up our schema on any endpoint in an Express Server. In this video, we’ll use the express-graphql package to serve up our GraphQL Schema as middleware, and also learn how to enable the GraphiQL tool in order to query our GraphQL Schema.

```shell
npm install --save express express-graphql
```

* `index.js`

```diff
'use strict';

+const express = require('express');
+const graphqlHTTP = require('express-graphql');
const { graphql, buildSchema } = require('graphql');

+const PORT = process.env.PORT || 3000;
+const server = express();

const schema = buildSchema(`
type Video {
  id: ID,
  title: String,
  duration: Int,
  watched: Boolean
}

type Query {
  video: Video
  videos: [Video]
}

type Schema {
  query: Query
}
`);

const videoA = {
  id: 'a',
  title: 'Create a GraphQL Schema',
  duration: 120,
  watched: true,
};

const videoB = {
  id: 'b',
  title: 'Ember.js CLI',
  duration: 240,
  watched: false,
};

const videos = [videoA, videoB];

const resolvers = {
  video: () => ({
    id: '1',
    title: 'Foo',
    duration: 180,
    watched: true,
  }),
  videos: () => videos,
};

-const query = `
-query myFirstQuery {
-  video {
-  videos {
-    id,
-    title,
-    duration,
-    watched
-  }
-}
-`;

-graphql(schema, query, resolvers)
-  .then(result => console.log(result))
-  .catch(error => console.log(error));

+server.use(
+  '/graphql',
+  graphqlHTTP({
+    schema,
+    graphiql: true,
+    rootValue: resolvers,
+  })
+);

+server.listen(PORT, () => {
+  console.log(`Listening on http://localhost:${PORT}`);
+});
```

```
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and query as

```
{
  videos {
    title
  }
}
```

Then, response as

```json
{
  "data": {
    "videos": [
      {
        "title": "Create a GraphQL Schema"
      },
      {
        "title": "Ember.js CLI"
      }
    ]
  }
}
```

## 6. Write a GraphQL Schema in JavaScript

> Writing out a GraphQL Schema in the common GraphQL Language can work for simple GraphQL Schemas, but as our application grows, or when we start using more complex types like interfaces or unions, we find that we can’t use a GraphQL Language file in the same way as before. In this video, we’ll learn how to translate a GraphQL Schema written in GraphQL into a GraphQL Schema written in JavaScript.

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
-const { graphql, buildSchema } = require('graphql');
+const {
+  GraphQLSchema,
+  GraphQLObjectType,
+  GraphQLString,
+  GraphQLID,
+  GraphQLInt,
+  GraphQLBoolean,
+} = require('graphql');

const PORT = process.env.PORT || 3000;
const server = express();

-const schema = buildSchema(`
-type Video {
-  id: ID,
-  title: String,
-  duration: Int,
-  watched: Boolean
-}

-type Query {
-  video: Video
-  videos: [Video]
-}

-type Schema {
-  query: Query
-}
-`);

+const videoType = new GraphQLObjectType({
+  name: 'Video',
+  description: 'A video on Egghead.io',
+  fields: {
+    id: {
+      type: GraphQLID,
+      description: 'The id of the video.',
+    },
+    title: {
+      type: GraphQLString,
+      description: 'The title of the video.',
+    },
+    duration: {
+      type: GraphQLInt,
+      description: 'The duration of the video (in seconds)',
+    },
+    watched: {
+      type: GraphQLBoolean,
+      description: 'Whether or not the viewer has watched the video.',
+    },
+  },
+});

+const queryType = new GraphQLObjectType({
+  name: 'QueryType',
+  description: 'The root query type.',
+  fields: {
+    video: {
+      type: videoType,
+      resolve: () =>
+        new Promise(resolve => {
+          resolve({
+            id: 'a',
+            title: 'GraphQL',
+            duration: 180,
+            watched: false,
+          });
+        }),
+    },
+  },
+});

+const schema = new GraphQLSchema({
+  query: queryType,
+});

const videoA = {
  id: 'a',
  title: 'Create a GraphQL Schema',
  duration: 120,
  watched: true,
};

const videoB = {
  id: 'b',
  title: 'Ember.js CLI',
  duration: 240,
  watched: false,
};

const videos = [videoA, videoB];

-const resolvers = {
-  video: () => ({
-    id: '1',
-    title: 'Foo',
-    duration: 180,
-    watched: true,
-  }),
-  videos: () => videos,
-};
-

server.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
-   rootValue: resolvers,
  })
);

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and query as

```
{
  video {
		id
    title
    duration
    watched
  }
}
```

Then, response as

```json
{
  "data": {
    "video": {
      "id": "a",
      "title": "GraphQL",
      "duration": 180,
      "watched": false
    }
  }
}
```

## 7. Use Arguments in a GraphQL Query

> In GraphQL, every field and nested object is able to take in arguments of varying types in order to do common operations like fetching an object by it's ID, filtering, sorting, and more. In this video, we'll update a field to take in an id argument and then learn how to use that argument in our resolve method to fetch a video by its id.

* `src/data/index.js`

```js
const videoA = {
  id: 'a',
  title: 'Create a GraphQL Schema',
  duration: 120,
  watched: true,
};

const videoB = {
  id: 'b',
  title: 'Ember.js CLI',
  duration: 240,
  watched: false,
};

const videos = [videoA, videoB];
const getVideoById = id =>
  new Promise(resolve => {
    const [video] = videos.filter(video => video.id === id);
    resolve(video);
  });

exports.getVideoById = getVideoById;
```

* `index.js`

````diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLString,
  GraphQLID,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
+const { getVideoById } = require('./src/data');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
      type: GraphQLID,
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds)',
    },
    watched: {
      type: GraphQLBoolean,
      description: 'Whether or not the viewer has watched the video.',
    },
  },
});

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
    video: {
      type: videoType,
      args: {
        id: {
          type: GraphQLID,
          description: 'The id of the video.',
        },
      },
-      resolve: () =>
-        new Promise(resolve => {
-          resolve({
-            id: 'a',
-            title: 'GraphQL',
-            duration: 180,
-            watched: false,
-          });
-        }),
+     resolve: (_, args) => getVideoById(args.id),
    },
  },
});

const schema = new GraphQLSchema({
  query: queryType,
});

-const videoA = {
-  id: 'a',
-  title: 'Create a GraphQL Schema',
-  duration: 120,
-  watched: true,
-};

-const videoB = {
-  id: 'b',
-  title: 'Ember.js CLI',
-  duration: 240,
-  watched: false,
-};

-const videos = [videoA, videoB];

server.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  })
);

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});```
````

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and query as

```
{
  video(id: "a") {
		title
  }
}
```

Then, response as

```json
{
  "data": {
    "video": {
      "title": "Create a GraphQL Schema"
    }
  }
}
```

## 8. Use GraphQLNonNull for Required Fields

> While certain fields in a GraphQL Schema can be optional, there are some fields or arguments that are necessary in order to either fulfill a query, or to provide a guarantee to people using the Schema that some field exists. In this video, we'll take a look at turning an argument in a NonNull argument by applying the GraphQLNonNull type in order to guarantee that the given argument is supplied in the query.

* `index.js`

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
  GraphQLObjectType,
+ GraphQLNonNull,
  GraphQLString,
  GraphQLID,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
const { getVideoById } = require('./src/data');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
      type: GraphQLID,
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds)',
    },
    watched: {
      type: GraphQLBoolean,
      description: 'Whether or not the viewer has watched the video.',
    },
  },
});

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
    video: {
      type: videoType,
      args: {
        id: {
-         type: GraphQLID,
+         type: new GraphQLNonNull(GraphQLID),
          description: 'The id of the video.',
        },
      },
      resolve: (_, args) => getVideoById(args.id),
    },
  },
});

const schema = new GraphQLSchema({
  query: queryType,
});

server.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  })
);

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and query as

```
{
  video {
		title
  }
}
```

Then, response as

```json
{
  "errors": [
    {
      "message": "Field \"video\" argument \"id\" of type \"ID!\" is required but not provided.",
      "locations": [
        {
          "line": 31,
          "column": 3
        }
      ]
    }
  ]
}
```

## 9. Use GraphQLList with GraphQLObject Types

> When working with collections of things in GraphQL, we'll always reach out for the GraphQLList Type. In this video, we'll learn how to use GraphQLList from the graphql package in combination with a GraphQLObject Type to create a field that returns a collection in our Schema.

* `src/data/index.js`

```diff
const videoA = {
  id: 'a',
  title: 'Create a GraphQL Schema',
  duration: 120,
  watched: true,
};

const videoB = {
  id: 'b',
  title: 'Ember.js CLI',
  duration: 240,
  watched: false,
};

const videos = [videoA, videoB];
const getVideoById = id =>
  new Promise(resolve => {
    const [video] = videos.filter(video => video.id === id);
    resolve(video);
  });

+const getVideos = () => new Promise(resolve => resolve(videos));

exports.getVideoById = getVideoById;
+exports.getVideos = getVideos;
```

* `index.js`

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
+ GraphQLObjectType,
  GraphQLList,
  GraphQLNonNull,
  GraphQLString,
  GraphQLID,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
const { getVideoById, getVideos } = require('./src/data');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
      type: GraphQLID,
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds)',
    },
    watched: {
      type: GraphQLBoolean,
      description: 'Whether or not the viewer has watched the video.',
    },
  },
});

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
+    videos: {
+      type: new GraphQLList(videoType),
+      resolve: getVideos,
+    },
    video: {
      type: videoType,
      args: {
        id: {
          type: new GraphQLNonNull(GraphQLID),
          description: 'The id of the video.',
        },
      },
      resolve: (_, args) => getVideoById(args.id),
    },
  },
});

const schema = new GraphQLSchema({
  query: queryType,
});

server.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  })
);

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and query as

```
{
  videos {
		title
  }
}
```

Then, response as

```json
{
  "data": {
    "videos": [
      {
        "title": "Create a GraphQL Schema"
      },
      {
        "title": "Ember.js CLI"
      }
    ]
  }
}
```

## 10. Write a GraphQL Mutation

> In order to change the data that we can query for in a GraphQL Schema, we have to define what is called a mutation in GraphQL. Mutations allow us to specify ways to create, update, and delete data. It also provides a way to fetch information after the mutation occurs. In this video, we’ll learn how to create a Mutation Type in GraphQL and specify the information we want returned.

* `src/data/index.js`

```diff
const videoA = {
  id: 'a',
  title: 'Create a GraphQL Schema',
  duration: 120,
  watched: true,
};

const videoB = {
  id: 'b',
  title: 'Ember.js CLI',
  duration: 240,
  watched: false,
};

const videos = [videoA, videoB];
const getVideoById = id =>
  new Promise(resolve => {
    const [video] = videos.filter(video => video.id === id);
    resolve(video);
  });

const getVideos = () => new Promise(resolve => resolve(videos));

+const createVideo = ({ title, duration, released }) => {
+  const video = {
+    id: new Buffer(title, 'utf-8').toString('base64'),
+    title,
+    duration,
+    released,
+  };

+  videos.push(video);

+  return videos;
+};

exports.getVideoById = getVideoById;
exports.getVideos = getVideos;
+exports.createVideo = createVideo;
```

* `index.js`

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLList,
  GraphQLNonNull,
  GraphQLString,
  GraphQLID,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
-const { getVideoById, getVideos } = require('./src/data');
+const { getVideoById, getVideos, createVideo } = require('./src/data');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
      type: GraphQLID,
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds)',
    },
    watched: {
      type: GraphQLBoolean,
      description: 'Whether or not the viewer has watched the video.',
    },
  },
});

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
    videos: {
      type: new GraphQLList(videoType),
      resolve: getVideos,
    },
    video: {
      type: videoType,
      args: {
        id: {
          type: new GraphQLNonNull(GraphQLID),
          description: 'The id of the video.',
        },
      },
      resolve: (_, args) => getVideoById(args.id),
    },
  },
});

+const mutationType = new GraphQLObjectType({
+  name: 'Mutation',
+  description: 'The root Mutation type.',
+  fields: {
+    createVideo: {
+      type: videoType,
+      args: {
+        title: {
+          type: new GraphQLNonNull(GraphQLString),
+          description: 'The title of the video.',
+        },
+        duration: {
+          type: new GraphQLNonNull(GraphQLInt),
+          description: 'The duration of the video (in seconds)',
+        },
+        released: {
+          type: new GraphQLNonNull(GraphQLBoolean),
+          description: 'Whether or not the video is released',
+        },
+      },
+      resolve: (_, args) => createVideo(args),
+    },
+  },
+});

const schema = new GraphQLSchema({
  query: queryType,
+ mutation: mutationType,
});

server.use(
  '/graphql',
  graphqlHTTP({
    schema,
    graphiql: true,
  })
);

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and mutate as

```
mutation M {
  createVideo(title: "Foo", duration: 300, released: false) {
    id,
    title
  }
}
```

Then, response as

```json
{
  "data": {
    "createVideo": {
      "id": "Rm9v",
      "title": "Foo"
    }
  }
}
```

## 11. Create an Input Object Type for Complex Mutations

> When we have certain mutations that require more complex input parameters, we can leverage the Input Object Type in GraphQL. In this video, we’ll learn how to create an Input Object Type and how to add it to a GraphQL Mutation Type.

- `index.js`

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLInputObjectType,
  GraphQLList,
  GraphQLNonNull,
  GraphQLID,
  GraphQLString,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
const { getVideoById, getVideos, createVideo } = require('./src/data');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
      type: GraphQLID,
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds).',
    },
    released: {
      type: GraphQLBoolean,
      description: 'Whether or not the video has been released.',
    },
  },
});

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
    videos: {
      type: new GraphQLList(videoType),
      resolve: getVideos,
    },
    video: {
      type: videoType,
      args: {
        id: {
          type: new GraphQLNonNull(GraphQLID),
          description: 'The id of the video.',
        },
      },
      resolve: (_, args) => {
        return getVideoById(args.id);
      },
    },
  },
});

+const videoInputType = new GraphQLInputObjectType({
+  name: 'VideoInput',
+  fields: {
+    title: {
+      type: new GraphQLNonNull(GraphQLString),
+      description: 'The title of the video.',
+    },
+    duration: {
+      type: new GraphQLNonNull(GraphQLInt),
+      description: 'The duration of the video (in seconds)',
+    },
+    released: {
+      type: new GraphQLNonNull(GraphQLBoolean),
+      description: 'Whether or not the video is released',
+    },
+  },
+});

const mutationType = new GraphQLObjectType({
  name: 'Mutation',
  description: 'The root Mutation type.',
  fields: {
    createVideo: {
      type: videoType,
      args: {
-       title: {
-         type: new GraphQLNonNull(GraphQLString),
-         description: 'The title of the video.',
-       },
-       duration: {
-         type: new GraphQLNonNull(GraphQLInt),
-         description: 'The duration of the video (in seconds)',
-       },
-       released: {
-         type: new GraphQLNonNull(GraphQLBoolean),
-         description: 'Whether or not the video is released',
-       },
+       video: {
+         type: new GraphQLNonNull(videoInputType),
+       }
      },
      resolve: (_, args) => {
        return createVideo(args);
      },
    },
  },
});

const schema = new GraphQLSchema({
  query: queryType,
  mutation: mutationType,
});

server.use('/graphql', graphqlHTTP({
  schema,
  graphiql: true,
}));

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

```shell
$ node index.js
```

Next, Go to `http://localhost:3000/graphql` and mutate as

```
mutation M {
  createVideo(video: {
    title: "Foo",
    duration: 300,
    released: false
  }) {
    id,
    title
  }
}
```

Then, response as

```json
{
  "data": {
    "createVideo": {
      "id": "Rm9v",
      "title": "Foo"
    }
  }
}
```

## 12. Add an Interface to a GraphQL Schema

> As we start building out more complex GraphQL schemas, certain fields start to repeat across different types. This is a perfect use-case for the Interface Type made available to us through GraphQL’s Type System. In this video, we’ll go over how to create an Interface Type and how to add it to an existing type in a GraphQL Schema.

- `src/node.js`

```js
'use strict';

const {
  GraphQLInterfaceType,
  GraphQLNonNull,
  GraphQLID,
} = require('graphql');

const { videoType } = require('../');

const nodeInterface = new GraphQLInterfaceType({
  name: 'Node',
  fields: {
    id: {
      type: new GraphQLNonNull(GraphQLID),
    },
  },
  resolveType: (object) => {
    if (object.title) {
      return videoType;
    }

    return null;
  },
});

module.exports = nodeInterface;
```

- `index.js`

```diff
'use strict';

const express = require('express');
const graphqlHTTP = require('express-graphql');
const {
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLInputObjectType,
  GraphQLList,
  GraphQLNonNull,
  GraphQLID,
  GraphQLString,
  GraphQLInt,
  GraphQLBoolean,
} = require('graphql');
const { getVideoById, getVideos, createVideo } = require('./src/data');
+const nodeInterface = require('./src/node');

const PORT = process.env.PORT || 3000;
const server = express();

const videoType = new GraphQLObjectType({
  name: 'Video',
  description: 'A video on Egghead.io',
  fields: {
    id: {
-      type: GraphQLID,
+      type: new GraphQLNonNull(GraphQLID),
      description: 'The id of the video.',
    },
    title: {
      type: GraphQLString,
      description: 'The title of the video.',
    },
    duration: {
      type: GraphQLInt,
      description: 'The duration of the video (in seconds).',
    },
    released: {
      type: GraphQLBoolean,
      description: 'Whether or not the video has been released.',
    },
  },
+  interfaces: [nodeInterface],
});
+exports.videoType = videoType;

const queryType = new GraphQLObjectType({
  name: 'QueryType',
  description: 'The root query type.',
  fields: {
    videos: {
      type: new GraphQLList(videoType),
      resolve: getVideos,
    },
    video: {
      type: videoType,
      args: {
        id: {
          type: new GraphQLNonNull(GraphQLID),
          description: 'The id of the video.',
        },
      },
      resolve: (_, args) => {
        return getVideoById(args.id);
      },
    },
  },
});

const videoInputType = new GraphQLInputObjectType({
  name: 'VideoInput',
  fields: {
    title: {
      type: new GraphQLNonNull(GraphQLString),
      description: 'The title of the video.',
    },
    duration: {
      type: new GraphQLNonNull(GraphQLInt),
      description: 'The duration of the video (in seconds)',
    },
    released: {
      type: new GraphQLNonNull(GraphQLBoolean),
      description: 'Whether or not the video is released',
    },
  },
});

const mutationType = new GraphQLObjectType({
  name: 'Mutation',
  description: 'The root Mutation type.',
  fields: {
    createVideo: {
      type: videoType,
      args: {
        video: {
          type: new GraphQLNonNull(videoInputType),
        }
      },
      resolve: (_, args) => {
        return createVideo(args);
      },
    },
  },
});

const schema = new GraphQLSchema({
  query: queryType,
  mutation: mutationType,
});

server.use('/graphql', graphqlHTTP({
  schema,
  graphiql: true,
}));

server.listen(PORT, () => {
  console.log(`Listening on http://localhost:${PORT}`);
});
```

## 13. Add a Relay Node Interface to a GraphQL Schema

> The GraphQL Relay Specification requires that a GraphQL Schema has some kind of mechanism for re-fetching an object. For typical Relay-compliant servers, this is going to be the Node Interface. In this video, we’ll add in the Node interface to a GraphQL Schema by using the helpers available in the graphql-relay npm package.
