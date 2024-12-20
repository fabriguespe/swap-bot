# Base Agent

Message or add [`baseagent.converse.xyz`](https://converse.xyz/dm/baseagent.converse.xyz)

## Prompt

```jsx
import { run, HandlerContext } from "@xmtp/message-kit";
import { agentRun } from "@xmtp/message-kit";
import { skills } from "./skills.js";
import { defaultPromptTemplate } from "@xmtp/message-kit";

export async function agent_prompt(senderAddress: string) {
  let fineTuning = `
  ## Example response:

  1. When user wants to swap tokens:
    Hey {PREFERRED_NAME! I can help you swap tokens on Base.\nLet me help you swap 10 USDC to ETH\n/swap 10 usdc eth

  2. When user wants to swap a specific amount:
    Sure! I'll help you swap 5 DEGEN to DAI\n/swap 5 degen dai

  3. When user wants to pay a specific token:
    I'll help you pay 1 USDC to 0x123...\n/pay 1 {TOKEN} 0x123456789...
    *This will return a url to pay

  4. If the user wants to pay a eth domain:
    I'll help you pay 1 USDC to vitalik.eth\nBe aware that this only works on mobile with a installed wallet on Base network\n/pay 1 vitalik.eth
    *This will return a url to pay

  5. If the user wants to pay a username:
    I'll help you pay 1 USDC to @fabri\nBe aware that this only works on mobile with a installed wallet on Base network\n/pay 1 @fabri
    *This will return a url to pay

  6. When user asks about supported tokens:
    can help you swap or pay these tokens on Base:\n- ETH\n- USDC\n- DAI\n- DEGEN\nJust let me know the amount and which tokens you'd like to swap or send!

  7. When user wants to tip default to 1 usdc:
    Let's go ahead and tip 1 USDC to nick.eth\n/pay 1 usdc 0x123456789...

  8. If the users greets or wants to know more or what else can he do:
    I can assist you with swapping, minting, tipping, dripping testnet tokens and sending tokens (all on Base). Just let me know what you need help with!.

  9. If the user wants to mint they can specify the collection and token id or a Url from Coinbase Wallet URL or Zora URL:
    I'll help you mint the token with id 1 from collection 0x123456789...\n/mint 0x123456789... 1
    I'll help you mint the token from this url\n/url_mint https://wallet.coinbase.com/nft/mint/eip155:1:erc721:0x123456789...
    I'll help you mint the token from this url\n/url_mint https://zora.co/collect/base/0x123456789/1...

  10. If the user wants testnet tokens and doesn't specify the network:
    Just let me know which network you'd like to drip to Base Sepolia or Base Goerli?

  11. If the user wants testnet tokens and specifies the network:
    I'll help you get testnet tokens for Base Sepolia\n/drip base_sepolia 0x123456789...
`;

  return defaultPromptTemplate(fineTuning, senderAddress, skills, "@base");
}
```

## Skills

```jsx
import type { SkillGroup } from "@xmtp/message-kit";
import { handler as baseHandler } from "./handler.js";

export const skills: SkillGroup[] = [
  {
    name: "Swap Bot",
    tag: "@base",
    description: "Swap bot for base.",
    skills: [
      {
        skill: "/swap [amount] [token_from] [token_to]",
        examples: ["/swap 10 usdc eth", "/swap 1 dai usdc"],
        handler: baseHandler,
        description: "Exchange one type of cryptocurrency for another.",
        params: {
          amount: {
            default: 10,
            type: "number",
          },
          token_from: {
            default: "usdc",
            type: "string",
            values: ["eth", "dai", "usdc", "degen"], // Accepted tokens
          },
          token_to: {
            default: "eth",
            type: "string",
            values: ["eth", "dai", "usdc", "degen"], // Accepted tokenss
          },
        },
      },
      {
        skill: "/drip [network] [address]",

        handler: baseHandler,
        examples: [
          "/drip base_sepolia 0x123456789",
          "/drip base_goerli 0x123456789",
        ],
        description:
          "Drip a default amount of testnet tokens to a specified address.",
        params: {
          network: {
            default: "base",
            type: "string",
            values: ["base_sepolia", "base_goerli"],
          },
          address: {
            default: "",
            type: "address",
          },
        },
      },
      {
        skill: "/url_mint [url]",

        handler: baseHandler,
        examples: ["/url_mint https://zora.co/collect/base/0x123456789/1..."],
        description:
          "Return a Frame to mint From a Zora URL or Coinbase Wallet URL",
        params: {
          url: {
            type: "url",
          },
        },
      },
      {
        skill: "/mint [collection] [token_id]",
        examples: ["/mint 0x73a333cb82862d4f66f0154229755b184fb4f5b0 1"],

        handler: baseHandler,
        description: "Mint a specific token from a collection.",
        params: {
          collection: {
            default: "0x73a333cb82862d4f66f0154229755b184fb4f5b0",
            type: "string",
          },
          token_id: {
            default: "1",
            type: "number",
          },
        },
      },
      {
        skill: "/pay [amount] [token] [username]",

        examples: ["/pay 10 vitalik.eth"],
        description:
          "Send a specified amount of a cryptocurrency to a destination address.",
        handler: baseHandler,
        params: {
          amount: {
            default: 10,
            type: "number",
          },
          token: {
            default: "usdc",
            type: "string",
            values: ["eth", "dai", "usdc", "degen"], // Accepted tokens
          },
          username: {
            default: "",
            type: "username",
          },
        },
      },

      {
        skill: "/show",

        examples: ["/show"],
        handler: baseHandler,
        description: "Show the base url",
        params: {},
      },
    ],
  },
];
```

---

Made with ❤️ by [Ephemera](https://ephemerahq.com).
