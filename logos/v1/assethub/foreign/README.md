# Asset Hub foreign assets

Foreign assets are keyed by their complete XCM location, not by symbol. Symbols
are not unique: for example, MYTH, XRT, and NEURO each have more than one
registered location.

Icon URLs use this form:

```text
/v1/assethub/foreign/{location-key}/icon.png
```

The canonical key is built in XCM junction order:

```text
p{parents}-ethereum-{chain-id}[-account-{account-key-20}]
p{parents}-kusama
p{parents}-parachain-{para-id}[-pallet-{instance}|-index-{index}|-key-{bytes}]
```

`manifest.json` is the lookup table from the canonical key to the on-chain
metadata and normalized XCM location.

Token artwork is sourced from the public
[SubWallet ChainList](https://github.com/Koniverse/SubWallet-ChainList)
registry. The exact-contract sUSDe and LBTC artwork is sourced from
[CoinGecko](https://www.coingecko.com/).
