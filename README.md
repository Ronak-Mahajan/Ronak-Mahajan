### Ronak Mahajan

CS + Math at Vanderbilt, class of 2029. I build quant and ML systems.

**[neural-options-lab](https://github.com/Ronak-Mahajan/neural-options-lab)** ([live demo](https://neural-options-lab.onrender.com)): deep-learning pricer for arithmetic Asian options. 1.3 bp ensemble price RMSE against 200,000-path Monte Carlo references on 600 held-out points; a rough-volatility 0DTE model whose served checkpoint carries a live SPY calibration adopted 2026-08-20, with Deribit BTC surface ingestion and static-arbitrage checks; and a regionally validated neural pricing map that calibrates a live 618-quote SPY capture in about 3 s on a CPU, within 0.046 vol points of the 68 s GPU Monte Carlo fit.

**[hft-lob](https://github.com/Ronak-Mahajan/hft-lob)**: lock-free C++20 order book for NASDAQ ITCH 5.0, measured on a synthetic ITCH stream the binaries generate in process; no recorded market data ships with the repo. On the desktop its README names: 8.5-10.6M messages/second on one core, about 49M through a single demux thread feeding 8 workers, and 113-120M aggregate across 23 workers with the feed pre-split per shard. Throughput is a property of the host as much as of the code, and a second machine measures roughly half of each. Before any scaling number is printed, every instrument's full-band depth is checked level by level against a single-threaded replay.

**[raft-exchange](https://github.com/Ronak-Mahajan/raft-exchange)**: a Raft consensus core written from the paper and verified by deterministic simulation before any networking exists, with a price-time-priority matching engine as the replicated state machine. C++20, CI on three toolchains, and a mutation gate in which all 24 hand-written defects are killed by a named assertion. In progress: the cluster is simulated, not networked.

**[hft-market-maker](https://github.com/Ronak-Mahajan/hft-market-maker)**: a 500-seed paired study of Avellaneda-Stoikov market making on a synthetic mid. Against the fixed-spread benchmark, inventory risk falls 17% and max drawdown 14% at the cost of 36% fewer fills; a four-arm decomposition attributes 12.1 of those 17 points to quoting wider and 4.9 to the inventory skew, and the P&L difference is not significant. The model buys risk reduction with volume, not edge.

**[prediction-markets-lab](https://github.com/Ronak-Mahajan/prediction-markets-lab)**: a self-recording archive of four prediction-market venues (Kalshi, Polymarket, PredictIt, Manifold), snapshotted by a two-hourly cron since 2026-08-23 at a realised median cadence of 3.3 h. The archive replays into a measured answer that the repo regenerates on every run: over the first 125 snapshots (19.9 days, through 2026-09-11), 320 gross monotonicity inversions across 1.0M adjacent Kalshi ladder pairs, of which 3 survive the venue fee model. Gross incoherence is common; net incoherence is rare.

**[crypto-microstructure](https://github.com/Ronak-Mahajan/crypto-microstructure)**: a self-recorded L2 book and trade recorder for Coinbase and Hyperliquid, plus an order-flow-imbalance harness, measured on one free Tardis day (Coinbase BTC-USD, 2026-09-01, 43.7M messages, 86,379 one-second bars). Across four purged walk-forward folds and 69,090 out-of-sample bars, multi-level OFI predicts the next second at OOS R^2 0.0081 (block-bootstrap CI [0.0046, 0.0117]) and is indistinguishable from zero by 30 s. The top decile of |prediction| earns at most 0.26 bps against a cheapest taking cost near 7 bps, so it does not pay through the spread. One day of one pair, and a measured negative.

**[ssda-folio-pipeline](https://github.com/slavesocieties/ssda-folio-pipeline)** and **[ssda-nlp-tools](https://github.com/slavesocieties/ssda-nlp-tools)**: the vision and record-linkage stages that turn a ~750,000-image colonial archive into a queryable database of people, events, and family ties. Sole developer, for Vanderbilt's Slave Societies Digital Archive.

Also built [climateharbor.org](https://climateharbor.org), a climate-data platform with users in 35 countries.

[LinkedIn](https://linkedin.com/in/ronak-mahajan) |
