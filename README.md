## Next.js with Supabase.io & TypeScript

https://supabase.io/docs/library/getting-started#usage-with-typescript

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `pages/index.js`. The page auto-updates as you edit the file.

## Usage with TypeScript

Supabase-js ships with type definitions for usage with TypeScript and for convenient IntelliSense autocomplete and documentation in your editor.

When using TypeScript, you can pass the type of database row as a type parameter to the `from` method to get better autocompletion support down the chain. 
If you don't provide a type for the row you need to explicitly pass `from<any>('tableName')`.

```ts
type Message = {
  id: number;
  inserted_at: string;
  message: string;
  user_id: string;
  channel_id: number;
  author: { username: string };
};

const response = await supabase
  .from<Message>('messages') // Message maps to the type of the row in your database.
  .select('*, author:user_id(username)')
  .match({ channel_id: 2 }); // Your IDE will be able to help with automcompletion.
response.body // Response body will be of type Array<Message>.

// If you don't provide a type for the row you need to explicitly pass `from<any>('tableName')`.
const response = await supabase
  .from<any>('messages')
  .select('*, author:user_id(username)')
  .match({ channel_id: 2 });
response.body // Response body will be of type Array<any>.
```

### Generate database types from Swagger OpenAPI specification

Supabase generates a Swagger speficiation file for your database which can be used to generate your data types for usage with TypeScript.

The Swagger specification for your Supabase project can be accessed as follows:

```txt
https://your-project.supabase.co/rest/v1/?apikey=your-anon-key
```

Using the open source [swagger-to-ts](https://github.com/manifoldco/swagger-to-ts#%EF%B8%8F-reading-specs-from-remote-resource) tool you can generate your types and store them locally:

```bash
npx @manifoldco/swagger-to-ts https://your-project.supabase.co/rest/v1/?apikey=your-anon-key --output types/supabase.ts
```

**Note:** Do note that your local types won't automatically stay in sync with your database, so make sure to regenerate your types after your make changes to your database.

After you have generated your types, you can use them in your TypeScript projects:

```ts
import { NextApiRequest, NextApiResponse } from "next";
import { createClient } from "@supabase/supabase-js";
import { definitions } from "../../types/supabase";

const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL,
  process.env.SUPABASE_SECRET_KEY
);

export default async (req: NextApiRequest, res: NextApiResponse) => {
  const allOnlineUsers = await supabase
    .from<definitions["users"]>("users")
    .select("*")
    .eq("status", "ONLINE");
  res.status(200).json(allOnlineUsers);
};
```

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js/) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/import?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/deployment) for more details.
