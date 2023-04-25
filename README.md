# -GraphQL-Misconfigurations

Description :

As new technologies emerge so will new vulnerabilities. By default graphQL does not implement authentication, this is put on the developer to implement. This means by default graphQL allows anyone to query it, any sensitive information will be available to attackers unauthenticated.

Once you find an open graphQL instance you need to know what queries it supports. This can be done by using the introspection system,

Misconfigurations in GraphQL:

Thing to understand here is that GraphQL like any other REST API is vulnerable to many attacks the same attacks the REST API might be prone to. I’ll list some of them below but the most interesting thing and the reason of making this entire post is the infamous Introspection query bug.

Introspection query: Simply put is a way to query the server for its GraphQL back-end schema and to get a complete documentation and list of what API calls are available in the back-end. This is originally meant to be used internally

1. after doing introspection query

![image](https://user-images.githubusercontent.com/84071887/234309226-9dd0c756-ae97-4fa4-9b16-85ab3ef36010.png)

 GraphQL Introspection Query Leaking back-end schema
Just replace the POST contents with the following query:


{"query":"\n    query IntrospectionQuery {\r\n      __schema {\r\n        queryType { name }\r\n        mutationType { name }\r\n        subscriptionType { name }\r\n        types {\r\n          ...FullType\r\n        }\r\n        directives {\r\n          name\r\n          description\r\n          locations\r\n          args {\r\n            ...InputValue\r\n          }\r\n        }\r\n      }\r\n    }\r\n\r\n    fragment FullType on __Type {\r\n      kind\r\n      name\r\n      description\r\n      fields(includeDeprecated: true) {\r\n        name\r\n        description\r\n        args {\r\n          ...InputValue\r\n        }\r\n        type {\r\n          ...TypeRef\r\n        }\r\n        isDeprecated\r\n        deprecationReason\r\n      }\r\n      inputFields {\r\n        ...InputValue\r\n      }\r\n      interfaces {\r\n        ...TypeRef\r\n      }\r\n      enumValues(includeDeprecated: true) {\r\n        name\r\n        description\r\n        isDeprecated\r\n        deprecationReason\r\n      }\r\n      possibleTypes {\r\n        ...TypeRef\r\n      }\r\n    }\r\n\r\n    fragment InputValue on __InputValue {\r\n      name\r\n      description\r\n      type { ...TypeRef }\r\n      defaultValue\r\n    }\r\n\r\n    fragment TypeRef on __Type {\r\n      kind\r\n      name\r\n      ofType {\r\n        kind\r\n        name\r\n        ofType {\r\n          kind\r\n          name\r\n          ofType {\r\n            kind\r\n            name\r\n            ofType {\r\n              kind\r\n              name\r\n              ofType {\r\n                kind\r\n                name\r\n                ofType {\r\n                  kind\r\n                  name\r\n                  ofType {\r\n                    kind\r\n                    name\r\n                  }\r\n                }\r\n              }\r\n            }\r\n          }\r\n        }\r\n      }\r\n    }\r\n  ","variables":null}



Now when you do this The response might be quite big and hard to comprehend. The best way to understand the schema is to visualize it. That can be done by copying the entire response body and using this website https://apis.guru/graphql-voyager/ click on the Change schema button and go in the introspection tab then paste the introspection query there

![image](https://user-images.githubusercontent.com/84071887/234309528-33cb4bcb-bd7a-43a2-a962-4d72d9beedba.png)


Now since we have the entire API calls list we can go over it and easily try to figure out if there are any sensitive API calls that can be abused. This is the most prevalent type of bug found in GraphQL back-ends which can lead to quite critical scenarios.


Video POC attached here :
https://drive.google.com/file/d/1UtspZvhdlDeInEPdQj4p4QOyVX1jLlkm/view
watch this video for clear understanding.

 
 

