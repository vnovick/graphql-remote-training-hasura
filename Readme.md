# Hasura engine workshop

In this workshop we will learn how to use Hasura and what benefits it will bring for developing our GraphQL API.

This repo follows remote training. Excercises will be added as we follow along during the training. 

These are the topics that we will cover
- Hasura Basics
- Using Hasura with new or existing Postgres
- Authorization
- Authentication
- Custom business logic
- Remote schemas and event triggers

# Excercise 1

In this excercise we will explore how to set Hasura app, explore console and auto generate GraphQL API. 

- Get started with Hasura by setting it up on Heroku. 
  - Head to [hasura.io](https://hasura.io) and click on Get started with Heroku
  - Click on "Deploy to Heroku" button to deploy engine along with PostgreSQL addon
- Create blogs api
  - create `posts` table with `title`, `content`, `timestamp`, `user_id` fields
  - create `users` table with `name` field. (id should be `Text` type)
  - create relationships between these tables. 
- Connect frontend
  - Clone this [codesandbox](https://codesandbox.io/s/hasura-workshop-excercise3-5bo4g) and update it to get data from Hasura endpoint. 
- Add one of your previous lesson example servers as a remote schema.

