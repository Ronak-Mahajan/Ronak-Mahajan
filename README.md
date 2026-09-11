### Ronak Mahajan

CS + Math at Vanderbilt, class of 2029. I build quant and ML systems.

**[neural-options-lab](https://github.com/Ronak-Mahajan/neural-options-lab)** ([live demo](https://neural-options-lab.onrender.com)): deep-learning pricer for arithmetic Asian options. 1.4 bp RMSE against 200,000-path Monte Carlo references on 600 held-out points; a rough-volatility 0DTE model whose served checkpoint carries a live SPY calibration adopted 2026-08-20, with Deribit BTC surface ingestion and static-arbitrage checks; and a certified neural pricing map that calibrates a live 618-quote SPY capture in about 3 s on a CPU, within 0.046 vol points of the 68 s GPU Monte Carlo fit.

**[hft-lob](https://github.com/Ronak-Mahajan/hft-lob)**: lock-free C++20 order book for NASDAQ ITCH 5.0. 10M+ messages/second on one core, 110M+ across 23 cores via wait-free sharding (with the feed pre-split per shard; about 49M through a single demux thread), verified byte-for-byte against a sequential reference.

**[raft-exchange](https://github.com/Ronak-Mahajan/raft-exchange)**: a Raft consensus core written from the paper and verified by deterministic simulation before any networking exists, with a price-time-priority matching engine as the replicated state machine. C++20, CI on three toolchains.

**[hft-market-maker](https://github.com/Ronak-Mahajan/hft-market-maker)**: a 500-seed paired study of Avellaneda-Stoikov market making. Inventory risk falls 17% and max drawdown 14%, at the cost of 36% fewer fills; the model buys risk reduction with volume, not edge.

**[prediction-markets-lab](https://github.com/Ronak-Mahajan/prediction-markets-lab)**: a self-recording archive of four prediction-market venues (Kalshi, Polymarket, PredictIt, Manifold), snapshotted every two hours since 2026-08-23, with coherence screens reported net of venue fees. In progress; the results wait on the archive.

**[crypto-microstructure](https://github.com/Ronak-Mahajan/crypto-microstructure)**: a self-recorded L2 book and trade recorder for Coinbase and Hyperliquid, plus an order-flow-imbalance harness (book rebuild, CKS OFI, walk-forward OLS). In progress; no results yet.

**[ssda-folio-pipeline](https://github.com/slavesocieties/ssda-folio-pipeline)** and **[ssda-nlp-tools](https://github.com/slavesocieties/ssda-nlp-tools)**: the vision and record-linkage stages that turn a 750,527-image colonial archive into a queryable database of people, events, and family ties. Sole developer, for Vanderbilt's Slave Societies Digital Archive.

Also built [climateharbor.org](https://climateharbor.org), a climate-data platform with users in 35 countries.

[LinkedIn](https://linkedin.com/in/ronak-mahajan) |
