# E-commerce FAQ Support Agent (RAG-based Chat)

## The Market Context and Competitive Gap

The "AI Chatbot" craze of 2023–2024 has settled into a functional necessity for e-commerce in 2026. However, the pricing for specialized support bots remains tethered to high-end SaaS models. Chatbase, a leader in this space, charges $\$32$ per month for its "Hobby" tier, which is limited to just 500 messages. For a small Shopify store or an Etsy seller, 500 messages can be exhausted in a single week of moderate traffic. SiteGPT offers a similar entry point at $\$39$ per month.

These tools use "Credit Multipliers" where better models (like Claude 4.5 or GPT-5.2) cost 2-5 credits per message, effectively shrinking the advertised message limit by 80%. There is a significant gap for a "Transparent Pricing" bot that offers unlimited or very high-volume messages for a flat, low fee, specifically targeting small Western e-commerce businesses that have predictable support needs based on their product catalogs.

### Strategic Arbitrage Execution

The Nigerian founder can exploit the "RAG Efficiency" (Retrieval-Augmented Generation). By using GPT-4o-mini and a vector database like Supabase (which has a generous free tier), the founder can store and retrieve store-specific information for a fraction of a cent per query. The value proposition here is "Containment"—the percentage of chats resolved without human intervention. In 2026, industry benchmarks suggest small teams resolve up to 89% of queries using tightly-scoped AI agents. By undercutting the market with a $\$15$ flat fee, the founder captures the "Long Tail" of millions of Shopify stores that currently use no AI or overpay for Chatbase.

### ShopAssist AI

- What it does: A specialized customer support chatbot that integrates with Shopify/WooCommerce to answer product and shipping questions using the store's own documentation.
- Western competitor & their price: Chatbase, $\$32.00$/month (Hobby tier).
- Your arbitrage price: $\$15.00$/month — [53% below competitor].
- Who buys it: US and UK-based dropshippers and small independent brand owners who need 24/7 coverage for basic customer queries.

### API/infrastructure cost per customer/month:
  - Model used: GPT-4o-mini (1 credit equivalent).
  - Estimated tokens/month per user: 100,000 input + 30,000 output (handling ~250 conversations).
  - Cost: $(100,000 \times \$0.00000015) + (30,000 \times \$0.0000006) = \$0.015 + \$0.018 = \$0.033$.
  - Vector DB (Supabase): $\$0.05$ (storage for product data).
  - Hosting: $\$0.05$ (Vercel).
  - Total variable cost: $\$0.133$/customer/month.

- MoR fee per customer/month: $\$1.25$ (5% of $\$15.00$ + $\$0.50$).
- Net profit per customer/month: $\$13.617$.
- Break-even customers for $\$500$/month personal income: 37 customers.
- Build complexity: Low — Standard RAG implementation using LangChain or Vercel AI SDK.
- Nigerian executability: Confirmed executable; e-commerce tools are historically high-performers for remote solo founders.
- One risk to the math: If the user has an extremely large product catalog (e.g., 50,000+ SKUs), the initial "indexing" cost could spike LLM usage for a single month.


{
  "env": {
    "NVIDIA_API_KEY": "YOUR_NVIDIA_API_KEY_HERE"
  },
  "modelProviders": {
    "openai": [
      {
        "id": "minimaxai/minimax-m2.7",
        "name": "NVIDIA MiniMax M2.7",
        "description": "MiniMax M2.7 via NVIDIA API",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 1,
          "top_p": 0.95,
          "max_tokens": 8192
        }
      },
      {
        "id": "qwen/qwen3.5-397b-a17b",
        "name": "NVIDIA Qwen3.5 397B",
        "description": "Qwen3.5 397B via NVIDIA API with thinking enabled",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 0.6,
          "top_p": 0.95,
          "top_k": 20,
          "max_tokens": 16384,
          "presence_penalty": 0,
          "repetition_penalty": 1
        },
        "chatTemplateOptions": {
          "enable_thinking": true
        }
      },
      {
        "id": "qwen/qwen3-next-80b-a3b-instruct",
        "name": "NVIDIA Qwen3 Next 80B A3B Instruct",
        "description": "Qwen3 Next 80B A3B Instruct via NVIDIA API",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 0.6,
          "top_p": 0.7,
          "max_tokens": 4096
        }
      },
      {
        "id": "moonshotai/kimi-k2-instruct",
        "name": "NVIDIA Kimi K2 Instruct",
        "description": "Moonshot AI Kimi K2 Instruct via NVIDIA API",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 0.6,
          "top_p": 0.9,
          "max_tokens": 4096
        }
      },
      {
        "id": "deepseek-ai/deepseek-v3.1-terminus",
        "name": "NVIDIA DeepSeek V3.1 Terminus",
        "description": "DeepSeek V3.1 Terminus via NVIDIA API with thinking enabled",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 0.2,
          "top_p": 0.7,
          "max_tokens": 8192
        },
        "chatTemplateOptions": {
          "thinking": true
        }
      },
      {
        "id": "mistralai/devstral-2-123b-instruct-2512",
        "name": "NVIDIA Devstral 2 123B Instruct",
        "description": "Mistral Devstral 2 123B Instruct via NVIDIA API",
        "envKey": "NVIDIA_API_KEY",
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "generationConfig": {
          "temperature": 0.15,
          "top_p": 0.95,
          "max_tokens": 8192,
          "seed": 42
        }
      }
    ]
  },
  "permissions": {
    "allow": [
      "Bash(tail *)",
      "Bash(ps *)",
      "Bash(grep *)",
      "Bash(echo *)",
      "mcp__playwright__browser_navigate",
      "mcp__playwright__browser_click",
      "Bash(pnpm exec *)",
      "Bash(cat *)",
      "Bash(ls *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "WebSearch",
      "WebFetch(www.grizzlypeaksoftware.com)",
      "WebFetch(ranksquire.com)",
      "WebFetch(vercel.com)",
      "WebFetch(blog.premai.io)",
      "WebFetch(www.vectra.ai)",
      "WebFetch(arxiv.org)",
      "WebFetch(qdrant.tech)",
      "WebFetch(signoz.io)",
      "WebFetch(www.icertglobal.com)",
      "WebFetch(www.aifreeapi.com)",
      "WebFetch(mbrenndoerfer.com)",
      "WebFetch(medium.com)",
      "WebFetch(www.teamday.ai)",
      "WebFetch(theplanettools.ai)",
      "WebFetch(aitestingguide.com)",
      "WebFetch(www.reddit.com)",
      "WebFetch(huggingface.co)",
      "WebFetch(langfuse.com)",
      "Bash(pnpm add *)",
      "Bash(pnpm *)",
      "Bash(sleep *)",
      "Bash(kill *)",
      "Bash(true)",
      "Bash(npx *)",
      "Bash(lsof *)",
      "Bash(xargs *)",
      "Bash(node *)",
      "WebFetch(ncapi.digitalbang.ma)",
      "Bash(pip3 *)",
      "Bash(source *)",
      "Bash(pip list)",
      "Bash(pip install *)",
      "Bash(python *)",
      "Bash(timeout *)",
      "Bash(gtimeout *)",
      "Bash(then)",
      "Bash(fi)",
      "Bash(pkill *)",
      "WebFetch(qwenlm.github.io)"
    ]
  },
  "$version": 3,
  "model": {
    "name": "mistralai/devstral-2-123b-instruct-2512"
  },
  "security": {
    "auth": {
      "selectedType": "openai"
    }
  }
}
