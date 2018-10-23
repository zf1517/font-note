graphQL learning note
=====================

GraphQL 是一个用于 API的查询语言，是一个使用基于类型系统来执行查询的服务端运行时。
与传统restful查询相比，GraphQL是在服务端将数据表抽象成一个个type进行组合，使用一次查询就可以获取所有想要的资源，而不需要在web端利用restful API的组合来获取想要的数据（这样得到的数据包含大量冗余，而且占用网络资源大）。GraphQL 这种方式能够将原有 RESTful 风格时的多次请求聚合成一次请求，不仅能够减少多次请求带来的延迟，还能够降低服务器压力，加快前端的渲染速度。

在GraphQL中，我们通过预先定义一张Schema和声明一些Type来达到上面提及的效果，我们需要知道：
*对于数据模型的抽象是通过Type来描述的
*对于接口获取数据的逻辑是通过Schema来描述的

### Schema
GraphQL查询的入口是Schema,Schema是一个大型的TYPE，我们可以将Schema理解为多个Query组成的一张表，其基本形式如下：
```js
schema {
  query: Query
  mutation: Mutation
}
```
Query是查询的入口，Mutation是修改数据的入口。
```js
import {
  GraphQLSchema as Schema,
  GraphQLObjectType as ObjectType,
} from 'graphql';

import me from './queries/me';
import userQueryWhere from './queries/userQuery';

const schema = new Schema({
  query: new ObjectType({
    name: 'Query',
    fields: {
      movies:{
	      	 type: new GraphQLList(MovieType),
	        resolve: _ => {
	          return Movies;
	        }
      	},
      actors:{
	      	type: new GraphQLList(ActorType),
	        resolve: _ => {
	          return Actors;
	        }
      }
    },
  }),
});

export default schema;
```
### TYPE
对于数据模型的抽象是通过Type来描述的，每一个Type有若干Field组成，每个Field又分别指向某个Type。

GraphQL的Type简单可以分为两种，一种叫做Scalar Type(标量类型)，另一种叫做Object Type(对象类型)
```js
const ActorType = new GraphQLObjectType({
  name: 'Actor',
  fields:{
    id: {type: GraphQLInt, },
    name: {type: GraphQLString,},
   }
});
const MovieType = new GraphQLObjectType({
  name: 'Movie',
  fields:{
    id: {type: GraphQLInt,},
    title: {type: GraphQLString,},
    actors: {
      type: new GraphQLList(ActorType),
      resolve: (movie) => {
        const movieActors = movie.actors.map(actorId => Actors.find(o => o.id === actorId))
        return movieActors;
      }
    }
  }),
});
```
### Query
一个基本的query如下：
```js
    {
      movies {
        id
        actors {
          name
        }
      }
    }
```
和普通的函数一样，query可以拥有参数，参数是可选的或需求的。参数使用方法如下:
```js
{
  movies(id: 5) {
    title
  }
}
```
除了参数，query还允许你使用变量来让参数可动态变化，变量以$开头书写，参数可以拥有默认值,使用方式如下：
```js
query GetAuthor($authorID: Int! = 5) {
      author(id: $authorID) {
        name
      }
    }
```
比如说，我们想分别获取ID5和7的movie，我们可以使用别名：
```js
{
      chimezie: movie(id: 5) {
        name
      }
      neo: movie(id: 7) {
        name
      }
}
```
### Mutation
传统的API使用场景中，我们会有需要修改服务器上数据的场景，mutations就是应这种场景而生。mutations被用以执行写操作，通过mutations我们会给服务器发送请求来修改和更新数据，并且会接收到包含更新数据的反馈。mutations和queries具有类似的语法，仅有些许的差别。
```js
  mutation UpdateAuthorDetails($authorID: Int!, $twitterHandle: String!) {
      updateAuthor(id: $authorID, twitterHandle: $twitterHandle) {
        twitterHandle
      }
    }
```
### Resolver
如果我们仅仅在Schema中声明了若干Query，那么我们只进行了一半的工作，因为我们并没有提供相关Query所返回数据的逻辑。为了能够使GraphQL正常工作，我们还需要再了解一个核心概念，Resolver（解析函数）。

GraphQL大体的解析流程就是遇到一个Query之后，尝试使用它的Resolver取值，之后再对返回值进行解析，这个过程是递归的，直到所解析Field的类型是Scalar Type（标量类型）为止。解析的整个过程我们可以把它想象成一个很长的Resolver Chain（解析链）

