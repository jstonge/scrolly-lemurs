## Databases

We started out with a small `csv` file, which we modify as we scroll down. What if our data logic is more complex? Here comes in [Drizzle](https://orm.drizzle.team/docs/overview), which comes as one of the many plug-in options when creating a sveltekit project. Drizzle will let us write `SQL-like` query to manipulate and store the data, depending on user input. Lets look at what has been created

```ts
// drizzle.config
import { defineConfig } from 'drizzle-kit';
if (!process.env.DATABASE_URL) throw new Error('DATABASE_URL is not set');

export default defineConfig({
	schema: './src/lib/server/db/schema.ts',

	dbCredentials: {
		url: process.env.DATABASE_URL
	},

	verbose: true,
	strict: true,
	dialect: 'sqlite'
});

```

```ts
// src/lib/server/db/index.ts
import { drizzle } from 'drizzle-orm/libsql';
import { createClient } from '@libsql/client';
import { env } from '$env/dynamic/private';
if (!env.DATABASE_URL) throw new Error('DATABASE_URL is not set');
const client = createClient({ url: env.DATABASE_URL });
export const db = drizzle(client);
```

```ts
// src/lib/server/db/schema.ts
import { drizzle } from 'drizzle-orm/libsql';
import { createClient } from '@libsql/client';
import { env } from '$env/dynamic/private';
if (!env.DATABASE_URL) throw new Error('DATABASE_URL is not set');
const client = createClient({ url: env.DATABASE_URL });
export const db = drizzle(client);
```



```svelte
[After sticky sequence]

<h2>Offering the choice</h2>

<label for="major-cat-select">Major Cat</label>
<select id="major-cat-select" multiple>
    <option value="lion">Lion</option>
    <option value="tiger">Tiger</option>
    <option value="leopard">Leopard</option>
    <option value="cheetah">Cheetah</option>
    <option value="panther">Panther</option>
</select>

```

```diff
+<style>
+#major-cat-select {
+    width: 200px;
+            height: 120px;
+            font-size: 14px;
+            border: 1px solid #ccc;
+            border-radius: 8px;
+            padding: 4px;
+        }
+        label {
+            display: block;
+            margin-bottom: 6px;
+            font-weight: bold;
+        }
+</style>
```