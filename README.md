# Yieldster Intro

The Yieldster project is an ambitious DeFi infrastructure framework that has the potential to dramatically change addoption within the DeFi space. The project is currently in stealth mode but we expect to go public at the End of January. In the meantime we have decided to release some of the tools we are using inter nally to the community if there is enough interest.

# Yieldster Vault

The yieldster vault is a framework, consisting of smart contract snippets, templates and tools, that allow anyone to create yEarn-style vaults, design or choose a strategy, apply the strategy to the vault, decide who can deposit or use the vault and which assets & protocols are allowed in the vault and then finally deploy it. It also provides a dashboard to track the progress, manage approved assets and manage whitelisting.

We envision family members creating vaults for their family, investment clubs building vaults for members, financial advisors creating vaults for their clients, hedge funds and other institutional creating vaults for specific markets and investors etc. The possibilities are endless.

To accomplish this we will have to make the process of creating and deploying the vault contract as simple and intuitive as possible. 

The framework provides a dashboard that allows vault creators and managers to add approved assets or protocols to the vault, manage whitelists and track progress and success of the vault.

Just like in a yEarn-style vault the vault will accept deposits in ERC20 compatible tokens (may ETH as well) and issue token representing a share in the vaults total value.

Vault creators can either use the composer to create sophisticated strategies in a drag-and-drop graphical UI incorporating any of the approved assets and protocols or can subscribe to strategies created by other users (if the creator provided for others to use their strategy) or even list their own strategies for others to subscribe to. While initially we will not provide a means to charge for subscriptions our ultimate goal is to allow strategy creators to also be able to charge a subscription fee for the use of their strategy.

The framework also includes a simplified way for the vault to locate, access and use data that is needed to track and evaluate approved assets. The data module/contract will enable the vault to easily access all of the relevant information from safe & reliable and approved data sources and will allow developers to provide portfolio allocation information that was generated off-chain (e.g. Off-chain AI) as an oracle. This model allows for maximum flexibility while at the same time ensuring that only safe data sources are being used in the validation of assets or strategies. This model also allows the handling of compute-intensive processes to be handled off-chain and then fed back either via oracle or regular web api (depending on how.

The yieldster vault framework consists of the following components:

## Composer

The composer is a graphical user interface to design vault strategies using the asset and protocol primitives as defined in the AP contract. We envision a drag and drop interface that allows a user to visually combine AP primary actions into complex strategies.

One such primary action could be for example “deposit.” To use a deposit function in the vault the strategy designer simply drags the primary action on the canvass and then sets the properties to the protocol or asset to be used. The AP contract has instructions and contract snippets that determine how to interact with the specified asset or protocol.

Only the assets, protocols or strategies the strategy designer is whitelisted for will be available within the composer.

Once a strategy has been designed the composer will build the smart contract by combining the smart contract snippets from the library and the Yieldster vault template smart contract and sends it to the factory contract.

The factory contract will then in turn deploy the contract and register with the APS contract so that the strategy can be used as a primitive in other strategies. User generated strategies can only be added to whitelists that are managed by the strategy designer. If  a strategy designer wants to offer the strategy for subscriptions or for use by third parties within the composer, they will have to make a proposal to the Yieldster DAO and if the community approves to add the protocol to the platform the yieldster admins will bring it on the platform and may remove any whitelisting restrictions.

The composer will also provide a marketplace for pre-built strategies and financial primitives that can be applied to a vault. While not strictly necessary in the first phase, we envision strategy designers to be able to charge a one-time or monthly recurring fee for strategies that have been accepted into the platform.

Once deployed the vault is ready to receive deposits.

In later iterations of the composer we will also include sophisticated risk analytics and simulation capabilities to allow strategy developers to better understand the impact different market movements have on a given strategy.

The composer will also allow the strategy results (e.g. Portfolio allocation) to be oraclized, so that it can be consumed via the data module within the vault contract. 

## Whitelisting 

The whitelisting contract allows to restrict access to APS to certain groups of users. At a minimum we will keep track of:

Whitelisting groups
* Name of group
* Owner of group
* Admin of group (add/remove/block web3 interface)
* Members

“None” is a special group that would allow a contract to interact with anyone 

The whitelisting contract can then be imported into any smart contract. A smart contract using the whitelisting contract will set the whitelists it is subscribed to and provide a user (contract) address to verify and will return wether the address is subscribed to a whitelisting group. This allows the smart contract to decide whether to continue to execute or fail the transaction.

To associate the contract with a specific group the contract will simply subscribe to that particular group.(the contract assignment only happens within the consuming contract not in the whitelisting contract)

Whitelisting should allow to whitelist the entire contract, only specific functions (e.g. read, transfer, mint, etc) or even granular to each exposed function of the contract. 

## Assets, Protocols & Strategies (AP contract)

A smart contract that keeps track of all the pertinent information of an asset or protocol. This information includes, but is not limited to:

* Underlying assets
* Date sources
* Contract addresses (underlying assets, protocol) 
* Indicators (e.g. moving averages, volatility, volume, trend)
* Risk rating
* Risk sensibility (what do we need to watch for when tracking this asset or protocol - e.g. debt ratio, liquidation price etc.
* Actions available: farm, harvest, lend, borrow, withdraw, deposit, etc. and which library snippet is responsible for executing that action
* Which whitelisting group does the asset belong to
* Fees (and estimated gas costs)

As malformed assets and contracts can pose a substantial risk to the entire yieldster framework, at least initially, we will only allow yieldster admins to add and manage assets and protocols.

For an asset or protocol to be allowed to be used within the yieldster framework the asset must be registered with this contract and provide information on how to track relevant information and how to interact with the asset or protocol.

To simplify management & use, assets and protocols can be grouped so that a subscribing contract can subscribe to multiple assets with one call. Assets or protocols can be members of multiple groups.

Any contract in yieldster vault framework must subscribe to at least one asset to be able to hold any value

Initially we will support the following protocols and farms:

MakerDao, yEarn, Compound, Aave, dYdX, Fulcrum, Bitfinex, Balancer, Curve, Uniswap, Harvest 

(The APS contract also keeps track of the Yieldster vault as an asset or protocol e.g. contract addresses etc)

## Data 

DeFi strategies depend on reliable and timely data, but finding reliable data source can be challenging. The yieldster framework will provide easy access to relevant data - as defined in APS contract).

Data access will be available both via web api and via oracles (e.g chainlink)

The data object can also be used to provide access to portfolio allocations that have been developed off-chain. This allows strategy designers to build AI and machine learning based portfolio allocation strategies that otherwise would be too compute intensive to be done on-chain.

## Vault contract template

The yieldster vault template is implementing a “standard” yEarn-style vault contract that allows for the following:

* Deposit (approved) assets (The vault contract should always be able to hold multiple assets
* Mint vault tokens when assets are deposited representing share of assets held in the contract
* Burn vault tokens when assets are withdrawn
* Withdraw assets
* Time-lock assets (eg. 0-180 days)
* Provides “standard” interface for ERC20 tokens and vault specific details (eg TVL)
* Implements Whitelisting and APS subscriptions as wells as Data access
* Fees (success, management, fixed)

The vault contract also exposes a standard interface that can be used to display vault information in the dashboard and composer.

## Factory Contract

The factory contract will take the yieldster vault contract and combine it with the results of the composer to create the final vault contract. It will then deploy the contract and register the contract address with the APS contract.

## Wrapper (time-lock & interest bearing)

The wrapper contract is itself a yieldster vault contract that implements a time-lock and allows the asset to be used for simple interest bearing investments. The yieldster vault framework will provide standard wrappers for supported assets that automatically switch to the highest interest carrying financial primitive.

In addition the wrapper will also allow the asset to be locked for a specified period of time during which the asset cannot be withdrawn.

Each wrapper can only hold one asset (eg ETH), so for each supported asset we will provide a dedicated interest bearing wrapper.

Just like all of the vault contracts the wrapper contract can subscribe to whitelist groups or can be accessible to anyone.
We will  be using the interest bearing versions of assets as financial primitives within the strategies that can be developed using the composer. The wrappers can of course also be used to park assets.

Initially we will support the following assets:

DAI, USDC, ETH, WBTC, USDT, MKR, ZRX, COMP, LINK, PAX, SNX, SUSD, TUSD, UNI, YFI

## Library

The library consists of a series of single task and protocol specific deployed smart contracts and code snippets that can be used by the composer and factory contract to build the final vault contract.

Example: 

“Deposit.balancer” could be a contract that allows to deposit assets into a balancer pool, while deposit.curve would implement deposits into curve pools.
To ensure that all protocols expose the same interface to the composer and factory contract we will wrap the protocol specific interface in a yieldster specific wrapper contract that also implements whitelisting.

Supported tasks could be deposit, withdraw, farm, harvest (rewards), lend, borrow, flashloan, 

The design of each library contract should be as narrow and atomic as possible and feasible.


## Dashboard

Interface to manage different aspects of the yieldster vault environment:

### Investors
* Vault view (shows what’s happening in a specific vault)
* Portfolio view (shows vaults & assets in which the user is invested in)
* Explore (shows APS for which the investor is whitelisted)
* Deposit/Withdraw
* Reports

### Users/Vault owners
* Whitelist groups (create, manage)
* Add/Delete addresses to groups
* Close vault (vault can only be closed once all assets have been withdrawn)
* Close deposits (prevents new assets from being deposited)
* Emergency divest: allows vault admin to withdraw investments from underlying APS into the vault. It does not allow for assets to be withdrawn from the vault contract

### Yieldster Admins
* Whitelist groups (create, manage)
* Add/Delete addresses to groups
* Close vault (vault can only be closed once all assets have been withdrawn)
* Close deposits (prevents new assets from being deposited)
* add/delete/pause approved APS
* Summary view of all vaults

Important: admins can only view/manage vaults for which they have been whitelisted or which they own and only if the vault contract specifies them as admin for the vault

## Order Balancer

Yieldster Framework utilises the order balancer to to exchange tokens. Order balancer enables users to transact between CEX and DEX by utilising the CEX2DEX bridge. Order Balancer is an extended version of 0x protocol and utilises https://0x.org/extensions to make modifications to order routing in the relayer. Order Balancer utilises both on and off chain definitions of 0x on transactions. 

### CEX-DEX Bridge

The order balancer comprises a bridge connector that allows the order balancer protocol to trade with centralized exchanges. This shall be an interface that allows for DEX to arbitrage against liquidity available on CEX.  For each CEX that is going to be integrated with order balancer relayer a cex specific bridge contract that allows for execution of transactions on that CEX. The bridge shall contain a XEX specific bridge contract and an auto trading bot developed to consume the CEX’s API’s.

# Glossary

## Subscription

To subscribe to an asset or whitelist the vault contract simply sets the asset or whitelisting object to the groups it wants to permit within the vault contract. In general the asset contract and the whitelisting contract do not keep track of which contracts are subscribed to them, rather the contract keeps track of what assets & protocols are permitted and which whitelist to apply











