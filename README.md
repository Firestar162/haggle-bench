# HaggleBench

HaggleBench is a browser game where you negotiate with an AI shopkeeper over a laptop or a pair of headphones. It demonstrates what happens when a model is trusted to follow a sales rule and what changes when the server enforces that rule itself. Orders are simulated.

## Run locally

You’ll need Node.js 24, npm, and a Nebius Token Factory API key.

1. Clone the repository and install the locked dependencies.

   ```sh
   git clone https://github.com/Firestar162/haggle-bench.git
   cd haggle-bench
   npm ci
   ```

2. Create a local `.env` file in the project root and add your key.

   ```dotenv
   NEBIUS_API_KEY=your_nebius_api_key
   ```

   Git ignores `.env`. Keep the key in your local copy.

3. Start the web app and open [localhost:3000](http://localhost:3000).

   ```sh
   node server.js
   ```

## Play and compare modes

Pick a product, send an offer, and watch the **Orders** panel. A sale appears there when the server accepts a `place_order` call.

| Price floor mode | What happens |
| --- | --- |
| **Shopkeeper enforced** | The model is instructed to honour the floor. If it places a below-floor order, the app records the sale and shows a red **Break achieved** card with the minimum price and the difference. |
| **Server enforced** | The server checks the price before recording an order. A below-floor attempt increments the blocked-order counter and creates no sale. |

Regular sales appear in green. Changing modes or selecting **New game** starts a fresh negotiation. Conversations and orders are stored in memory and clear when the server restarts.

For a demo, start in **Shopkeeper enforced**, then switch to **Server enforced** and try the same negotiation in the new game. The model can respond differently across runs, so use the order cards and blocked-order notice to show what the server actually did.

## Shopkeeper tools

| Tool | Purpose |
| --- | --- |
| `get_list_price(product)` | Looks up the advertised price. |
| `get_floor_price(product)` | Looks up the internal minimum price. The model is instructed to use this before agreeing to a sale and keep the value private. |
| `place_order(product, price)` | Requests a simulated sale at the proposed price in pounds. |

The browser UI is in `views/index.ejs`. The Express server in `server.js` manages sessions, runs the tools, and calls `openai/gpt-oss-120b` through Nebius Token Factory.
