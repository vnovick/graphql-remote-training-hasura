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

# Excerise part 2

- Run Hasura in docker container following this [guide](https://hasura.io/docs/1.0/graphql/manual/deployment/docker/index.html)
- Connect it to existing database in Heroku, that you've just created along with Hasura, 
by inspecting Heroku dashboard and providing correct postgresql url to HASURA_DATABASE_URL
- Install [Hasura CLI](https://hasura.io/docs/1.0/graphql/manual/hasura-cli/hasura.html#hasura)
  - Run `hasura init` to generate hasura configuration and migrations folder
  - Run `hasura console` from directory generated by `hasura init`
  - Add `is_published` field to `posts` table. Notice migrations changes
- Check Heroku hasura instance, see `is_published` is added to articles field. 
- Create a SQL VIEW in SQL tab that will show only blog posts that are published as true
```
create view "publicPosts" as SELECT * from posts where is_published = true;
```

# Excercise part 3 - Event triggers and async flow and Remote schemas

- Go to Event triggers tab and click on Glitch event trigger sample. 
- Deploy this event trigger to Glitch
- Add an Event trigger `insert_user_echo` to trigger and run insert user mutation to trigger Glitch webhook.
- Check in Events tab that an event was triggered
- Add one of your previous lesson example servers as a remote schema. (or add https://graphql-bootcamp-swapi.herokuapp.com/
)

**Homework Bonus:** Add an event trigger for new posts insert to filter out bad words https://github.com/vnovick/bad-words-lambda/blob/master/index.js

# Excercise part 4 - Auth
- Secure Hasura endpoint by setting environment variable HASURA_GRAPHQL_ADMIN_SECRET to any secret of your choice. 
> Note: an app in codesandbox will fail. It's ok for now.

#### Set up Auth0
- Set up Auth0 account and go to [Dashboard](https://manage.auth0.com/).
- Create a new tenant.
- Click on the Applications menu option on the left and then click the + Create Application button.
- In the Create Application window, set a name for your application and select Single Page Web Applications. 

#### Create Auth0 App

In the settings of the application, we will add appropriate (e.g: http://localhost:3000/callback) URLs as Allowed Callback URLs and Allowed Web Origins. We can also add domain specific URLs as well for the app to work. (e.g: https://myapp.com/callback).

#### Create Auth0 rules to add user

Create a new rule in Auth0 for custom claims:
Add custom claims: 
```
function (user, context, callback) {
  const namespace = "https://hasura.io/jwt/claims";
  context.idToken[namespace] = 
    { 
      'x-hasura-default-role': 'user',
      // do some custom logic to decide allowed roles
      'x-hasura-allowed-roles': ['user'],
      'x-hasura-user-id': user.user_id
    };
  callback(null, user, context);
}
```

#### Define JWT config in your Hasura
- Head to 
https://hasura.io/jwt-config
- select Auth0 from the drop down
- copy the config and set it up in Heroku as custom env variable `HASURA_GRAPHQL_JWT_SECRET`

#### Sync new users
Create a new rule in Auth0 for creating new users

```
function (user, context, callback) {
  const userId = user.user_id;
  const nickname = user.nickname;
  
  const admin_secret = "";
  const url = "";
  request.post({
      headers: {'content-type' : 'application/json', 'x-hasura-admin-secret': admin_secret},
      url:   url,
      body:    `{\"query\":\"mutation($userId: String!, $nickname: String) {\\n          insert_users(\\n            objects: [{ id: $userId, name: $nickname }]) {\\n            affected_rows\\n          }\\n        }\",\"variables\":{\"userId\":\"${userId}\",\"name\":\"${nickname}\"}}`
  }, function(error, response, body){
       console.log(body);
       callback(null, user, context);
  });
}
```

- Clone `https://github.com/auth0-samples/auth0-react-samples`
- Change `auth_config.json.example` to `auth_config.json` and put there your DOMAIN and CLIENT_ID from Auth0 Dashboard
- Go to `Profile.js` and change 

```
const { loading, user} = useAuth0();
```
to 

```
const { loading, user, getIdTokenClaims} = useAuth0();
```

Add this:

```
 getIdTokenClaims().then(token => {
    console.log(token)
  })
```
- Copy `__raw` key (this is your JWT token)
- Now you can go to Hasura Console and add the following field
`Authentication` and value will be `Bearer [token]` where `[token]` is your Auth0 JWT token



# Excercise 5 - Permissions

- Head to Hasura console and add `anonymous` role.
- Add `HASURA_GRAPHQL_UNAUTHORIZED_ROLE` specifying which role will be used as `anonymous`
- Set permissions for `anonymous` role to view only published blog posts.
- Set a user group `user` and allow users to see only their own or public blog posts
- Set insert permissions. Whenever a new blog post is inserted get userId from JWT token and insert it


# Homework Bonus:
Business logic techniques
- Write your own syncronous validation resolver that will trigger Hasura mutation whenever validation succeeds.


# Resources

[Learn Tutorials](learn.hasura.io)
[@VladimirNovick](https://twitter.com/VladimirNovick)
[vnovick.com](https://vnovick.com)





