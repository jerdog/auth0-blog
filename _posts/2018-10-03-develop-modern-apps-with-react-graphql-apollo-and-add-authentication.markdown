---
layout: post
title: "Developing and Securing Modern Apps with GraphQL, React, and Apollo"
metatitle: "Learn to Develop and Secure React Apps with GraphQL, Apollo, and Auth0"
description: "Learn how to build modern apps. Follow along to see how to build GraphQL API using resolvers and connect to SQLite database. React app front-end and authentication with Auth0."
metadescription: ""
date: 2018-10-03 8:30
category: Technical Guide, Stack, Modern Apps
author:
  name: "Prosper Otemuyiwa"
  url: "https://twitter.com/unicodeveloper"
  mail: "prosper.otemuyiwa@auth0.com"
  avatar: "https://en.gravatar.com/avatar/1097492785caf9ffeebffeb624202d8f?s=200"
design:
  bg_color: "#222228"
  image: "https://cdn.auth0.com/blog/graphql-auth0/logo.png"
tags:
- graphql
- auth
- react
- apollo
- authentication
- javascript
- modern-apps
- api
related:
- 2018-08-14-react-context-api-managing-state-with-ease
- 2018-08-28-react-tutorial-building-and-securing-your-first-app
- 2018-09-20-a-scout-approach-to-software-development
---

**TL;DR:** Modern applications are increasingly data driven, which leads to a lot of time spent fetching, filtering, and aggregating data. When implemented with the [Apollo platform](https://www.apollographql.com/docs/fundamentals/platform.html), GraphQL reduces the complexity associated with data fetching and enables developers to take advantage of incredible tooling for crafting flexible APIs. In this tutorial, we'll explore how to build a production-ready application with GraphQL, React and Apollo. Finally, we'll wrap it up by learning how to add authentication with Auth0. The [completed code is available on GitHub](https://github.com/auth0-blog/book-app).

{% include tweet_quote.html quote_text="Learn how to develop and secure modern applications with GraphQL, React and Auth0." %}

---

## Introduction to GraphQL

GraphQL is a specification for client-server communication. It enables the client to express its data requirements declaratively and the server to send back only what the client requests.

A GraphQL server exposes a schema that describes its API including queries to fetch data and mutations to modify data. This allows clients to specify their data requirements with queries and send it to one GraphQL endpoint, instead of collecting information from multiple endpoints as is typical with REST. A GraphQL schema is strongly typed, which unlocks great developer tooling.

Apollo is an implementation of GraphQL that we'll be using in today's tutorial to help us get up and running quickly. It includes two runtime libraries, Apollo Server and Client, for building and querying your API's schema with GraphQL. It also features developer tooling that integrates with your existing workflow and gives you full visibility into the performance and security of your API.

## What We'll Build

We'll build an application called **CoolReads**. **CoolReads** is a popular web application where users can actually review the books that they have read over time. These reviews help new users determine whether to embark on reading a book or not.

**CoolReads** is an application that displays a list of books with the appropriate book cover image, title, and average user ratings. Everyone should be able to see the ratings of these books, but only an authenticated user can add a new book with ratings. We'll assume there is a poll of user votes where the authenticated users just get the ratings and add to the platform.

We'll make use of GraphQL to build the API for the application. GraphQL’s strongly typed query language enables developers to take advantage of incredible tooling for exploring GraphQL APIs. Developers can query a GraphQL schema for information about what queries and types it supports because of the built-in introspection system.

## Prerequisites

Before building the GraphQL API, make sure you have the following installed :

* **Node.js and npm**: At least [Node.js v8.0 and npm v5](https://nodejs.org/en/download).
* **nodemon**: Ensure that you have [nodemon](https://www.npmjs.com/package/nodemon) installed globally.

After that, create a directory and run `npm init --y` to create a `package.json` file. Then install the [esm](https://www.npmjs.com/package/esm) package. This package allows us to use ES6 style imports and exports at will.

## Building CoolReads GraphQL API

### Schema First Development

Schema first development is a recommended approach for building applications with GraphQL that involves the frontend and backend teams agreeing on a schema first, which serves as a contract between the UI and the backend before any API development commences. GraphQL schemas are at their best when they are designed around the needs of client applications. We'll take this approach in designing a schema for our API.

### Building CoolReads Schema

At the core of any GraphQL server is a schema. The schema defines types and their relationships. It also specifies which queries can be made against the server. Let's build out the schema for **CoolReads**.

Create an `src/server.js`file in the _api_ directory. Our schema will live here.

The Schema for our application will look like this:


```bash
 const typeDefs = gql`
    type Author {
      id: Int!
      first_name: String!
      last_name: String!
      books: [Book]!
    }

    type Book {
      id: Int!
      title: String!
      cover_image_url: String!
      average_rating: Float!
      author: Author!
    }

    type Query {
      books: [Book!]!,
      book(id: Int!): Book!

    }

    type Mutation {
      addBook(title: String!, cover_image_url: String!, average_rating: Float!, authorId: Int!): Book!
    }
  `;
```

The `typeDefs` string defines our schema in the [GraphQL Schema Definition Language](https://www.apollographql.com/docs/apollo-server/v2/essentials/schema.html#sdl).

We have the `Author`, `Book`, `Query` and `Mutation` type. The `Query` type informs the server about the queries. that users are allowed to make. The `gql` tag is a JavaScript template literal tag that enables syntax highlighting for our schema.

The users of our app should be able to see all the books available at once. This is where the `books` query becomes useful. If we want to retrieve all books, we'll write a query like this:

```bash

query {
 books {
    id
    title
    cover_image_url
    author {
      first_name
      last_name
    }
  }
}

```

This query will return a GraphQL response like this:

```bash

{
  "data": {
    "books": [
      {
        "id": 1,
        "title": "The trials of Brother Jero",
        "cover_image_url": "https://pmcvariety.files.wordpress.com/2017/03/baywatch-reboot.jpg",
        "author": {
          "first_name": "Wole",
          "last_name": "Soyinka"
        }
      },
      {
        "id": 2,
        "title": "Half of a Yellow Sun",
        "cover_image_url": "https://resizing.flixster.com/PTWb8cveEQtMkpDSq3ypCZBuGoU=/300x300/v1.bjsxNTE4NzE4O2o7MTczODc7MTIwMDszMDAwOzIwMDA",
        "author": {
          "first_name": "Chimamanda",
          "last_name": "Adichie"
        }
      },
      {
        "id": 3,
        "title": "Americanah",
        "cover_image_url": "http://t1.gstatic.com/images?q=tbn:ANd9GcQcCAOmt-FsRsR8GebIzI67qSvdQ2JLYDRLxeAcbH-541fzqq1H",
        "author": {
          "first_name": "Chimamanda",
          "last_name": "Adichie"
        }
      },
      {
        "id": 4,
        "title": "King Baabu ",
        "cover_image_url": "http://t1.gstatic.com/images?q=tbn:ANd9GcQcCAOmt-FsRsR8GebIzI67qSvdQ2JLYDRLxeAcbH-541fzqq1H",
        "author": {
          "first_name": "Wole",
          "last_name": "Soyinka"
        }
      }
    ]
  }
}
```

For the query to return this response, a couple of things such as resolvers, a form of data source (database or external API), and a GraphQL server to execute the schema need to be available.

Resolvers provide the instructions for turning a GraphQL operation into data. They are organized into a one to one mapping to the fields in a GraphQL schema. Without further ado, let's add an in-app memory data source!


{% include tweet_quote.html quote_text="Resolvers provide the instructions for turning a GraphQL operation into data." %}


```bash

const books = [
    { id: 1, title: 'The Trials of Brother Jero',  cover_image_url: 'ssdsds', average_rating: 8, authorId: 1 },
    { id: 2, title: 'Half of a Yellow Sun',  cover_image_url: 'dsdsds', average_rating: 9, authorId: 3 },
    { id: 3, title: 'Americanah',  cover_image_url: 'dsdsds', average_rating: 9, authorId: 3 },
    { id: 4, title: 'King Baabu',  cover_image_url: 'sdsds', average_rating: 7, authorId: 1 },
    { id: 5, title: 'Children of Blood and Bone',  cover_image_url: 'sdsds', average_rating: 7, authorId: 2 },
  ];

  const authors = [
    { id: 1, first_name: 'Wole', last_name: 'Soyinka' },
    { id: 2, first_name: 'Tomi', last_name: 'Adeyemi' },
    { id: 3, first_name: 'Chimamanda', last_name: 'Adichie' },
  ];

 const typeDefs = gql`
    type Author {
      id: Int!
      first_name: String!
      last_name: String!
      books: [Book]!
    }

    type Book {
      id: Int!
      title: String!
      cover_image_url: String!
      average_rating: Float!
      author: Author!
    }

    type Query {
      books: [Book!]!,
      book: Book!
      author(id: Int!): Author!
    }

    type Mutation {
      addBook(title: String!, cover_image_url: String!, average_rating: Float!, authorId: Int!): Book!
    }
  `;
```

In the code above, we have the `books` and `authors` data source. We'll go ahead to write the resolvers that fetch data from the data source above before progressing to hooking up our GraphQL API with a proper database.

### Writing the Resolvers

Resolvers are functions that connect schema fields and types to various backends. As I mentioned earlier, they provide the instructions for turning a GraphQL operation into data. They can retrieve or write data from anywhere, including a SQL, a No-SQL, graph database, a micro-service or a REST API. Resolvers can also return strings, ints, null, and other primitives.

```js
const { find, filter } = require('lodash');
...
...

let book_id = 5;
let author_id = 3;

const resolvers = {
  Query: {
    books: () => books,
    book: (_, { id }) => find(books, { id: id }),
    author: (_, { id }) => find(authors, { id: id }),
  },
  Mutation: {
   addBook: (_, {title, cover_image_url, average_rating, authorId }) => {
      book_id++;

      const newBook = {
        id: book_id,
        title,
        cover_image_url,
        average_rating,
        author_id
      };

      books.push(newBook);
      return newBook;
    }
  },
  Author: {
    books: (author) => filter(books, { authorId: author.id }),
  },
  Book: {
    author: (book) => find(authors, { id: book.authorId }),
  },
};
```

At this stage, I'm sure you are yearning to see it work. Before shedding more light on each of the resolver functions, go ahead and install `apollo-server`, `graphql`, and `lodash`.

```bash
npm install apollo-server graphql lodash --save
```

> [Apollo Server](https://www.apollographql.com/docs/apollo-server) is a library that helps you quickly build and deploy a GraphQL API. It also has variants, such as apollo-server-express, apollo-server-hapi, apollo-server-koa, and others to support serverless deployment with AWS Lambda. Apollo Server has built-in support for GraphQL subscriptions, powerful error handling tools, schema stitching, file uploads, mocking, schema directives, and easy monitoring integration.


{% include tweet_quote.html quote_text="Apollo Server is a library that helps you quickly build and deploy a GraphQL API." %}

The full `server.js` should look like this now:

```js

const { ApolloServer, gql } = require('apollo-server');
const { find, filter } = require('lodash');

const books = [
    { id: 1, title: 'The Trials of Brother Jero',  cover_image_url: 'ssdsds', average_rating: 8, authorId: 1 },
    { id: 2, title: 'Half of a Yellow Sun',  cover_image_url: 'dsdsds', average_rating: 9, authorId: 3 },
    { id: 3, title: 'Americanah',  cover_image_url: 'dsdsds', average_rating: 9, authorId: 3 },
    { id: 4, title: 'King Baabu',  cover_image_url: 'sdsds', average_rating: 7, authorId: 1 },
    { id: 5, title: 'Children of Blood and Bone',  cover_image_url: 'sdsds', average_rating: 7, authorId: 2 },
  ];

  const authors = [
    { id: 1, first_name: 'Wole', last_name: 'Soyinka' },
    { id: 2, first_name: 'Tomi', last_name: 'Adeyemi' },
    { id: 3, first_name: 'Chimamanda', last_name: 'Adichie' },
  ];

 const typeDefs = gql`
    type Author {
      id: Int!
      first_name: String!
      last_name: String!
      books: [Book]!
    }

    type Book {
      id: Int!
      title: String!
      cover_image_url: String!
      average_rating: Float!
      author: Author!
    }

    type Query {
      books: [Book!]!,
      book(id: Int!): Book!
      author(id: Int!): Author!

    }

    type Mutation {
      addBook(title: String!, cover_image_url: String!, average_rating: Float!, authorId: Int!): Book!
    }
  `;

  let book_id = 5;
  let author_id = 3;

  const resolvers = {
    Query: {
      books: () => books,
      book: (_, { id }) => find(books, { id: id }),
      author: (_, { id }) => find(authors, { id: id }),
    },
    Mutation: {
     addBook: (_, {title, cover_image_url, average_rating, authorId }) => {
        book_id++;

        const newBook = {
          id: book_id,
          title,
          cover_image_url,
          average_rating,
          author_id
        };

        books.push(newBook);
        return newBook;
      }
    },
    Author: {
      books: (author) => filter(books, { authorId: author.id }),
    },
    Book: {
      author: (book) => find(authors, { id: book.authorId }),
    },
  };

  const server = new ApolloServer({
    typeDefs,
    resolvers
  });

  server.listen().then(({ url }) => {
    console.log(`🚀  Server ready at ${url}`);
  });
```

Run `nodemon -r esm src/server.js` and check out the URL in your browser, `http://localhost:4000/`. You can run several queries to test your API in the GraphQL Playground, just like you can test your REST API endpoints with Postman.

Back to the resolver functions! We have resolver functions for all the fields in the `Query` and `Mutation` type. If you're following the tutorial step by step, you might have noticed this is a one-to-many relationship. One Author has many books, and many books belong to one author. Resolving the field in `Author`, and `Book` type enables us to get the right data when we have nested queries such as this:

```bash

{
  author(id: 3) {
    first_name
    last_name
    books {
      title
      cover_image_url
      average_rating
    }
  }

  book(id: 2) {
    title
    author {
      id
      first_name
      last_name
    }
  }
}
```

Experiment with the queries in the browser and play around with the resolvers to get a clearer picture. Check out [this excellent article on how GraphQL turns a query into a response](https://blog.apollographql.com/graphql-explained-5844742f195e).


### Connect to a Database

Let's swap out the arrays for a proper database. In this tutorial, we'll make use of SQLite and Sequelize. Go ahead and install them via npm:

```
npm install sequelize sqlite --save
```

Create a `store.js` file within the `src` directory and a `coolreads.sqlite` file within the root directory. The `sqlite` file is our database store.

Add code to `store.js` like so:

```js
const Sequelize = require('sequelize');
const casual = require('casual');
import _ from 'lodash';

const db = new Sequelize('coolreads', null, null, {
  dialect: 'sqlite',
  storage: './coolreads.sqlite',
});

const AuthorModel = db.define('author', {
  first_name: { type: Sequelize.STRING },
  last_name: { type: Sequelize.STRING },
});

const BookModel = db.define('book', {
  title: { type: Sequelize.STRING },
  cover_image_url: { type: Sequelize.STRING },
  average_rating: { type: Sequelize.STRING },
});

AuthorModel.hasMany(BookModel);
BookModel.belongsTo(AuthorModel);

// create mock data with a seed, so we always get the same
casual.seed(123);
db.sync({ force: true }).then(() => {
  _.times(10, () => {
    return AuthorModel.create({
      first_name: casual.first_name,
      last_name: casual.last_name,
    }).then((author) => {
      return author.createBook({
        title: casual.title,
        cover_image_url: casual.url,
        average_rating: casual.integer(-10, 10),
      });
    });
  });
});

const Author = db.models.author;
const Book = db.models.book;

export { Author, Book };
```

In the code above, we initialized our database, created the `Author` and `Book` models, established the relationship, and built a seeder for provisioning the database with initial data! What do we do next? Go ahead and install `casual`:

```
npm install casual
```

**Note:** `casual` is a package that generates random fake data.

Run `nodemon -r esm src/store.js`. From the terminal, you should see data been added to the database. However, you can't view the data in the _sqlite_ file unless you use an SQLite browser.

Go ahead and install the [SQLite browser](https://github.com/sqlitebrowser/sqlitebrowser). It will prove very useful!

Open the _sqlite_ file via the SQLite browser. You'll see all the data in it. Now, go ahead and replace that data with the initial data we started with, `books` and `authors` array.

![SQLite browser Database view Books Section](https://cdn.auth0.com/blog/sqlite/books.png)
_SQLite: Coolreads database, Books table_

![SQLite browser Database view Authors Section](https://cdn.auth0.com/blog/sqlite/authors.png)
_SQLite: Coolreads database, Authors table_


### Rewrite the Resolvers

Open up `src/server.js` file. Import the models from the store:

```js
...
import { Author, Book } from './store';
```

Rewrite the resolvers to use the database model methods in fetching data from the SQLite database:

```js
...
Query: {
  books: () => Book.findAll(),
  book: (_, args ) => Book.find({ where: args }),
  author: (_, args) => Author.find({ where: args })
},

Author: {
  books: (author) => author.getBooks(),
},

Book: {
 author: (book) => book.getAuthor(),
},

Mutation: {
  addBook: (_, {title, cover_image_url, average_rating, authorId }) => {

   return Book.create({
      title: title,
      cover_image_url: cover_image_url,
      average_rating: average_rating,
      authorId: authorId
    }).then(book => {
      return book;
    });
  }
}
```

* `Book.findAll` - Fetches all the books from the `books` table.
* `Book.find` - Fetches the book that has a certain `id`.  `args` is an object in the [resolver signature](https://www.apollographql.com/docs/graphql-tools/resolvers#Resolver-function-signature) that contains arguments passed to the book field as declared in the schema. In this case, `args` is `{ id }`.
* `Author.find` - Fetches the author with a certain `id`.
* `author.getBooks` - Selects and returns the books that belong to a particular author.
* `book.getAuthor` - Fetches the author that wrote a certain book.
* `Book.create` -  Creates a new book, writes it to the database and returns the details of the new book that was just created.


### Build The Frontend

We'll use ReactJS library to build the frontend. Create a new app like so:


After installing globally, go ahead and scaffold a new **ReactJS** app like so:

```bash
create-react-app coolreads
```

**Note:** If you don't have the [CRA tool](https://github.com/facebookincubator/create-react-app), go ahead and install it globally, `npm install -g create-react-app`.


After scaffolding, go ahead and run the app like so:

```
npm start
```

Then open [`http://localhost:3000`](http://localhost:3000) to see your app.

![Create React App localhost view showing](https://cdn.auth0.com/blog/react/ready-app.png)

**Note:** `create-react-app` automatically invokes Yarn for installation. If you don't have Yarn installed, it falls back to use npm.

Before we start fleshing out new components, we'll set up `Apollo Client` in our React app. The simplest way to get started with Apollo Client is by using Apollo Boost, a starter kit that configures your client for you with our recommended settings. Apollo Boost includes packages that we think are essential for building an Apollo app, like our in-memory cache, local state management, and error handling. It’s also flexible enough to handle features like authentication.

Install the following packages:

```bash
npm install apollo-boost react-apollo graphql --save
```

* **apollo-boost:** Package containing everything you need to set up Apollo Client.
* **react-apollo:** View layer integration for React.
* **graphql:** Also parses your GraphQL queries.

We'll need to do some routing and styling in this app. Go ahead and install the following:

```bash
npm install tachyons react-router react-router-dom --save
```

* **tachyons:** is a CSS toolkit for styling applications
* **react-router-dom:** is a router library for react

## Set Up Apollo Client and Routing

Open up `index.js` and rewrite the code to be this:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from "./App";
import { BrowserRouter } from 'react-router-dom';
import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";

import 'tachyons';
import './index.css';

import registerServiceWorker from './registerServiceWorker';

const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});

ReactDOM.render(
  <BrowserRouter>
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  </BrowserRouter>,
  document.getElementById('root')
);

registerServiceWorker();
```

To connect Apollo Client to React, you will need to use the `ApolloProvider` component exported from `react-apollo` like we did in the code above. The `ApolloProvider` is similar to React’s `context` provider. It wraps your React app and places the client on the context, which allows you to access it from anywhere in your component tree.

In the code above, we also configured routing by using the `BrowserRoute` component to wrap the `ApolloProvider ` component in the render method.

Open up `App.js` and rewrite the code to be this:

```js
import React, { Component } from 'react';
import {Route, withRouter} from 'react-router-dom';
import Nav from './components/Nav';
import ListBook from './components/ListBook';
import CreateBook from './components/CreateBook';
import './App.css';

class App extends Component {

  render() {
    return (
      <div>
        <Nav />
        <Route exact path='/' component={ListBook} />
        <Route exact path='/create' component={CreateBook} />
      </div>
    );
  }
}

export default withRouter(App);
```

Next, let's start building our components. We need to build 4 major components:

* **CreateBook:** This component will be for uploading a reviewed book.
* **ListBook:** This component will be for listing all the reviewed books.
* **Nav:** This component will be strictly for navigation.
* **Callback:** This special component is for handling authentication while retrieving tokens from the Auth0 server. You'll understand why this component later in the tutorial.


These components will reside in an `src/components` directory. Go ahead and create that directory.

## Build the Nav Component

Create a `Nav.js` file in the `src/components` directory. This will house the Nav component. Go ahead and add code to it like so:

```js
import React, { Component } from 'react';
import { Link, withRouter } from 'react-router-dom';
import '../App.css';

class Nav extends Component {

  render() {
    return (
      <nav className="navbar navbar-default">
        <div className="navbar-header">
          <Link className="navbar-brand" to="/">COOL READS</Link>
        </div>
        <ul className="nav navbar-nav">
          <li>
            <Link to="/">All Book Ratings</Link>
          </li>
          <li>
            <Link to="/create">Upload a Rated Book</Link>
          </li>
        </ul>
        <ul className="nav navbar-nav navbar-right">
          <li><button className="btn btn-info log">Log In</button></li>
        </ul>
      </nav>
    );
  }
}

export default withRouter(Nav);
```

**Note:** The `withRouter` is a HOC that allows a component to have access to the history object's properties. It will pass the updated `match`, `location`, and `history` props to the wrapped component whenever it renders. Once we add authentication to this app, you'll understand why it's useful. For now, don't think too much about it, just flow with the tutorial.

## Build the ListBook and CreateBook Component

The `ListBook` component will be responsible for displaying the details of each book. Create a `components/ListBook.js` file and add the following code:


```js
import React from 'react';
import { Query } from "react-apollo";
import { gql } from "apollo-boost";
import '../App.css';

const LIST_BOOKS = gql`
  query AllBooks {
    books {
      id
      title
      cover_image_url
      average_rating
    }
  }
`

export default () => (
  <Query query={LIST_BOOKS}>
    {({ loading, error, data }) => {
      if (loading) return <p>Loading...</p>;
      if (error) return <p>Error...</p>;

      return (
        <div className="col-sm-12">
          {!loading &&
            data.books.map(book => (
              <div className="col-sm-4" key={book.id}>
                <div className='pa3 bg-black-05 ma3'>
                  <div
                  {% raw %}
                    style={{
                      backgroundImage: `url(${book.cover_image_url})`,
                      backgroundSize: 'cover',
                      paddingBottom: '100%',
                    }}
                  {% endraw %}
                  />
                  <div>
                    <div className='book'>
                      <h3 align="center"> { book.title }&nbsp; </h3>
                      <h4 align="center">Average Rating:  { book.average_rating } / 10 </h4>
                    </div>
                  </div>
                </div>
              </div>
            ))}
        </div>
      );
    }}
  </Query>
);
```

Let's analyze the code above carefully:

* **Query**: The `Query` component from `react-apollo` is a React component that uses the _render prop_ pattern to share GraphQL data with your UI.
* **gql**: The `gql` function is a JavaScript template tag that parses GraphQL queries into an abstract syntax tree (AST).

Then the query, _LIST_BOOKS_, is for querying the GraphQL API for the list of books available:

```js
const LIST_BOOKS = gql`
  query AllBooks {
    books {
      id
      title
      cover_image_url
      average_rating
    }
  }
`
```

**Note:** The query operation name, `AllBooks`, is very useful for logging and debugging when something goes wrong in a GraphQL server.

In the function below the query, we passed the GraphQL query to the `query` prop on the `Query` component. Then, we provided a function to the Query component’s children prop to determine what to render, which Query will call with an object containing `loading`, `error`, and `data` properties. Apollo Client tracks error and loading state for you, which will be reflected in the loading and error properties. Once the result of your query comes back, it will be attached to the `data` property.

Now, let's create the `CreateBook` component! Create a `components/CreateBook.js` file and add the following code to it:

```js
import React from 'react';
import { Mutation } from "react-apollo";
import { gql } from "apollo-boost";

const ADD_BOOK = gql`
  mutation addBook($title: String!, $cover_image_url: String!, $average_rating: Float!, $authorId: Int!){
    addBook(title: $title, cover_image_url: $cover_image_url, average_rating: $average_rating, authorId: $authorId){
      id
      title
      cover_image_url
      average_rating
    }
  }
`

export default () => (
  <Mutation mutation={ADD_BOOK} onCompleted={() => window.location.href="/" }>
      {(addBook, { data, loading, error }) => (
        <div>
          <div className='w-100 pa4 flex justify-center'>
            <form
              onSubmit={e => {
                e.preventDefault();
                addBook({ variables: {
                  title: this.title.value,
                  cover_image_url: this.coverImage.value,
                  average_rating: parseFloat(this.rating.value),
                  authorId: parseInt(this.authorId.value)
                }});

                this.title.value = "";
                this.coverImage.value = "";
                this.rating.value = "";
                this.authorId.value = "";
              }}
            >

            {% raw %}
            <div style={{ maxWidth: 400 }} className=''>
              <label> Book Title: </label>
              <input
                className='w-100 pa3 mv2'
                type="text"
                required
                placeholder='Title of the book'
                ref={node => this.title = node} />

              <label> Book Cover Image: </label>
              <input
                className='w-100 pa3 mv2'
                type="url"
                required
                placeholder='Image Url'
                ref={node => this.coverImage = node} />

              <label> Book Rating as decided by Popular votes: </label>
              <input
                className='w-100 pa3 mv2'
                type="number"
                required
                min="1"
                max="10"
                placeholder='Average Rating'
                ref={node => this.rating = node} />

              <label> Author: </label>
              <select
                ref={select => this.authorId = select}
                name="authorId" required>
                <option value="">Select an author</option>
                <option value="1">Wole Soyinka</option>
                <option value="2">Tomi Adeyemi</option>
                <option value="3">Chimamanda Adichie</option>
              </select>
            </div>
            {% endraw %}
            {loading && <p>Loading...</p> }
            {error && <p>Error :( Please try again</p>}

            <button type="submit">Add Book</button>
            </form>
          </div>
        </div>
      )}
  </Mutation>
);
```

Let's analyze the code above carefully:

* **Mutation**: The `Mutation` component from `react-apollo` is a React component that uses the _render prop_ pattern to trigger mutations from your UI. React will call the _render prop_ function you provide with a `mutate` function and an object with your mutation result containing `loading`, `error`, and `data` properties

Then the `Mutation` query, _ADD_BOOK_, is for mutating the GraphQL API:

```js
const ADD_BOOK = gql`
  mutation addBook($title: String!, $cover_image_url: String!, $average_rating: Float!, $authorId: Int!){
    addBook(title: $title, cover_image_url: $cover_image_url, average_rating: $average_rating, authorId: $authorId){
      id
      title
      cover_image_url
      average_rating
    }
  }
`
```

In the function below the `Mutation`, we passed the mutation query to the `mutation` prop on the `Mutation` component.

The mutate function optionally takes `variables`, `optimisticResponse`, `refetchQueries`, and `update`; however, you can also pass in those values as props to the `Mutation` component. In the form, the mutate function, `addBook`, is used to submit the form with variables from the form itself.

The second argument to the render prop function is an object with your mutation result on the `data` property, as well as booleans for loading and if the mutate function was called, in addition to error. If you’d like to ignore the result of the mutation, pass ignoreResults as a prop to the mutation component.

In the code above, we passed an `onCompleted` prop to the `Mutation` component. The function passed to this prop is invoked when the mutation has finished execution. And in our app, it simply redirects to the root route once the mutation completes.

## Styling

We need to reference bootstrap for styling. Add the link to bootstrap in `public/index.html` file.

```bash
...
<link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
...
```

Modify the `src/App.css` file to contain the code [here](https://github.com/auth0-blog/book-app/blob/master/client/src/App.css).

Now, run your app. There should be a list of books on the landing page. The existing data from our backend is fetched and rendered on our client app.

![React App locally showing our list of books](https://cdn.auth0.com/blog/coolreads/landing-page.png)

Try, to add a book by going to the `/create` route and using the form:

![React App locally showing our create book component page](https://cdn.auth0.com/blog/coolreads/createbook.png)

Awesome! Right now, you can create books. Next, let's make sure only authenticated users can add books.

## Secure your GraphQL API with Auth0

Auth0 allows us to easily add authentication to applications. Let's get started with securing our GraphQL API. Open your GraphQL API terminal and install the following libraries:

```bash
npm install jsonwebtoken jwks-rsa --save
```

* **[jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)**: JsonWebToken implementation for Node.js
* **[jwks-rsa](https://github.com/auth0/node-jwks-rsa)**: A library to retrieve RSA public keys from a JWKS (JSON Web Key Set) endpoint.

Open up `src/server.js` and import them:

```js
const { ApolloServer, gql, AuthenticationError } = require('apollo-server');
const jwt = require('jsonwebtoken');
const jwksClient = require('jwks-rsa');
...
...
```

Now, let's add the code to verify a token:

```js
const { ApolloServer, gql, AuthenticationError } = require('apollo-server');
const jwt = require('jsonwebtoken');
const jwksClient = require('jwks-rsa');
import { Author, Book } from './store';


const client = jwksClient({
  jwksUri: `https://<YOUR_AUTH0_DOMAIN>/.well-known/jwks.json`
});

function getKey(header, cb){
  client.getSigningKey(header.kid, function(err, key) {
    var signingKey = key.publicKey || key.rsaPublicKey;
    cb(null, signingKey);
  });
}

const options = {
  audience: '<YOUR_AUTH0_CLIENT_ID>',
  issuer: `https://<YOUR_AUTH0_DOMAIN>/`,
  algorithms: ['RS256']
};

...
```

Replace `<YOUR_AUTH0_DOMAIN>` with the value in the **Domain** field of your Auth0 application (e.g. `kabiyesi.auth0.com`). Also, replace the `<YOUR_AUTH0_CLIENT_ID>` placeholder with the value presented in the **Client ID** field of your Auth0 Application.

In GraphQL, there's no set standard on how to perform authentication and authorization for APIs. However, Apollo has an [excellent guide for access control in GraphQL](https://www.apollographql.com/docs/guides/access-control.html).

One of the ways of adding authentication to GraphQL APIs according to the guide is putting user information on the **context**. Let's go ahead and do that right away!

_src/server.js_

```js
...
...
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req }) => {
    // simple auth check on every request
    const token = req.headers.authorization;
    const user = new Promise((resolve, reject) => {
      jwt.verify(token, getKey, options, (err, decoded) => {
        if(err) {
          return reject(err);
        }
        resolve(decoded.email);
      });
    });

    return {
      user
    };
  },
});

...
```

In the Apollo Server constructor above, we pass a function to build our `context` object. The context object is one that gets passed to every single resolver at every level, so we can access it anywhere in our schema code. It’s where we can store things like data fetchers, database connections, and information about the user making the request.

Since the context is generated again with every new request, we don’t have to worry about cleaning up user data at the end of execution.

The context function here looks at the request headers, pulls off the header named authorization, and stores it to a `token` variable. It then calls the `jwt.verify` method to validate the token by using the `getKey` callback. A promise is resolved with the user's email if the token is valid and a promise is rejected with an error if the token is not a valid one. After that, it returns a context object containing the (potential) user, for all of our resolvers to use.

Now, let's get the user from the context object in our Mutation resolver!

```js
const { ApolloServer, gql, AuthenticationError } = require('apollo-server');

...
...
...
const resolvers = {
  ...

  Mutation: {
    addBook: async (_, {title, cover_image_url, average_rating, authorId }, { user }) => {
       try {
        const email = await user; // catching the reject from the user promise.
        const book = await Book.create({
          title: title,
          cover_image_url: cover_image_url,
          average_rating: average_rating,
          authorId: authorId
        });

        return book;
       } catch(e) {
           throw new AuthenticationError('You must be logged in to do this');
       }
    }
  }
};
```

The third argument in a resolver is the `context` object. In the code above, we retrieved `{ user }` passed from the context in Apollo Server constructor in the `addBook` resolver.

```js
const email = await user;
```

This line of code above waits for the user promise to resolve and stores the email of the user in an `email` variable. If there's an error, the `catch` block gets the error and throws an `AuthenticationError`. Wait a minute? Where's the `AuthenticationError` coming from? We imported it from Apollo Server.

Apollo Server 2.0 provides a couple of predefined errors, including `AuthenticationError`, `ForbiddenError`, `UserInputError` and a generic `ApolloError`. These errors are designed to enhance errors thrown before and during GraphQL execution. The provided errors focus on debugging an _Apollo_ server as well as enabling the client to take specific action based on an error.

When an [error occurs in Apollo server both inside and outside of resolvers](https://blog.apollographql.com/full-stack-error-handling-with-graphql-apollo-5c12da407210), each error inside of the errors array will contain an object at extensions that contains the information added by Apollo server.

Now, run your server and try to perform a mutation with a fake token!

![GraphQL mutation with fake token showing API error](https://cdn.auth0.com/blog/coolreadsapi/errors.png)
_GraphQL API Error_

## Secure your React App

Auth0 allows us to easily add authentication to applications. Login to your Auth0 [management dashboard](https://manage.auth0.com) and create a [new application](https://manage.auth0.com/#/applications). In the dialog shown, enter the name and select **Single Page Application** as its type:

![Auth0 Dashboard Select Application](https://cdn.auth0.com/blog/coolreads/selectapp.png)
_Select application_

![Auth0 Dashboard App Settings](https://cdn.auth0.com/blog/coolreads/settings.png)
_Grab client id_

In the Settings tab, add `http://localhost:3000/callback` in the **Allowed Callback URLs** and `http://localhost:3000` to the **Allowed Origins (CORS)**. Also add `http://localhost:3000` into the **Allowed Logout URLs** field.

Ensure you also replace the value of `<AUTH0_DOMAIN>` in `const AUDIENCE = 'https://<AUTH0_DOMAIN>/userinfo';` to your auth0 domain.

### Create the Auth Service

We'll create an authentication service to handle everything about authentication in our app. Go ahead and create an `Auth.js` file in the `src` directory.

Before we add code, you need to install `auth0-js` package like so:

```bash
npm install auth0-js --save
```

_src/Auth.js_

Open up the `Auth.js` file and add code to it like so:

```js
import auth0 from 'auth0-js';

class Auth {
  constructor() {
    this.auth0 = new auth0.WebAuth({
      domain: '<YOUR_AUTH0_DOMAIN>',
      clientID: '<YOUR_AUTH0_CLIENT_ID>',
      redirectUri: 'http://localhost:3000/callback',
      audience: 'https://<YOUR_AUTH0_DOMAIN>/userinfo',
      responseType: 'token id_token',
      scope: 'openid email'
    });

    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
    this.handleAuthentication = this.handleAuthentication.bind(this);
    this.isAuthenticated = this.isAuthenticated.bind(this);
  }

  login() {
    this.auth0.authorize();
  }

  getIdToken() {
    return this.idToken;
  }

  handleAuthentication() {
    return new Promise((resolve, reject) => {
      this.auth0.parseHash((err, authResult) => {
        if (err) return reject(err);
        if (!authResult || !authResult.idToken) {
          return reject(err);
        }
        this.setSession(authResult);
        resolve();
      });
    })
  }

  setSession(authResult) {
    this.idToken = authResult.idToken;
    console.log(this.idToken);
    // set the time that the id token will expire at
    this.expiresAt = authResult.expiresIn * 1000 + new Date().getTime();
  }

  logout() {
    this.auth0.logout({
      returnTo: 'http://localhost:3000',
      clientID: '<YOUR_AUTH0_CLIENT_ID>',
    });
  }

  silentAuth() {
    return new Promise((resolve, reject) => {
      this.auth0.checkSession({}, (err, authResult) => {
        if (err) return reject(err);
        this.setSession(authResult);
        resolve();
      });
    });
  }

  isAuthenticated() {
    // Check whether the current time is past the token's expiry time
    return new Date().getTime() < this.expiresAt;
  }
}

const auth = new Auth();

export default auth;
```

**Note:** Replace the `<YOUR_AUTH0_CLIENT_ID>` and `<YOUR_AUTH0_DOMAIN>` with the values from your Auth0 application.

Let's analyze what's going on in the authentication code above:

* **constructor:** An instance of `auth0.WebAuth` is created and initialized with your Auth0 values and define some other important configurations. For example, you are defining that Auth0 will redirect users (redirectUri) to the `http://localhost:3000/callback` URL (the same one you inserted in the Allowed Callback URLs field previously).
* **getIdToken:** This method returns the `idToken` generated by Auth0 for the current user.
* **handleAuthentication:** This is the method that your app will call right after the user is redirected from Auth0. This method simply reads the hash segment of the URL to fetch the user details and the id token.
* **isAuthenticated:** This method returns whether there is an authenticated user or not.
* **login:** This method initializes the authentication process. This method sends your users to the Auth0 login page.
* **logout:** This method signs a user out.

Let's go update the `Nav` component to hide/show the `login` and `logout` buttons based on the user's authentication status.

Now, your `Nav` component should be refactored to look like this:

```js
import React, { Component } from 'react';
import { Link, withRouter } from 'react-router-dom';
import auth from '../Auth';
import '../App.css';

class Nav extends Component {

  constructor(props){
    super(props);
  }

  logout = () => {
    auth.logout();
    this.props.history.replace('/');
  };

  render() {
    return (
      <nav className="navbar navbar-default">
        <div className="navbar-header">
          <Link className="navbar-brand" to="/">COOL READS</Link>
        </div>
        <ul className="nav navbar-nav">
          <li>
            <Link to="/">All Book Ratings</Link>
          </li>
          <li>
            {
              ( auth.isAuthenticated() ) ? <Link to="/create">Upload a Rated Book</Link> :  ''
            }
          </li>
        </ul>
        <ul className="nav navbar-nav navbar-right">
          <li>
            {
             (auth.isAuthenticated()) ? (<button className="btn btn-danger log" onClick={() => this.logout()}>Log out </button>) : (<button className="btn btn-info log" onClick={() => auth.login()}>Log In</button>)
            }
          </li>
        </ul>
      </nav>
    );
  }
}

export default withRouter(Nav);
```

### Add A Callback Component

We will create a new component in the `src/components` directory and call it `Callback.js`. This component will be activated when the `localhost:3000/callback` route is called and it will process the redirect from Auth0 and ensure we received the right data back after a successful authentication.

_Callback.js_

```js
import React, { Component } from 'react';
import {withRouter} from 'react-router-dom';
import loading from '../loading.svg';
import auth from '../Auth';

class Callback extends Component {

  async componentDidMount() {
    await auth.handleAuthentication();
    this.props.history.replace('/');
  }

  render() {
    const style = {
      position: 'absolute',
      display: 'flex',
      justifyContent: 'center',
      height: '100vh',
      width: '100vw',
      top: 0,
      bottom: 0,
      left: 0,
      right: 0,
      backgroundColor: 'white',
    }

    return (
      <div style={style}>
        <img src={loading} alt="loading"/>
      </div>
    );
  }
}

export default withRouter(Callback);
```

Once a user is authenticated, Auth0 will redirect back to our application and call the `/callback` route. Auth0 will also append the `id_token` to this request, and our Callback component will make sure to properly process and store the token in the in-app memory. Then, the app will be redirected back to the `/` page and will be in a logged-in state.

Go ahead and invoke the command below to copy the `loading.svg` into the appropriate directory:

```bash
curl -o src/loading.svg -O https://raw.githubusercontent.com/auth0-blog/book-app/master/client/src/loading.svg
```

Now, go ahead and add the `/callback` route in `App.js`.

```js
...
import Callback from './components/Callback';
...

class App extends Component {

  render() {
    return (
      <div>
        <Nav />
        <Route exact path='/' component={ListBook} />
        <Route exact path='/create' component={CreateBook} />
        <Route exact path='/callback' component={Callback} />
      </div>
    );
  }
}

export default withRouter(App);
```

### Send Token Via Headers From Apollo Client

Open `index.js` and modify it to be this:

```js
...
import auth from './Auth';
...
const client = new ApolloClient({
  uri: "http://localhost:4000/graphql",
  request: operation => {
    operation.setContext(context => ({
      headers: {
        ...context.headers,
        authorization: auth.getIdToken(),
      },
    }));
  },
});
...
```

In the code above, we're setting the authorization header to the value of the `id_token` and sending it to the GraphQL API endpoint. Now try, to add a reviewed book again!

### Keeping Users Signed In

Right now, once you reload the application, the users automatically get logged out because the user's credentials are stored in the app's memory. Let's keep the users logged in!

We'll use the [Silent Authentication](https://auth0.com/docs/api-auth/tutorials/silent-authentication) provided by Auth0. Whenever your application is loaded, it will send a silent request to Auth0 to check if the current user (actually the browser) has a valid session. If they do, Auth0 will send back to you an `idToken` and an `idTokenPayload,` just like it does on the authentication callback.

Head over to the [Applications section](https://manage.auth0.com/#/applications) of your Auth0 dashboard and update the following:

* **Allowed Web Origins**: Add `http://localhost:3000` to the field. Without this value there, Auth0 would deny any AJAX request coming from your app.
* **Social Connections**: Auth0 auto-configure all new accounts to use development keys registered at Google for the social login. We expect developers to replace these keys with theirs once they start using Auth0 in more capacity. Furthermore, every time an app tries to perform a silent authentication, and that app is still using the development keys, Auth0 returns that there is no session active (even though this is not true). Head over to the [Social connections on your dashboard](https://manage.auth0.com/#/connections/social), and update the _Client ID_ and _Client Secret_ fields with the new keys gotten from [Connect your app to Google documentation provided by Auth0.](https://auth0.com/docs/connections/social/google).

**Note:** If you don't want to use your Google keys, you can deactivate this social connection and rely only on users that sign up to your app through [Auth0's Username and Password Authentication](https://manage.auth0.com/#/connections/database).

Check out the `silentAuth` function in the `Auth.js` file:

```js
...
silentAuth() {
  return new Promise((resolve, reject) => {
    this.auth0.checkSession({}, (err, authResult) => {
      if (err) return reject(err);
      this.setSession(authResult);
      resolve();
    });
  });
}
...
```

This method is responsible for performing the _Silent Authentication_. It makes a request to AuthO's `checkSession` function and sets a new session.

Let's refactor the `src/Auth.js` file to ensure the new authentication works properly.

```js

import auth0 from 'auth0-js';

class Auth {
  constructor() {
    ...
    this.authFlag = 'isLoggedIn';
    ...
  }

  ...
  isAuthenticated() {
    return JSON.parse(localStorage.getItem(this.authFlag));
  }

  setSession(authResult) {
    this.idToken = authResult.idToken;
    localStorage.setItem(this.authFlag, JSON.stringify(true));
  }

  signIn() {
    this.auth0.authorize();
  }

  signOut() {
    localStorage.setItem(this.authFlag, JSON.stringify(false));
    this.auth0.logout({
      returnTo: 'http://localhost:3000',
      clientID: 'LUft9iOEONnQilP8mFDdmiBHdNljGJ2u',
    });
  }

  silentAuth() {
    if(this.isAuthenticated()) {
      return new Promise((resolve, reject) => {
        this.auth0.checkSession({}, (err, authResult) => {
          if (err) {
            localStorage.removeItem(this.authFlag);
            return reject(err);
          }
          this.setSession(authResult);
          resolve();
        });
      });
    }
  }
}

const auth = new Auth();

export default auth;
```
Let's analyze the change above. The `authFlag` constant is a flag that we store in local storage to indicate whether a user is logged in or not. It is also an indicator for renewing tokens with the Auth0 server after a full-page refresh.

Once the user is authenticated and redirected back to the app from the Auth0 server, the `authFlag` is set to true and stored in local storage so that if the user returns to the app later, we can check whether to ask the authorization server for a fresh token. The `silentAuth` method checks if the user is indeed authorized via the Auth0 `checkSession` method if the `authFlag` is true. If the user is authorized, new authentication data is returned and the `setSession` method is called. If the user is not authorized, the `authFlag` is deleted from local storage and logged out.


One more thing. Head over to `App.js` and update it like so:

```js
import React, { Component } from 'react';
import {Route, withRouter} from 'react-router-dom';
import Nav from './components/Nav';
import Callback from './components/Callback';
import ListBook from './components/ListBook';
import CreateBook from './components/CreateBook';
import './App.css';
import auth from './Auth';

class App extends Component {

  async componentDidMount() {
    if (this.props.location.pathname === '/callback') return;
    try {
      await auth.silentAuth();
      this.forceUpdate();
    } catch (err) {
      if (err.error === 'login_required') return;
      console.log(err.error);
    }
  }

  render() {
    return (
      <div>
        <Nav />
        <Route exact path='/' component={ListBook} />
        <Route exact path='/create' component={CreateBook} />
        <Route exact path='/callback' component={Callback} />
      </div>
    );
  }
}

export default withRouter(App);
```

If the requested route is `/callback`, the app does nothing. This is the correct behavior because, when users are requesting for the `/callback` route, they do so because they are getting redirected by Auth0 after the authentication process. In this case, you can leave the Callback component to handle the process.

If the requested route is anything else, the app wants to try a `silentAuth`. Then, if no error occurs, the app calls `forceUpdate` so the user can see its name and that they are signed in.

If there is an error on the `silentAuth`, the app checks if the error is `login_required`. If this is the case, the app does nothing because it means the user is not signed in (or that you are using development keys, which you shouldn't).

If there is an error that is not `login_required`, the error is simply logged to the console. Actually, in this case, it would be better to notify someone about the error so they could check what is happening.

Try to authenticate your app. Then, refresh your browser and you'll discover that you won't have to lose your session again. Perfect!


### Secure the Create Route

You want to make sure users can't access the `/create` route without being authenticated first on the client side. Go ahead and create a `GuardedRoute.js` file in the `src/components` directory. Now add code to the `GuardedRoute.js` file:

```js
import React from 'react';
import {Route} from 'react-router-dom';
import auth from '../Auth';

function GuardedRoute(props) {
  const { component: Component, path} = props;
  return (
    <Route exact path={path} render={(props) => {
     if (!auth.isAuthenticated()) return auth.login();
      return <Component {...props} />
    }} />
  );
}

export default GuardedRoute;
```

In the code above, we created a functional component that checks if a user is authenticated before rendering a component. Let's apply this recently created functionality to the `/create` route.

Open up `App.js`. Make sure the `GuardedRoute` is imported at the top of the file like so:

```js
...
import GuardedRoute from './components/GuardedRoute';
...
```

Now, pass the `CreateBook` component to the `GuardedRoute` like so:

```js
...
import GuardedRoute from './GuardedRoute/GuardedRoute';
...

class App extends Component {

  ...

  render() {
    return (
      <div>
        <Nav />
        ...
        <GuardedRoute exact path='/create' component={CreateBook} />
      </div>
    );
  }
}

export default withRouter(App);
```

Once you try to access the `/create` route, you'll be redirected to the login page.


## Conclusion

In this tutorial, we covered how easy it is to build a product with GraphQL using great tools like Apollo Client, Apollo Server, React and Auth0. What a time to be alive!

The next step after this is to learn how to monitor your app's performance with Apollo Engine. Apollo Engine is a cloud service that provides deep insights into your GraphQL layer, so you can run in production with confidence. Check out this excellent guide on [GraphQL performance](https://www.apollographql.com/docs/guides/performance.html) and [monitoring](https://www.apollographql.com/docs/guides/monitoring.html).

If you want to learn more about using GraphQL, check out the [excellent Apollo guides](https://www.apollographql.com/docs/guides).

Let me know if you have any questions about GraphQL in the comments section!
