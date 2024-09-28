## はじめに

src/coffees/coffees.graphqlのファイルを作成

```graphql
type Coffee {
  id: ID! // ID (unique identifier) - non-nullable (required)
  name: String! // String - non-nullable (required)
  brand: String! // String - non-nullable (required)
  flavors: [String!]! // non-nullable Array of non-nullable String's
}

type Query {
  coffees: [Coffee!]!
}
```

// ◾️ Terminal - let's install a dependency
npm i ts-morph
// ◾️ Terminal - let's run our generate-types Script
npx ts-node src/generate-types

src/graphql.tsのファイルが自動生成されて以下が出力される

```ts
/*
 * -------------------------------------------------------
 * THIS FILE WAS AUTOMATICALLY GENERATED (DO NOT MODIFY)
 * -------------------------------------------------------
 */

/* tslint:disable */
/* eslint-disable */

export class Coffee {
  id: string;
  name: string;
  brand: string;
  flavors: string[];
}

export abstract class IQuery {
  coffees: Coffee[];
}

type Nullable<T> = T | null;
```

code-firstだとentityが行っていたものを自動的に生成される

generate-types.tsを変更

```ts
import { GraphQLDefinitionsFactory } from '@nestjs/graphql';
import { join } from 'path';

const definitionsFactory = new GraphQLDefinitionsFactory();
definitionsFactory.generate({
  typePaths: ['./**/*.graphql'],
  path: join(process.cwd(), 'src/graphql.ts'),
  outputAs: 'class',
  watch: true,
  skipResolverArgs: true,
  defaultTypeMapping: {
    ID: 'number', // IDをnumber型にする
  },
});
```

```ts
/*
 * -------------------------------------------------------
 * THIS FILE WAS AUTOMATICALLY GENERATED (DO NOT MODIFY)
 * -------------------------------------------------------
 */

/* tslint:disable */
/* eslint-disable */

export class Coffee {
  id: number;
  name: string;
  brand: string;
  flavors: string[];
}

export abstract class IQuery {
  coffees: Coffee[];
}

type Nullable<T> = T | null;
```

// ◾️ Terminal
nest g module coffees
nest g resolver coffees

// 📝 CoffeesResolver
import { Resolver } from '@nestjs/graphql';

@Resolver()
export class CoffeesResolver {
@Query('coffees')
async findAll(): Promise<Coffee[]> {
return [];
}
}

// 🌚 Insomnia - query Demo
{
coffees {
id
name
brand
flavors
}
}

code-fristはエンティティを定義してnestを起動するとschema.gqlを更新する

schema-firstはgraphqlを定義してnpx ts-node src/generate-typesを実行することによってschema.gqlを生成する

code-firstとschema-firstの違いはまずはそこから
