---
layout: post
title: "Developing and Securing Modern Apps with GraphQL, React and Auth0"
description: "Learn how to use GraphQL to build modern apps and add authentication to it with Auth0."
date: 2018-09-02 8:30
category: Technical guide, GraphQL, Authentication
author:
  name: "Prosper Otemuyiwa"
  url: "https://twitter.com/unicodeveloper"
  mail: "prosper.otemuyiwa@auth0.com"
  avatar: "https://en.gravatar.com/avatar/1097492785caf9ffeebffeb624202d8f?s=200"
design:
  bg_color: "#222228"
  image: "https://cdn.auth0.com/blog/graphql-auth0/logo.png"
tags:
- GraphQL
- auth
- react
- apollo
- authentication
related:
- 2017-01-24-optimizing-react
- 2017-02-21-reactjs-authentication-tutorial

---

**TL;DR:** Modern applications require a different paradigm in the way they are crafted and deployed. GraphQL in particular enables developers to take advantage of incredible tooling like the [Apollo platform](https://www.apollographql.com/docs/fundamentals/platform.html) for crafting flexible APIs and shipping client apps with optimistic UI. In this tutorial, we'll explore how to build an application with GraphQL and React. And we'll wrap it up with learning how to add authentication. The [completed code is available on GitHub](https://github.com/auth0-blog/conference-app).

{% include tweet_quote.html quote_text="Learn how to develop and secure modern applications with GraphQL, React and Auth0." %}

---

## The Modern Application Paradigm

Present day applications utilize the client-server design. This architectural design isolates software development into two major groups,**clients** and **servers**. These classifications are also known as **front end** and **back end**. APIs are developed and served from the back end, while the front end consumes them.

The client-server architecture aids productivity by permitting the option of having different clients devour the responses from an API on the server. These clients could be anything from Single Page Applications (SPAs), portable applications, to non-interactive clients such as CLIs or Daemons.

These days, most JavaScript applications are packaged and deployed as Single Page Applications running on Vanilla JS, Vue, React, Polymer, Angular, consuming and pusing information to a backend app running on the server. The front end/back end model of developing software is very common and goes way beyond the JavaScript community.


## Introduction to GraphQL

GraphQL is a typed query language for your API. It enables the client to express its data requirements declaratively and the server to send back only what the client requests. It's heavily in use by companies such as Coursera, Shopify and GitHub.

A GraphQL server exposes a schema that describes its API including queries to fetch data and mutations to modify data. This allows clients to specify their data requirements with queries and send it to one GraphQL endpoint, instead of collecting information from multiple endpoints as is typical with REST. While queries are a very easy and quick way for clients to get exactly the data they need in one request, the GraphQL server has to parse and validate the query, check which fields are included and return the underlying data from the database.

The type-safe schema unlocks new possibilities for tooling, as demonstrated by GraphQL which is maintained by Facebook. Check out how GitHub employs [GraphQL for its API](https://githubengineering.com/the-github-graphql-api/).

## What we'll build

We'll build an application called **EasyReads**. **GoodReads** is a popular web application where users can actually review the books that they have read over time. These reviews help new users determine whether to embark on reading a book or not.

**EasyReads** is an application that displays a list of books with the appropriate book cover image, title and average user ratings. Everyone should be able to see the ratings of these books, but only an authenticated user can add a new book with ratings. We'll assume there is a poll of user votes where the authenticated users just get the ratings and add to the platform.

We'll make use of GraphQL to build the API for the application. GraphQL’s strongly typed query language enables developers to take advantage of incredible tooling for exploring GraphQL APIs. Developers can query a GraphQL schema for information about what queries and types it supports because of the built-in introspection system.


## Building Easy Reads GraphQL API

### Schema First Driven Development

Schema First Driven development is a recommended approach for designing and building modern UIs that involves the frontend and backend teams agreeing on a Schema first, which serves as a contract between the UI and the backend before any API development commences. GraphQL schemas are at their best when they are designed around the needs of client applications. We'll take this approach in designing a Schema for our API.

### Building Easy Reads Schema

At the core of any GraphQL server is a schema. The schema defines types and their relationships. It also specifies which queries can be made against the server. Let's build out the schema for **Easy Reads**.

Create a `src/server.js`file in the _api_ directory. Our schema will live here.

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

We have the `Author`, `Book`, `Query` and `Mutation` type. The `Query` type informs the server about the queries. that users are allowed to make. The `gql` tag is a JavaScript template literal tag that parses GraphQL queries into an abstract syntax tree during the process of execution by the GraphQL engine.

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

Resolvers provide the instructions for turning a GraphQL operation into data. They are organized into a one to one mapping to the fields in a GraphQL schema. Without much ado, let's add an in-app memory data source!


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
      author(id: Int!): Author!

    }

    type Mutation {
      addBook(title: String!, cover_image_url: String!, average_rating: Float!, authorId: Int!): Book!
    }
  `;
```

In the code above, we have the `books` and `authors` data source. We'll go ahead to write the resolvers that fetches data from the data source above before progressing to hooking up our GraphQL API with a proper database.

### Writing the Resolvers

Resolvers are functions that connects schema fields and types to various backends. As I mentioned earlier, they provide the instructions for turning a GraphQL operation into data. They can retrieve or write data from either an SQL, a No-SQL, graph database, a micro-service or a REST API. Resolvers can also return strings, ints, null, and other primitives.

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
      console.log(books);
      return newBook;
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
npm install apollo-server graphql lodash
```

> [Apollo Server](https://www.apollographql.com/docs/apollo-server) provides GraphQL schemas written with it the privilege to be deployed anywhere that other Node.js projects can be deployed. It also has variants, such as apollo-server-express, apollo-server-hapi, apollo-server-koa and others to support serverless deployment with AWS Lambda. Apollo Server has built-in support for GraphQL subscriptions, powerful error handling tools, schema stitching, file uploads, mocking, schema directives and easy monitoring integration.

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
        console.log(books);
        return newBook;
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

Run `node src/server.js` and check out the URL in your browser, `http://localhost:4000/`. You can run several queries to test your API in the GraphQL Playground, just like you can test your REST API endpoints with Postman.

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

In the code above, we initialized our database, created the `Author` and `Book` models, established the relationship, and built a seeder for provisioning the database with initial data!

Install `casual`:

```
npm install casual
```

**Note:** `casual` is a package that generates random fake data.

Run your server again. From the terminal, you should see data been added added to the database. However, you can't view the data in the _sqlite_ file unless you use a SQLite browser.

Go ahead and install the [SQLite browser](https://github.com/sqlitebrowser/sqlitebrowser). It will prove very useful!

Open the _sqlite_ file via the SQLite browser. You'll see all the data in it. Now, go ahead and replace that data with the initial data we started with, `books` and `authors` array.

![Books Section](https://cdn.auth0.com/blog/sqlite/books.png)
_SQLite: Coolreads database, Books table_

![Authors Section](https://cdn.auth0.com/blog/sqlite/authors.png)
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
};
```

* `Book.findAll` - Fetches all the books from the `books` table.
* `Book.find` - Fetches the book that has a certain `id`.  `args` is an object in the [resolver signature](https://www.apollographql.com/docs/graphql-tools/resolvers#Resolver-function-signature) that contains arguments passed to the book field as declared in the schema. In this case, `args` is `{ id }`.
* `Author.find` - Fetches the author with a certain `id`.
* `author.getBooks` - Selects and returns the books that belongs to a particular author.
* `book.getAuthor` - Fetches the author that wrote a certain book.
* `Book.create` -  Creates a new book, writes it to the database and returns the details of the new book that was just created.



### Build The Frontend

We'll use ReactJS to build the frontend. Go ahead and flesh out a new app.

If you dont have the [CRA tool](https://github.com/facebookincubator/create-react-app), go ahead and install it globally:

```bash

npm install -g create-react-app

```

After installing globally, go ahead and scaffold a new **ReactJS** app like so:

```bash

create-react-app rottentomatoes

```

> **Note:** We have a custom React script that comes bundled with Auth0 authentication. So you can use create-react-app to boostrap an app with authentication support like this `create-react-app my-app --scripts-version auth0-react-scripts`


Then open [`http://localhost:3000`](http://localhost:3000) to see your app.

![App recently scaffolded and showing at Localhost](https://cdn.auth0.com/blog/react/ready-app.png)

**Note:** `create-react-app` automatically invokes Yarn for installation. If you don't have Yarn installed, it falls back to use npm.

## Build the Nav Component

The `Nav.js` file is our Nav component. Go ahead and add code to it like so:

```js

import React, { Component } from 'react';
import { Link } from 'react-router';
import '../App.css';

class Nav extends Component {

  render() {
    return (
      <nav className="navbar navbar-default">
        <div className="navbar-header">
          <Link className="navbar-brand" to="/">Rotten Tomatoes</Link>
        </div>
        <ul className="nav navbar-nav">
          <li>
            <Link to="/">All Movie Ratings</Link>
          </li>
          <li>
           <Link to="/create">Add New Movies</Link>
          </li>
        </ul>
        <ul className="nav navbar-nav navbar-right">
          <li><button className="btn btn-info log">Log In</button></li>
          <li><button className="btn btn-danger log">Log out </button></li>
        </ul>
      </nav>
    );
  }
}

export default Nav;

```

**Note:** Open up your terminal and install `react-router` like so: `npm install react-router@3.0.0 --save`. At the time of this writing, `react-router` is in 4.0 alpha, so you can explore its features.

## Build the DisplayMovie and CreateMovie Component

The `DisplayMovie` component will be responsible for displaying the details of each movie. Create a `components/DisplayMovie.js` file and the following code:

```js

import React from 'react'
import '../App.css';

class DisplayMovie extends React.Component {

  render () {
    return (

      <div className='pa3 bg-black-05 ma3'>
        <div
        {% raw %}
          style={{
            backgroundImage: `url(${this.props.movie.imageUrl})`,
            backgroundSize: 'cover',
            paddingBottom: '100%',
          }}
        {% endraw %}
        />
        <div>
          <div className='movie'>
            <h3><span className='movie-title'>Movie Title: </span> {this.props.movie.description}&nbsp; </h3>
            <h2><span className='movie-title'>Rating: </span> { this.props.movie.avgRating }% </h2>
          </div>
        </div>
      </div>
    )
  }
}


export default DisplayMovie

```

In the code above, you can see how we access the properties of the movie schema in form of props. You'll get to know how it works that way soon.

The `CreateMovie` component will be responsible for adding new movies to our platform. So go ahead and create a `components/CreateMovie.js` file. Add the following code to it:

```js

import React from 'react'
import { withRouter } from 'react-router'
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'
import Nav from './Nav';

class CreateMovie extends React.Component {

  state = {
    description: '',
    imageUrl: '',
    avgRating: 0,
  }

  render () {
    return (
      <div>
        <Nav />
        <h3 className="text-center"> Add Rotten Movie Ratings!</h3>
        <hr/>
        <div className='w-100 pa4 flex justify-center'>
        {% raw %}
          <div style={{ maxWidth: 400 }} className=''>
        {% endraw %}
            <label> Movie Title: </label>
            <input
              className='w-100 pa3 mv2'
              value={this.state.description}
              placeholder='Title of the movie'
              onChange={(e) => this.setState({description: e.target.value})}
            />
            <label> Movie Cover Image: </label>
            <input
              className='w-100 pa3 mv2'
              value={this.state.imageUrl}
              placeholder='Image Url'
              onChange={(e) => this.setState({imageUrl: e.target.value})}
            />
            <label> Movie Rating as decided by Popular votes: </label>
            <input
              className='w-100 pa3 mv2'
              value={this.state.avgRating}
              type="number"
              placeholder='Average Rating'
              onChange={(e) => this.setState({avgRating: parseInt(e.target.value)})}
            />

            {this.state.imageUrl &&
              <img src={this.state.imageUrl} role='presentation' className='w-100 mv3' />
            }
            {this.state.description && this.state.imageUrl &&
              <button className='btn btn-info btn-lg' onClick={this.handleMovie}>Add New Movie</button>
            }
          </div>
        </div>
      </div>
    )
  }

  handleMovie = () => {
    const {description, imageUrl, avgRating} = this.state
    this.props.addMovie({ description, imageUrl, avgRating })
      .then(() => {
        this.props.router.push('/')
    })
  }
}

const addMutation = gql`
  mutation addMovie($description: String!, $imageUrl: String!, $avgRating: Int!) {
    createMovie(description: $description, imageUrl: $imageUrl, avgRating: $avgRating) {
      id
      description
      imageUrl
      avgRating
    }
  }
`

export default graphql(addMutation, {
  props: ({ ownProps, mutate }) => ({
    addMovie: ({ description, imageUrl, avgRating }) =>
      mutate({
        variables: { description, imageUrl, avgRating },
      })
  })
})(withRouter(CreateMovie))

```

In the code above, we have new buddies. The `graphql-tag` and `react-apollo` packages. Apollo is a GraphQL client. Install these new packages like so:

```
npm install graphql-tag react-apollo apollo-client
```

**Note:** I added one more package for us to install via the terminal. The [Apollo Client](http://dev.apollodata.com/react). I'll explain what it does later on in the article.

* `graphql-tag` provides functionality for parsing the JavaScript template literals that will contain our GraphQL queries and mutations.
* `react-apollo` implements React-specific bindings for Apollo
* `apollo-client` is one of the most popular GraphQL clients available. It makes interacting with a GraphQL backend seamless. It comes bundled with features like caching, query batching, and realtime updates via subscriptions.

```js

const addMutation = gql`
  mutation addMovie($description: String!, $imageUrl: String!, $avgRating: Int!) {
    createMovie(description: $description, imageUrl: $imageUrl, avgRating: $avgRating) {
      id
      description
      imageUrl
      avgRating
    }
  }
`

```

This code above represents a new mutation with `gql`.

```js

export default graphql(addMutation, {
  props: ({ ownProps, mutate }) => ({
    addMovie: ({ description, imageUrl, avgRating }) =>
      mutate({
        variables: { description, imageUrl, avgRating },
      })
  })
})(withRouter(CreateMovie))

```

This code above injects the `addMovie` function to the props of our `CreateMovie` component and makes it possible for a create operation query to happen on the backend with the values of the movie `description`, `imageUrl` and `avgRating`. Adding mutations to React components is similar to adding queries, but instead of injected data, functions are injected for each mutation. It's that simple!

## Build the ListMovie Component

The `ListMovie` component will be responsible for displaying the list of movies on the landing page. Create a `components/ListMovie.js` file and the following code:

```js

import React from 'react'
import DisplayMovie from './DisplayMovie'
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'
import Nav from './Nav';

class ListMovie extends React.Component {

  render () {

    if (this.props.data.loading) {
      return (<div>Loading</div>)
    }

    return (
      <div>
        <Nav />
        <h3 className="text-center"> Latest Rotten Movie Ratings!</h3>
        <hr/>
        <div className="col-sm-12">
          {this.props.data.allMovies.map((movie, index) => (
            <div className="col-sm-4" key={index}>
              <DisplayMovie key={movie.id} movie={movie} refresh={() => this.props.data.refetch()} />
            </div>
          ))}
        </div>
      </div>
    )
  }
}

const FeedQuery = gql`query allMovies {
  allMovies(orderBy: createdAt_DESC) {
    id
    description
    imageUrl
    avgRating
  }
}`

export default graphql(FeedQuery)(ListMovie)

```

This component above is responsible for displaying the list of movies on the landing page.

This query fetches all the movies from Graphcool and orders them.

```js

const FeedQuery = gql`query allMovies {
  allMovies(orderBy: createdAt_DESC) {
    id
    description
    imageUrl
    avgRating
  }
}`

```

## Set up Apollo Client and Routing

Open up `src/index.js` and modify the code to be like so:

```js

import React from 'react';
import ReactDOM from 'react-dom';
import ListMovie from './components/ListMovie'
import CreateMovie from './components/CreateMovie'
import { Router, Route, browserHistory } from 'react-router'
import ApolloClient, { createNetworkInterface } from 'apollo-client'
import { ApolloProvider } from 'react-apollo'
import 'tachyons'
import './index.css';
import registerServiceWorker from './registerServiceWorker';
import { requireAuth } from './utils/AuthService';


const networkInterface = createNetworkInterface({
  uri: 'https://api.graph.cool/simple/v1/cj4j8xezmtdvv0130l95q2gkk'
})

// For Authentication
networkInterface.use([{
  applyMiddleware (req, next) {
    if (!req.options.headers) {
      req.options.headers = {}
    }
    // get the authentication token from local storage if it exists
    if (localStorage.getItem('id_token')) {
      req.options.headers.authorization = `Bearer ${localStorage.getItem('id_token')}`
    }
    next()
  },
}])

const client = new ApolloClient({
  networkInterface
})

ReactDOM.render((
  <ApolloProvider client={client}>
    <Router history={browserHistory}>
      <Route path='/' component={ListMovie} />
      <Route path='/create' component={CreateMovie} />
    </Router>
  </ApolloProvider>
  ), document.getElementById('root'));
registerServiceWorker();

```

Let's take a good look at the code above. It's very important to understand what is happening here. The call to our GraphQL backend is made possible here.

```js

const networkInterface = createNetworkInterface({
  uri: 'https://api.graph.cool/simple/v1/cj4j8xezmtdvv0130l95q2gkk'
})

```

Replace the `uri` with your Graphcool endpoint. A network interface is created and passed to the Apollo Client. Now, the `ApolloProvider` is wrapping our Router to ensure that all child components can access the functionality from Apollo Client to send queries and perform mutations.

```js
// For Authentication
networkInterface.use([{
  applyMiddleware (req, next) {
    if (!req.options.headers) {
      req.options.headers = {}
    }
    // get the authentication token from local storage if it exists
    if (localStorage.getItem('id_token')) {
      req.options.headers.authorization = `Bearer ${localStorage.getItem('id_token')}`
    }
    next()
  },
}])
```

The code above is for authentication purposes. This ensures that only an authenticated person can perform a certain operation. We'll go into more details in the authentication section.

One more thing. We need to reference bootstrap for styling. Add the link to bootstrap in `public/index.html` file.

```bash
...
<link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
...
```

Modify the `src/App.css` file to contain the code [here](https://github.com/auth0-blog/rottentomatoes-clone/blob/master/src/App.css).

Now, run your app. There should be nothing on the landing page because you haven't added any movies. Head over to the `/create` URL and add a new movie.

![Add movies](https://cdn.auth0.com/blog/rottentomatoes/addmovies.png)

Add a few movies. Now your landing page should look like this:

![List movies](https://cdn.auth0.com/blog/rottentomatoes/list.png)

Awesome! Right now, everyone can view and create movies. Next, let's make sure only authenticated users can add movies.

## Add Authentication to Your App

Auth0 allows us to issue [JSON Web Tokens (JWTs)](https://jwt.io). If you don't already have an Auth0 account, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up</a> for a free one now.

Login to your Auth0 [management dashboard](https://manage.auth0.com) and create a new client. Change the **Client Type** to `Regular Web Application` and **Token Endpoint Authentication Method** to `Basic`. Scroll down to `Advanced Settings > OAuth`, ensure the **JsonWebToken Signature Algorithm** is set to `HS256`.

### Create the Auth Service

We'll create an authentication service to handle everything about authentication in our app. Go ahead and create an `AuthService.js` file inside the `utils` directory.

Before we add code, you need to install `jwt-decode` and `auth0-js` node packages like so:

```bash

npm install jwt-decode auth0-js --save

```

Open up the `AuthService.js` file and add code to it like so:

```js
import decode from 'jwt-decode';
import { browserHistory } from 'react-router';
import auth0 from 'auth0-js';
const ID_TOKEN_KEY = 'id_token';

const CLIENT_ID = '{AUTH0_CLIENT_ID}';
const CLIENT_DOMAIN = 'AUTH0_DOMAIN';
const REDIRECT = 'YOUR_CALLBACK_URL';
const SCOPE = 'openid email profile';
const AUDIENCE = 'https://<AUTH0_DOMAIN>/userinfo';

var auth = new auth0.WebAuth({
  clientID: CLIENT_ID,
  domain: CLIENT_DOMAIN
});

export function login() {
  auth.authorize({
    responseType: 'id_token',
    redirectUri: REDIRECT,
    audience: AUDIENCE,
    scope: SCOPE
  });
}

export function logout() {
  clearIdToken();
  clearProfile();
  browserHistory.push('/');
}

export function requireAuth(nextState, replace) {
  if (!isLoggedIn()) {
    replace({pathname: '/'});
  }
}

export function getIdToken() {
  return localStorage.getItem(ID_TOKEN_KEY);
}

function clearIdToken() {
  localStorage.removeItem(ID_TOKEN_KEY);
}

function clearProfile() {
  localStorage.removeItem('profile');
  localStorage.removeItem('userId');
}

// Helper function that will allow us to extract the id_token
export function getAndStoreParameters() {
  auth.parseHash(window.location.hash, function(err, authResult) {
    if (err) {
      return console.log(err);
    }

    setIdToken(authResult.idToken);
  });
}

export function getEmail() {
  return getProfile().email;
}

export function getName() {
  return getProfile().nickname;
}

// Get and store id_token in local storage
function setIdToken(idToken) {
  localStorage.setItem(ID_TOKEN_KEY, idToken);
}

export function isLoggedIn() {
  const idToken = getIdToken();
  return !!idToken && !isTokenExpired(idToken);
}

export function getProfile() {
  const token = decode(getIdToken());
  return token;
}

function getTokenExpirationDate(encodedToken) {
  const token = decode(encodedToken);
  if (!token.exp) { return null; }

  const date = new Date(0);
  date.setUTCSeconds(token.exp);

  return date;
}

function isTokenExpired(token) {
  const expirationDate = getTokenExpirationDate(token);
  return expirationDate < new Date();
}
```

In the code above, we are using an hosted version of Auth0 in the `login` method and passed in our credentials.

The auth0 package calls the Auth0's `authorize` endpoint. With all the details we passed to the method, our client app will be validated and authorized to perform authentication. You can learn more about the specific values that can be passed to the authorize method [here](https://auth0.com/docs/libraries/auth0js/v8#login).

The parameters that you do not have yet are the `{AUTH0_CLIENT_ID}` and the `{YOUR_CALLBACK_URL}`. Copy the **CLIENT ID** of your newly created client on the Auth0 dashboard and replace it with the value of `AUTH0_CLIENT_ID` in the variable `CLIENT_ID`. Replace your callback url with `http://localhost:3000/callback`. Don't forget to add that to the **Allowed Callback URLs** and `http://localhost:3000` to the **Allowed Origins (CORS)**.

Ensure you also replace the value of `<AUTH0_DOMAIN>` in `const AUDIENCE = 'https://<AUTH0_DOMAIN>/userinfo';` to your auth0 domain.

We also checked whether the token has expired via the `getTokenExpirationDate` and `isTokenExpired` methods. The `isLoggedIn` method returns `true` or `false` based on the presence and validity of a user `id_token`.

Finally, we implemented a middleware, the `requireAuth` method. We'll use this method to protect the `/special` route from being accessed for non-loggedIn users.

Let's go update the `Nav` component to hide/show the `login` and `logout` buttons based on the user's authentication status.

Now, your `Nav` component should look like this:

```js
import React, { Component } from 'react';
import { Link } from 'react-router';
import { login, logout, isLoggedIn } from '../utils/AuthService';
import '../App.css';

class Nav extends Component {

  render() {
    return (
      <nav className="navbar navbar-default">
        <div className="navbar-header">
          <Link className="navbar-brand" to="/">Rotten Tomatoes</Link>
        </div>
        <ul className="nav navbar-nav">
          <li>
            <Link to="/">All Movie Ratings</Link>

          </li>
          <li>
            {
              ( isLoggedIn() ) ? <Link to="/create">Add Movies</Link> :  ''
            }
          </li>
        </ul>
        <ul className="nav navbar-nav navbar-right">
          <li>
            {
             (isLoggedIn()) ? ( <button className="btn btn-danger log" onClick={() => logout()}>Log out </button> ) : ( <button className="btn btn-info log" onClick={() => login()}>Log In</button> )
            }
          </li>
        </ul>
      </nav>
    );
  }
}

export default Nav;
```

We imported `login`, `logout` and `isLoggedIn` functions from the `AuthService`. Then, we attached the `login()` and `logout()` functions to the `login` and `logout` buttons respectively.

We also hid the `/create` link by checking the authentication status of the user via the `isLoggedIn()` function.

### Add A Callback Component

We will create a new component and call it `Callback.js`. This component will be activated when the `localhost:3000/callback` route is called and it will process the redirect from Auth0 and ensure we received the right data back after a successful authentication. The component will store the `id_token`.

_Callback.js_

```js
import { Component } from 'react';
import { withRouter } from 'react-router'
import { getAndStoreParameters, getIdToken, getEmail, getName } from '../utils/AuthService';
import { graphql } from 'react-apollo'
import gql from 'graphql-tag'

class Callback extends Component {

  componentDidMount() {
    getAndStoreParameters();
    this.createUser();
  }

  createUser = () => {
    const variables = {
      idToken: getIdToken(),
      email: getEmail(),
      name: getName()
    }

    this.props.createUser({ variables })
      .then((response) => {
          console.log("Response from create user", response);
          localStorage.setItem('userId', response.data.createUser.id);
          this.props.router.replace('/')
      }).catch((e) => {
        console.error("Error of life ", e)
        this.props.router.replace('/')
      })
  }

  render() {
    return null;
  }
}

const createUser = gql`
  mutation ($idToken: String!, $name: String!, $email: String!){
    createUser(authProvider: {auth0: {idToken: $idToken}}, name: $name, email: $email) {
      id
    }
  }
`

const userQuery = gql`
  query {
    user {
      id
    }
  }
`

export default graphql(createUser, {name: 'createUser'})(
  graphql(userQuery, { options: { fetchPolicy: 'network-only' }})(withRouter(Callback))
)
```

Once a user is authenticated, Auth0 will redirect back to our application and call the `/callback` route. Auth0 will also append the `id_token` to this request, and our Callback component will make sure to properly process and store the token in localStorage. If all is well, meaning we received an `id_token`, we will be redirected back to the `/` page and will be in a logged-in state.

### Add some values to Auth0 Dashboard

Just before you try to log in or sign up, head over to your [Auth0 dashboard](https://manage.auth0.com/#/) and add `http://localhost:3000/callback` to the **Allowed Callback URLs** and `http://localhost:3000` to **Allowed Origins (CORS)**.

### Secure The Special Route

We need to ensure that no one can go to the browser and just type `/create` to access the movie route.

Open up `index.js` and add an `onEnter` prop with a value of `requireAuth` to the `/create` route like so:

```js
....
....
ReactDOM.render((
  <ApolloProvider client={client}>
    <Router history={browserHistory}>
      <Route path='/' component={ListMovie} />
      <Route path='/create' component={CreateMovie} onEnter={requireAuth} />
      <Route path="/callback" component={Callback} />
    </Router>
  </ApolloProvider>
  ), document.getElementById('root'));
registerServiceWorker();

```
_index.js_

Go ahead and login.

![Lock Login Widget](https://cdn.auth0.com/blog/rottentomatoes/login.png)
_Hosted Lock Login Widget_

![Logged in Page](https://cdn.auth0.com/blog/rottentomatoes/loginout.png)
_Logged in Page_

Now you are logged in. Perfect!


We have successfully handled authentication on the frontend, but something is missing. Our GraphQL endpoints are not secured. Anybody can get access to our endpoints and make fetch queries and mutations.

### Secure GraphQL endpoints

Head over to Graphcool. Thankfully, it ships with Permission systems and Auth0. Click on the `User System` as shown below. Also, click on the `Configure Auth Provider` highlighted below:

![Configure Auth Provider](https://cdn.auth0.com/blog/rottentomatoes/configure-auth-provider.png)

Click on Integrations and enable Auth0

![Click on Integrations](https://cdn.auth0.com/blog/rottentomatoes/activate.png)

![Integrations Dialog Box](https://cdn.auth0.com/blog/rottentomatoes/enable-auth0.png)

Copy your Auth0 Client Credentials and paste it here.
![Add Client Details](https://cdn.auth0.com/blog/rottentomatoes/add-client-details.png)

After you are done correctly configuring and integrating Auth0, try to sign up a new user.

A new user will be created in your graphcool dashboard. Remember our createUser mutation? Yes, it creates a new user in the graphcool backend.

Now, head back to graphcool and click on Permissions. We need to restrict permission on the type of user that can perform certain operations.

![Permissions](https://cdn.auth0.com/blog/rottentomatoes/permissions.png)

Right now, everyone can create movies. Let's change that to ensure only authenticated users can!

![Everyone can create movies](https://cdn.auth0.com/blog/rottentomatoes/everyonecreate.png)

Click on the `Everyone` field and update it to make sure only `Authenticated` users can create.

![Update permissions](https://cdn.auth0.com/blog/rottentomatoes/updatepermissions.png)

Now, if a user isn't authenticated, he or she can't create movies.

![Authenticated Users can create movies](https://cdn.auth0.com/blog/rottentomatoes/authenticatedcancreatemovies.png)

Head back to your app. Open up `src/index.js` file and remove the `onEnter` hook for the `/create` route.

Now, run your app and try to create a movie. Aha! Something went wrong, it doesn't work. Check your console.

![Insufficient Permission](https://cdn.auth0.com/blog/rottentomatoes/insufficient-permission.png)

GraphQL has now made it impossible for just any user to create a movie. You have to be authenticated to have the privilege. Let's fix that.

Open the `createMovie.js` file and update it to send the userId of the logged-in user while creating a new movie:

```js
...
state = {
    description: '',
    imageUrl: '',
    avgRating: 0,
    reviewer: localStorage.getItem('userId')
}
...
...
handleMovie = () => {
    const {description, imageUrl, avgRating, reviewer } = this.state
    this.props.addMovie({ description, imageUrl, avgRating, reviewer })
      .then(() => {
        this.props.router.push('/')
    })
}

const addMutation = gql`
  mutation addMovie($description: String!, $imageUrl: String!, $avgRating: Int!, $reviewer: ID!) {
    createMovie(description: $description, imageUrl: $imageUrl, avgRating: $avgRating, reviewerId: $reviewer) {
      id
      description
      imageUrl
      avgRating
    }
  }
`

export default graphql(addMutation, {
  props: ({ ownProps, mutate }) => ({
    addMovie: ({ description, imageUrl, avgRating, reviewer }) =>
      mutate({
        variables: { description, imageUrl, avgRating, reviewer },
      })
  })
})(withRouter(CreateMovie))
```

Now, try to create a movie again. You'll discover that you can create a movie successfully without any issues.

## Conclusion

In this tutorial, we covered how easy it is to build a product with a backend like Graphcool and add authentication to it easily using Auth0.

In addition, Auth0 can help secure your apps with more than just username-password authentication. It provides features like [multifactor auth](https://auth0.com/docs/multifactor-authentication), [anomaly detection](https://auth0.com/docs/anomaly-detection), [enterprise federation](https://auth0.com/docs/identityproviders), [single sign on (SSO)](https://auth0.com/docs/sso), and more. <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">Sign up</a> today so you can focus on building features unique to your app.
