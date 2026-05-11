1. Introduction
Solana is one of the highest-throughput blockchains in production, processing thousands of transactions per second. For developers building programs (smart contracts) on Solana, understanding how their deployed code behaves in production is critical — yet surprisingly difficult. Existing tools either require significant technical overhead (writing custom SQL on Dune Analytics), charge premium subscription fees (Nansen, Zettablock), or focus on wallet and token analytics rather than program-level behaviour.

SolScope was conceived as the missing developer tool: a "Google Analytics for Solana programs." A developer should be able to paste their program address and immediately see which instructions are being called most, what the compute unit consumption looks like, how many distinct wallets are interacting, and whether there are failure patterns to investigate.

1.1  Problem Statement
•	No existing tool provides an instant, no-code, per-program usage dashboard for Solana developers.
•	Health scoring — a composite reliability metric for deployed programs — does not exist in any current Solana tooling.
•	Side-by-side program comparison requires writing SQL on Dune or paying for enterprise-tier APIs.
•	All major analytics platforms target traders or researchers, not the developers who built the programs.

1.2  Objectives
•	Build a fully client-side Solana program analytics tool with zero backend infrastructure.
•	Provide instruction frequency breakdown, compute unit distribution, and error rate analysis per program.
•	Implement a composite Health Score (0-100) based on four measurable program dimensions.
•	Enable side-by-side comparison of two programs in a single click.
•	Ensure accessibility for any developer — no signup, no SQL, no cost.

2. Features
2.1  Usage Dashboard
The Usage Dashboard is the core analytical view. Given any Solana program address:
•	Fetches the last 50 transactions involving that program from Helius mainnet.
•	Displays four headline metrics: transactions fetched, success rate, unique callers, and average compute units.
•	Renders an instruction frequency bar chart showing which instruction types are called most (e.g. Transfer, Swap, MintTo).
•	Shows a per-transaction compute unit distribution chart with success/error colour coding.
•	Lists the 20 most recent transactions with clickable Solscan links, decoded instruction names, status badges, and CU values.
•	Instruction names are sourced from the Helius transaction type field, falling back to a custom SPL Token and Anchor discriminator decoder.

2.2  Ecosystem Explorer
The Ecosystem Explorer allows developers to compare two programs side-by-side:
•	Fetches 50 transactions for both programs simultaneously using Promise.all — minimising latency.
•	Displays a two-panel comparison with success rate, average fee, unique callers, error count, and top instruction types.
•	Colour-codes each metric green (better) or red (worse) relative to the competing program.
•	Shows an overall winner card based on a 3-metric composite score.
•	Renders an overlay bar chart comparing fee distributions for both programs.
•	Includes presets: Orca Whirlpool vs Marginfi v2, and SPL Token vs Orca Whirlpool.

2.3  Health Score
The Health Score is SolScope's most distinctive feature — a composite 0-100 score from four pillars:
•	Percentage of transactions completed without error. Success Rate (0-25 pts):
•	Inversely proportional to average fee/CU consumption. Lower fees score higher. Compute Unit Efficiency (0-25 pts):
•	Number of unique wallets relative to total transactions. High diversity indicates broad adoption. Caller Diversity (0-25 pts):
•	Count of distinct instruction byte patterns. Rich instruction sets indicate full feature utilisation. Instruction Variety (0-25 pts):

The score renders as an animated circular dial with a grade badge (Excellent / Good / Fair / Weak / Critical) and contextual alert cards explaining specific findings such as fee spikes, error patterns, or low caller diversity.

2.4  UX Enhancements
•	Each page generates a shareable URL encoding the program address — ?page=health&addr=... — that auto-loads on open. Share Links:
•	Animated shimmer placeholders appear during data fetch. Skeleton Loaders:
•	One-click presets for SPL Token, Orca Whirlpool, and Marginfi v2 for instant demo. Quick Pick Buttons:
•	On page load, the app reads URL parameters and auto-triggers analysis. URL Restoration:
