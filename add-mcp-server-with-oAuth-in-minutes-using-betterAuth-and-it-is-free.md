# Create MCP Server with OAuth in minutes

Start with this starter repo:
https://github.com/proSamik/youtube-simple-starter-kit-nextjs/tree/day-23

Use the `day23` branch.

## Installation

Install the following dependencies:

```bash
pnpm add @modelcontextprotocol/sdk
pnpm install @vercel/mcp-adapter
```

## Step 1: Edit auth.ts

In the plugins section of betterAuth, add the MCP plugin:

```typescript
import { mcp } from "better-auth/plugins";   

plugins: [
  // ... other plugins
  mcp({
    loginPage: "/sign-in" 
  })
]
```

## Step 2: Edit Schema.ts

Add the following three tables to your schema:

```typescript
export const oauthApplication = pgTable("oauth_application", {
  id: text("id").primaryKey(),
  name: text("name"),
  icon: text("icon"),
  metadata: text("metadata"),
  clientId: text("client_id").unique(),
  clientSecret: text("client_secret"),
  redirectURLs: text("redirect_u_r_ls"),
  type: text("type"),
  disabled: boolean("disabled"),
  userId: text("user_id"),
  createdAt: timestamp("created_at"),
  updatedAt: timestamp("updated_at"),
});

export const oauthAccessToken = pgTable("oauth_access_token", {
  id: text("id").primaryKey(),
  accessToken: text("access_token").unique(),
  refreshToken: text("refresh_token").unique(),
  accessTokenExpiresAt: timestamp("access_token_expires_at"),
  refreshTokenExpiresAt: timestamp("refresh_token_expires_at"),
  clientId: text("client_id"),
  userId: text("user_id"),
  scopes: text("scopes"),
  createdAt: timestamp("created_at"),
  updatedAt: timestamp("updated_at"),
});

export const oauthConsent = pgTable("oauth_consent", {
  id: text("id").primaryKey(),
  clientId: text("client_id"),
  userId: text("user_id"),
  scopes: text("scopes"),
  createdAt: timestamp("created_at"),
  updatedAt: timestamp("updated_at"),
  consentGiven: boolean("consent_given"),
});

export type oauthApplicationEntity = typeof oauthApplication.$inferSelect;
export type oauthAccessTokenEntity = typeof oauthAccessToken.$inferSelect;
export type oauthConsentEntity = typeof oauthConsent.$inferSelect;
```

## Step 3: Update auth.ts Database Schema

In the `drizzleAdapter` database configuration, add the new tables:

```typescript
import { user as UserSchema, 
  account as AccountSchema, 
  session as SessionSchema,
  verification as VerificationSchema,
  oauthApplication,
  oauthAccessToken,
  oauthConsent
} from "@/lib/db/schema";


database: drizzleAdapter(db, {
  provider: "pg",
  schema: {
    session: SessionSchema,
    verification: VerificationSchema,
    oauthApplication,
    oauthAccessToken,
    oauthConsent
  }
})
```

## Step 4: Generate and Migrate Database

Run the following commands to update your database:

```bash
pnpm db:generate
pnpm db:migrate
```

## Step 5: Create OAuth Routes

In the `app` directory, create two new directories with their respective route files:

### oauth-authorization-server/route.ts

```typescript
import { oAuthDiscoveryMetadata } from "better-auth/plugins";
import { auth } from "../../../lib/auth";
 
export const GET = oAuthDiscoveryMetadata(auth);
```

### oauth-protected-resource/route.ts

```typescript
export async function GET(req: Request) {
  const origin = new URL(req.url).origin;

  return Response.json(
    {
      resource: `${origin}`,
      authorization_servers: [`${origin}`],
    },
    {
      headers: {
        "Content-Type": "application/json",
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "GET, OPTIONS",
        "Access-Control-Allow-Headers": "Content-Type, mcp-protocol-version",
      },
    },
  );
}
```

## Step 6: Add CORS Support

Edit `api/auth/[...all]/route.ts` and add the OPTIONS handler:

```typescript
export async function OPTIONS() {
  return new Response(null, {
    status: 200,
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, POST, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type, Authorization",
    },
  });
}
```

## Step 7: Create Main MCP Route

Create a new directory `api/[transport]/` and add the following `route.ts`:

```typescript
import { auth } from "@/lib/auth";
import { createMcpHandler } from "@vercel/mcp-adapter";
import { z } from "zod";
 
const handler = async (req: Request) => {
  // session contains the access token record with scopes and user ID
  const session = await auth.api.getMcpSession({
    headers: req.headers
  })
  
  if (!session) {
    // this is important and you must return 401
    return new Response(null, {
      status: 401
    })
  }
  
  return createMcpHandler(
    (server) => {
      server.tool(
        "echo",
        "Echo a message",
        {
          message: z.string().describe("The message to echo")
        },
        async (args) => {
          return {
            content: [{ type: "text", text: `Tool echo: ${args.message}` }],
          };
        }
      );
    },
    {
      capabilities: {
        tools: {
          echo: {
            description: "Echo a message",
          },
        },
      },
    },
    {
      basePath: "/api",
      verboseLogs: true,
      maxDuration: 60,
    },
  )(req);
}
 
export { handler as GET, handler as POST, handler as DELETE };
```

# Your MCP server with OAuth authentication is now ready to use.

## 1/ Paste this in Cursor IDE
```
    "mcp-server-localhost": {
      "url": "http://localhost:3000/api/mcp"
    }
```
## 2/ Use mcp inspector directly to test connection

run this in terminal
```bash
npx @modelcontextprotocol/inspector
```
And open the url with pre-filled token

<img width="1095" height="517" alt="Screenshot 2025-08-30 at 17 07 02" src="https://github.com/user-attachments/assets/205ac04d-7f40-4d0b-8f84-bd70146f6aca" />

and then paste this url to MCP Inspector with shown configurations- 
```
http://localhost:3000/api/mcp
```
Press Connect and you're good to go

<img width="765" height="519" alt="Screenshot 2025-08-30 at 17 08 18" src="https://github.com/user-attachments/assets/f3ff4c2a-42a6-48e9-a5ce-50e4b4ce6266" />


Congratulations, you built MCP server with OAuth in Nextjs App router


