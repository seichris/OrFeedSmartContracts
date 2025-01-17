# OrFeed Smart Contract

## Decentralized Price Feed for Crypto, Forex, Stocks, ETFs and more.

A highly reliable oracle for Ethereum-based DeFi apps that need financial data from the outside world.

![OrFeed Logo](https://www.orfeed.org/images/orfeed.png)


Website: [orfeed.org](https://www.orfeed.org)

[The Reality Stone on the Blockchain](https://medium.com/proof-of-fintech/the-reality-stone-on-the-blockchain-accessible-to-all-1654a3ec71a7) blog post

[How OrFeed Was Conceived](https://medium.com/proof-of-fintech/introducing-orfeed-aa323342d34c) blog post

Demo: [https://etherscan.io/dapp/0x73f5022bec0e01c0859634b0c7186301c5464b46](https://etherscan.io/dapp/0x73f5022bec0e01c0859634b0c7186301c5464b46)

[Youtube video tutorial](https://youtu.be/LK1BiSveEI4)


## Getting Started

At the top of your smart contract or in a referenced file in your dApp project, include this interface.

```javascript
interface OrFeedInterface {
  function getExchangeRate ( string fromSymbol, string toSymbol, string venue, uint256 amount ) external view returns ( uint256 );
  function getTokenDecimalCount ( address tokenAddress ) external view returns ( uint256 );
  function getTokenAddress ( string symbol ) external view returns ( address );
  function getSynthBytes32 ( string symbol ) external view returns ( bytes32 );
  function getForexAddress ( string symbol ) external view returns ( address );
}
```


To Initialize OrFeed, simply include this code:

```javascript
OrFeedInterface orfeed= OrFeedinterface(0x73f5022bec0e01c0859634b0c7186301c5464b46);

```

One of the best things about OrFeed is that OrFeed automatically detects which kind of asset you are looking for (though the data can come from different providers), as the parameter of "venue" when making the getExchangeRate call. For example, you can get the price for ETH/USD the same way you get the price for JPY/ETH. The 3rd parameter is the venue. Use blank ('') for default oracle. In the future, you can reference several venues/providers to get their data and throw out any that deviate too far from the average.

```javascript
uint jpyusdPrice = orfeed.getExchangeRate("JPY", "USD", "DEFAULT", 100000);
// returns 920 (or $920.00)
```

Note: Replace "DEFAULT" with the oracle provider you would like data from. For example, if you want to know Uniswap's price on the buy side, use "BUY-UNISWAP-EXCHANGE". If you want Kyber's sell side data for the same, you can use "SELL-KYBER-EXCHANGE". Because ERC-20s have many, many integers, when getting prices from token to token, be sure to use very large amounts.... 1000000000 DAI is less than one penny, for example, due to divisibility at 18. 

More examples:

```javascript
uint price = orfeed.getExchangeRate("ETH", "USDC", "BUY-KYBER-EXCHANGE", 100000000000000);
```

```javascript
uint price = orfeed.getExchangeRate("BTC", "DAI", "SELL-UNISWAP-EXCHANGE", 100);
```

```javascript
uint price = orfeed.getExchangeRate("MKR", "EUR", "", 100000000000000);
```


Experimental:


```javascript
uint price = orfeed.getExchangeRate("AAPL", "USD", "PROVIDER1", 1);
```


Stocks and ETFs are provided by a centralized oracle and we are currently working on a register in which requests can go to any provider (centralized or decentralized)... or of course, aggregated with the removal of outliers (recommended). The current contract supports the top 10 equities by market cap (AAPL, AMZN, JNJ etc) and the top 5 ETFs (SPY, etc)

You can run you own oracle by deploying a Node app with the example code in /examples/oracleNodeExampleApp (contract code is in contacts/stockETFPriceContract.sol) and provide as many stocks at the update frequency of your choice. We recommend buying GAS token (we are in no way affiliated and not shilling) to lower your Ethereum fee risk (and so you can pay a reasonable gas price f you plan to run an oracle for a long period of time). Otherwise, you will be exposed to gas fee price risk, or your prices might not update at the frequency you would like.


## Source and Asset Examples (Currently Live)


| Asset       | Example Provider (Venue)           | Type  |
| ------------- |:-------------:| -----:|
| ETH      | DEFAULT | Cryptocurrency |
| BTC      | DEFAULT | Cryptocurrency |
| DAI      | BUY-KYBER-EXCHANGE      |   Token |
| USDC | SELL-UNISWAP-EXCHANGE    |    Token |
| MKR      | DEFAULT | Token |
| KNC      | DEFAULT      |   Token |
| ZRX | DEFAULT    |    Token |
| TUSD | DEFAULT    |    Token |
| SNX | DEFAULT    |    Token |
| CUSDC | DEFAULT    |    Token |
| BAT | DEFAULT    |    Token |
| OMG | DEFAULT    |    Token |
| SAI | DEFAULT    |    Token |
| JPY | DEFAULT    |    Forex |
| EUR | DEFAULT    |    Forex |
| CHF | DEFAULT    |    Forex |
| USD | DEFAULT    |    Forex |
| GBP | DEFAULT    |    Forex |
| AAPL | PROVIDER1    |    Equity |
| MSFT | PROVIDER1    |    Equity |
| GOOGL | PROVIDER1    |    Equity |
| NFLX | PROVIDER1    |    Equity |
| BRK.A | PROVIDER1    |    Equity |
| FB | PROVIDER1    |    Equity |
| BABA | PROVIDER1    |    Equity |
| V | PROVIDER1    |    Equity |
| JNJ | PROVIDER1    |    Equity |
| TSLA | PROVIDER1    |    Equity |
| JPM | PROVIDER1    |    Equity |
| DIS | PROVIDER1    |    Equity |
| SPX | PROVIDER1    |    ETF |
| VOO | PROVIDER1    |    ETF |
| QQQ | PROVIDER1    |    ETF |
| GLD | PROVIDER1    |    ETF |
| VXX | PROVIDER1    |    ETF |



contracts/pegTokenExample.sol contains a template code and live contract reference for a token using OrFeed data that is pegged to the value of an off-chain asset (Alibaba Stock in the example). We are looking forward to less primitive examples that leverage DAOs, advanced collateralization techniques, etc.


## Testing

To test that the contracts are working well in the respective networks, please do the following

1. Install `node.js` in your system/environment, if it is not installed already.
2. Install truffle globall, once `node.js` is done installing i.e. `yarn global add truffle` and then install the project dev-dependencies too i.e. `yarn install`
3. Create a `.secrets` file in the root folder of this project, and paste into it the `mnemonic phrase` of the the wallet you want to use for testing in the respective network i.e. mainnet, kovan or rinkeby.
4. Enter the infura `project-ID` for the infura project you are using to test in either of the networks, in the file `truffle-config.js`.
5. Make sure the wallet has enough eth for testing. Atleast `$5` should be enough for both contract deployment and testing.
6. Finally run either of the following commands to test the contracts, depending on the network,
  - `truffle test --mainnet` for the main ethereum network, be careful though as this will cost you real money.
  - `truffle test --kovan` for the kovan test network.
  - `truffle test --rinkeby` for the rinkeby test network.

### Read the full docs [orfeed.org/docs](https://www.orfeed.org/docs)

Free data is provided by Kyber, Uniswap and Synthetix. 

Premium data is provided as follows:

![How it all fits together](https://www.orfeed.org/images/diagram.png)

### Demo on Testnets
the OrFeed demo already deployed to testnets  
**Kovan**: [0x31a29958301c407d4b4bf0d53dac1f2d154d9d8d](https://kovan.etherscan.io/address/0x31a29958301c407d4b4bf0d53dac1f2d154d9d8d)  
**Rinkeby**: [0x97875355ef55ae35613029df8b1c8cf8f89c9066](https://rinkeby.etherscan.io/address/0x97875355ef55ae35613029df8b1c8cf8f89c9066) 


### Works Provided As Inspiration Of Thought Through Development:

[William George, Clément Lesaege: Smart Contract Oracle for Approximating Real-World, Real Number Values](http://drops.dagstuhl.de/opus/volltexte/2019/11396/pdf/OASIcs-Tokenomics-2019-6.pdf)

[Aragon Network Whitepaper](https://github.com/aragon/whitepaper)

[Vitalik Buterin: Minimal Anti-Collusion Infrastructure ](https://ethresear.ch/t/minimal-anti-collusion-infrastructure/5413)



