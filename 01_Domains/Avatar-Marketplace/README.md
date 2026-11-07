---
title: Avatar Marketplace Domain
tags:
  - domain
  - avatar-marketplace
aliases:
  - Avatar Marketplace
---

# Avatar Marketplace Domain

> [!important] Agreed 2026-11-07 — implementation pending
> [[01_Domains/Avatar-Marketplace/Avatar-Marketplace-Spec|Detailed Vietnamese MVP specification]] is the source of truth. Decision 26 supersedes the old Marketplace exclusion; this is approved future scope, not a claim that application main already implements it.

Candidates and Recruiters list their own distributable avatars for Admin review, browse approved listings, and purchase a copy into their own inventory using coins. Seller retains the original and receives the full price; no fee or cash-out in MVP. Admin moderates listings, without owning an avatar inventory or wallet.

Add only avatar_assets and avatar_listings; avatars gains asset_id and moves model_path into assets. No preview_path/source, purchase/entitlement table, purchase_transaction_id, or Marketplace-specific transaction columns. Existing transactions records from/to/amount/status/currency and a human-readable purchase description.

Inventory ownership grants normal use. A new purchase creates an owned avatar row atomically with buyer debit/seller credit and ledger recording. Retry never charges/grants twice. Deleting the bought avatar does not retain a free restore entitlement; a later new purchase requires a new intent/key. Seller delist/source deletion does not remove buyer rows or shared files still referenced.

Ten conceptual capabilities are agreed: seven buyer/seller capabilities and three Admin moderation capabilities. Existing inventory/setup/history are extended, not duplicated. No per-listing sales analytics, individual revoke/refund, resale, gifting, NFT or cash-out. Formal UC IDs/count remain unchanged until documentation reconciliation.

Related: [[01_Domains/Avatar-Voice/README|Avatar inventory]], [[01_Domains/Payment/README|coins/transactions]], [[03_Decisions/Product-Decisions|Decision 26]].
