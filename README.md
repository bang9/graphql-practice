# GraphQL Practice Repository
Reference(https://www.slideshare.net/deview/112rest-graph-ql-relay)
 
### Tech Stacks
* GraphQL
* Express
* **TO DO**
    * Relay

### GraphiQL
> **You can test your query here** (http://localhost:4000/graphql)
> ```
> var express = require('express');
> var graphqlHTTP = require('express-graphql');
>  
> var app = express();
> app.use('/graphql',graphqlHTTP({
>     schema:schema,
>     pretty:true,
>     graphiql:true,
> }));
>  
> app.listen(4000);
> ```

### GraphQL Basics
* Query
    * GET DATA
* Mutation
    * INSERT DATA
    * DELETE DATA

### GraphQL Type (http://graphql.org/graphql-js/type/)
  * GraphQLSchema
    * query
    * mutation
  * GraphQLObjectType
    * name : String
    * fields : [...]
        * type : GraphQLOutputType
        * resolve : GraphQLResolveFn(source,args,context,info)=>{}
  * GraphQLNonNull(GraphQL Data Type)
  * GraphQL Data Type
      * GraphQLBoolean
      * GraphQLFloat
      * GraphQLID
      * GraphQLInt
      * GraphQLList(Data Type)
      * GraphQLString
      * GraphQLEnumType

<br/>
***
<br/>

## How to make Query

#### Step1 - make a type
```
// construct type(schema) with custom name and fields
var userType = new GraphQLObjectType({
    name:'User',
    fields:()=>({
        id:{ type: GraphQLString },
        name:{ type: GraphQLString },
        age:{ type: GraphQLInt }
    })
});
```

#### Step2 - make a query
```
// resolver function handling DB with data
var query = new GraphQLObjectType({
    name:'Query',
    fields:{
        user:{
            type: userType,
            args: {
                id: { type: new GraphQLNonNull(GraphQLString) },
            },
            resolve: (root,args)=>{
                // Find user in DB
                // and return User
                
                var id = args.id;
                var user = users.find((user)=>{if(user.id === id) return user});
                if(user == null) throw new Error('no user exists with id ' + id);
                
                console.log('GET USER ID : '+JSON.stringify(user));
                return user;
            }
        },
        allUser:{
            type: new GraphQLList(userType),
            resolve: ()=>{
                return users
            }
        }
    }
});
```

### Step3 - serve
```
var schema = new GraphQLSchema({
    query: query,
});
 
var app = express();
app.use('/graphql',graphqlHTTP({
    schema:schema,
    pretty:true,
    graphiql:true,
}));
app.listen(4000);
```

#### Step4 - test query
```
query {
    user(id:"1"){
        name
    }
    allUser{
        id
        name
        age
    }
}
 
 
/* RESPONSE
{
  "data": {
    "user": {
      "name": "Kim"
    },
    "allUser": [
      {
        "id": "0",
        "name": "KANG",
        "age": 24
      },
      {
        "id": "1",
        "name": "Kim",
        "age": 18
      },
      {
        "id": "2",
        "name": "Lyu",
        "age": 27
      },
      {
        "id": "3",
        "name": "Lee",
        "age": 22
      },
      ...
    ]
  }
}
*/
```