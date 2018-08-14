
Dash Evolution

Rev: 0, DRAFT

Abstract
We identify the two main problems of cryptocurrencies as being inherently hard to use and integrate, in  
terms of operating in a way that’s complex to users unfamiliar with cryptocurrency as compared to  
mainstream payment systems and processes in general, and secondly being hard to access and prone  
to a movement of users from fullnodes to semi­trusted, centralized intermediary services due to the lack  
of incentives to run fullnodes as their operating costs increases and due to a network design which  
assumes all users are prepared to operate as a p2p node using non­standard communication protocols  
instead of a Client such as a browser or mobile over a common protocol such as HTTPS. To solve the  
problem of usability for end­users and application integrations, we introduce the concept of a blockchain  
Account providing a persistent user­subscription that can add, update and retrieve objects that make it  
easier for Account users to manage and spend their funds and connect with other users, using objects  
such as Contacts, Apps and Orders. Users burn Dash when creating the account which forms a  
fee­balance for the Account that users can spend to miners who claim fees for updates to Accounts in  
blocks via a coinbase output. For flexibility, we define the objects Accounts can create and update using a  
JSON based schema that provides consensus rules on object validation using programmatic  
implementation of the object protocol from core nodes to web browser clients. For scalability, nodes store  
just the hash of the transitions in Account state in blocks and commit the transition data, which is  
essentially notarized in the block, to a parallel storage mechanism that keeps only recent states of an  
Account’s data, with users accessing the current state normally and therefore scaling in relation to the  
total number of end­users rather than the total state transitions on their data, which is also sharded on a  
per­account basis. In rare cases that could result in object data loss, such as a mass correlated  
destruction of peers and their data, or if user’s want to restore a past Account state, only a single copy of  
an account state needs to be recovered, for example from a user’s local backup, from a block explorer or  
from an archive service for a fee, which can then be authenticated by validating its hash against the  
blockchain object­state consensus and repopulated into the network. This enables an immutable and  
fully durable consensus on the authenticity of objects and their state transition histories with minimal  
impact on blockchain growth, and a fully immutable consensus on object data with a durability level  
maximized in relation to the size of the user­base and the total capacity of the peer­to­peer network, and  
with the collateralized nodes facilitating and storing state transitions rewarded only when they achieve a  
minimum quota of transitions in blocks derived from the total nodes and total Account activity within a  
recent timeframe. To solve the problem of decentralized access, we implement a Client protocol that  
enables HTTPS access by Account users via collateralized nodes working in quorums determined using a  
recent block hash, who can update their account within authenticated pseudonymous sessions and verify  
quorum responses using other nodes in the network anonymously as well as gaining full SPV access to  
the payments layer through the HTTPS API.


www.dash.org


Dash Evolution





www.dash.org
1  

Dash Evolution

Contents
1 Introduction
2 Design Rationale
2.1 Evolution Goals
2.2 Accounts & Objects
2.3 Consensus
2.4 Decentralized Access
3 Accounts
3.1 Account Subscriptions
3.2 Account State
3.3 Account Data
3.4 State Transitions
3.5 Data Transitions
4 Schema
4.1 Interoperability
4.2 Schema Base
4.3 Schema Model
4.4 Payment Objects
4.5 State Sequences
5 Consensus
5.1 Persistence Strategy
5.2 State Transitions
5.3 Block Protocol
5.4 Block Validation
6 Drive
6.1 Data Types
6.2 Committing Data
6.3 Scalability
6.4 Storage Architecture
6.5 Data Partitioning
7 Decentralized API
7.1 Network Architecture
7.2 Client Protocol
7.3 Quorum State Transitions
7.4 Simplified Verification
8 DashPay Model
9 Triggers
9.1 Ratings
9.2 Masternode Shares
9.3 Proof­of­Service Verification
9.4 Masternode Rewards
9.5 Governance
9.6 System Admins

www.dash.org
2  
Dash Evolution

3

1 Introduction
Dash Evolution provides an Object­based Tier­3 access layer that encapsulates existing core  
payment objects such as transactions and blocks, and new types of object such as Users, Apps  
and Orders that make Dash easier to use and integrate for end­users and 3rd party applications.
Evolution enables end­users and applications to access the system using a persistent Account  
to read, write and query Objects through a Decentralized API (DAPI) distributed across the  
Masternode network using HTTPS from any device. DAPI is designed around the kind of data  
models and design patterns commonly found in mainstream applications, without needing  
in­depth cryptocurrency knowledge to integrate or use Dash easily.  
Nodes and clients can access object­based meta­payment functions that abstract the core  
function of sending transactions between 2 cryptographic addresses within the kind of  
processes users and businesses are more familiar with. In this sense, the Object protocol  
overlays the existing core transactional protocol in Dash; Objects cannot enact movements of  
funds in Dash, but they can represent the metadata needed in common use cases between  
parties to abstract the transaction process and persist end­user and application data in a useful  
and friendly way to most mainstream users and developers, without centralized intermediary  
services or middleware.



www.dash.org  

Dash Evolution
4

2 Design Rationale
2.1 Evolution Goals
The overall goal of Evolution as a whole is to provide a secure, decentralized solution for  
end­users who find cryptocurrencies too inaccessible or hard to use and businesses who find  
cryptocurrencies to be too complex and costly to integrate to their websites and applications.
Evolution is a system that provides ease­of­use in the form of, for typical consumers, the ability  
to simply signup as a Dash user from a website or mobile app and store funds or pay friends or  
merchants by name without needing permission or access from an intermediary service, that is  
to say, the service they connect to is the peer­to­peer network itself and not a proxy, which when  
combined with a SPV service available through a network­wide Decentralized­API and the  
ability for end­user clients to act as selfish nodes in the p2p network, provides the most secure  
and decentralized access for end­users who are not prepared to run their own fullnode. For  
merchants, this means the ability to integrate Dash payments directly into their web page or app  
in the a way they are familiar with, e.g. copying and pasting some JavaScript into the web page /  
web server to enable payments as per PayPal or Stripe, instead of having to either implement a  
non­standard payments infrastructure including a full­node implementation, or use a  
permissioned remote payment processor, and also with direct SPV­based peer­network access..
2.2 Accounts & Objects
User Accounts and the Objects they can add and update are the basic data structure used in  
Evolution’s new Account functions in the same way that Transactions are the basic data  
structure used Dash’s current payment­only functions.
Instead of just the ability to read and write transactions to the Dash Network, Evolution provides  
a range of inter­related Object types that model common and useful entities during B2C, B2B  
and C2C payment processes and enable end­users and applications to interface with Dash in a  
much simpler way that abstracts the cryptographic elements of Dash for end­users and  
applications into an easier to use platform, such as users not having to enter or validate Dash  
payment addresses, and provide this as a service through a Decentralized API (DAPI).
For example, currently Merchants integrating Dash have to operate at a transactional level, that  
is to say, each customer purchase requires the merchant to generate and display a long  
cryptographically generated address, which the user has to physically copy to a wallet and  
construct a payment transaction manually. Once the payment is made, the merchant then has to  
check for the transaction on the blockchain and link that to their own internal accounting system  
for recording and auditing.

www.dash.org  

Dash Evolution
5

If we abstract this process using objects, for example a Merchant, User and Order object, the  
process can be that the User passes their unique Object identifier (such as username) to the  
Merchant, who in turn creates an Order object with the User object marked as the recipient and  
a physical address exchange handled automatically within those objects based on a  
pregenerated HD seed. The User can then make the payment and the system can alert and  
represent the payment to both the user and the merchant without anyone having to enter an  
address, and using objects that are common­place and understood well by both end­users,  
merchants and their developers and will likely be pre­existing and interoperable with in the  
merchants backend systems.
By delivering a cryptocurrency service at an abstract, Object based level instead of at a purely  
Transactional level, the main benefits to users are:
● Businesses can integrate Dash payments directly (through a decentralized HTTPS API)  
without having to implement middleware or 3rd party solutions to interface a  
non­standard (p2p cryptocurrency) payments system to their existing system’s  
functionality and data­model.
● Users can signup and login to Dash directly (from a webpage or mobile app) to access a  
persistent account using a username and password and perform all typical payment  
functions provided by standard (centralized) services at a level of abstraction that hides  
the non­standard cryptocurrency payments experience and provides a more familiar and  
easy to use experience
● Operators such as Masternode operators, contractors, admins and moderators can  
interact with the Network in an easy to use way (via websites and apps connected to the  
DAPI) and without needing to use a fullnode CLI.
The architecture also enables Dash to be used as a decentralized marketplace, for example, an  
online merchant operating an e­commerce pipeline can register an ‘App’ object in Dash and list  
their product SKU’s within it and retrieve these on either their server or client side, so they can  
either link their existing pipeline implementation to Dash, or if fiat transactions are not required,  
implement their whole pipeline off of Dash’s object implementation. They can then link Dash  
users directly to their local users and bill them and confirm payment using object based HTTPS  
API calls to the Dash Network without having to integrate any non­standard functions such as  
addresses and transactions on their backend, and they can do this with a lite (SPV)  
implementation through the API or with their own fullnode if they want full trustless operation.
For end­users, after signing up they can access their full account and history from any location,  
connect with and pay friends, and discover and “install” (authorize) Apps that they can then  
interact with, either within Dash (if the App is listing product objects) or externally through the  
App’s own interface. They also have access to a wide range of features, such as account types  
like savings, cash (anonymous), joint (multi­sig) and vaults (covenants), or payment types such  

www.dash.org  
Dash Evolution

6

as one­time payments, moderated refunds or recurring auto­payments (subscriptions), without  
having to manage addresses or manually lookup a transaction in a block explorer.
2.3 Consensus
The Dash Blockchain stores transaction data in an immutable, decentralized consensus with full  
durability, that is with a zero probability of data loss with at least one honest fullnode available.  
In the case of Accounts and their Object data, and maintaining a consensus on the correct  
sequence of transitions of Account states, the blockchain would be the most durable method to  
store all Account data in a chain of state transitions, but it is also the most expensive. In fact, in  
the type of decentralized e­commerce applications that Evolution is designed to support, the  
meta­data the objects support around each payment would increase blockchain growth several  
times, linearly to the increase in payment volume. The economics of this kind of overhead, even  
with an incentivized node network, are infeasible because essentially the cost of storing meta  
data around a payment cannot be many times the cost of the actual payment on the blockchain  
because it does not add the same increase in value to end users of using DAPI over a basic  
core­client payment.
The main problems with storing full data for Accounts and their Objects on the Blockchain are:
●
●
Transactions involve a movement of funds from which the fees to include transactions in  
a block can be deducted from easily. Storing additional Objects that don’t transfer funds  
has the problem of providing no incentives for miners to include them in blocks.
As entities designed to abstract transactions within the kind of processes end­users and  
applications are familiar with, Objects inherently will have a larger data size than the  
transactions that they encapsulate. This will result in a larger chain, with chain size  
already being one of the key limitations of cryptocurrencies in general in terms gaining  
traction with an alternative to centralized payments systems that can process payments  
with orders of magnitude more transactions per second and without a O(n) overhead and  
at a much lower cost to the operators involved. Dash alleviates this problem by  
rewarding collateralized nodes to host higher capacity infrastructure as usage as the  
system scales from the block reward, but this is still subject to the problem that if  
provision costs overtake rewards, the network capacity and availability is dependant on  
altruism and capacity starts to decrease and infrastructure becomes more centralized to  
businesses that depend on capacity to operate.




www.dash.org  
Dash Evolution

7

At the same time, the Blockchain is the only secure way to achieve decentralized consensus on  
Account states and their histories. For these reasons, we take the approach:
1. We only use the Blockchain to keep a record of what an Account’s Data   should   be, using  
it’s hash, and then offload the storage of the actual Objects to Masternodes who are paid  
only if they store that Object Data and provide it on the network when needed.
2. Incentivize miners to include Object hashes in blocks by forcing users to burn a minimum  
quantity of Dash at the time of Object creation, then use this as an account balance from  
which a fixed fee is allocated to a miner when they include that Object hash in a block.
3. Enable nodes to store only a subset of Object’s Data (whilst maintaining a full set of  
blocks and the contained Object hashes) to spread the cost of data provision across  
nodes, i.e. O(n/s), where s is the size of the subset as a factor of the total number of  
Objects nodes are required to provide.
4. Force collateralized nodes to proof a minimum level of service deterministically to  
receive rewards for providing Account access and storage
This gives us the properties:
●
●
●
●
●
●
●
An Account and every state­transition in its history can be validated by comparing its  
hash  to the hash in the Block’s Merkle Tree. This means that in the event of data loss  
from e.g. mass correlated node failure, only a single copy of the Object’s Data needs to  
be reintroduced for the network to the validate, propagate and persist that data, for  
example from an end­user backup or an archive service.
Fullnodes can validate an Object during relay without needing an existing copy of the  
data
Miners are incentivized to include Object state transition hashes in blocks
Nodes are incentivized to store and provide Object data to users
Overall storage in the network for Object data scales at a reduced rate of O(n/s) where s  
is the shard factor required by the network.
Blockchain size scales at an increased linear rate (estimated around O(<2n) when  
allowing 1­2 Account state transitions per transaction) regardless of Object size
Increased utility of an easy­to­integrate & easy­to­use service feeds back into the value  
of rewards paid to Masternodes to cover increased costs of hosting a larger Blockchain  
and the total Object Data­set.




www.dash.org  
Dash Evolution

8

2.4 Decentralized Access
Evolution has been designed from the front­end­up,with the focus on providing an end­user  
experience that’s easy to use, secure and decentralized, rather than starting from a basic need  
to send funds between 2 addresses, to remove some of the main frictions to mass adoption and  
to open the door to new, permissionless ways for developers to build digital cash based apps  
and services using the most commonly used programming languages, data structures and  
protocols.
One of the main problems with real­world usage of Cryptocurrencies is that the vast majority of  
users do not actually deal directly with the Cryptocurrencies p2p network, they rely on 3rd  
parties to provide intermediary services, to which the user is usually trusting for payment  
verification and always dependant on the permission and availability of those services as well as  
subject to censorship and monitoring, which undermines one of the main advantages to  
cryptocurrencies in terms of not relying on an intermediary to send or verify funds. This is  
because running your own fullnode is something most users choose not to do because most  
users do not want the hassle and cost to run complex software from their desktop when they are  
already using centralized services from the browser and mobile devices. A comparison would  
be seeders and leechers in BitTorrent; only a small fraction of users seed, and of those, most  
have financial incentives to do so. Of seeders, most of those are on desktop apps, with  
browsers and mobile devices needing to go through proxies to access the torrent network.  
What this results in is the cryptocurrency p2p network where end­users rarely access directly  
and instead access via centralized businesses and mostly without SPV (Simplified Payment  
Verification), especially on the web. This reduces the benefits such as permissionless access,  
service availability and trustless operation to end­users when choosing between  
cryptocurrencies and existing easy­to­use centralized payment services and reduces the  
potential market size to which cryptocurrencies have fully­featured access to.
What is needed is a way for end­users to access the P2P network directly in a read­only (i.e.  
selfish, leaching, or non­contributing manner), using the kind of tools and protocols that they are  
already using, and ensuring that that access is SPV based and with the ability to interoperate  
with any contributing node in the network, in other words a Client protocol.




www.dash.org  
Dash Evolution

9

Cataloguing the major problems:
●
●
●
The communication protocol used in the Dash P2P network is a bespoke messaging  
protocol on non­standard ports that isn’t understood or used by most developers, hard to  
implement, inaccessible to the clients with the largest end­user­payments market share  
(browsers) and even though it is available on mobile, as a non­standard protocol it is  
often blocked in firewall policies and easier to censor by authorities.
Even with a p2p connection, the protocol is extremely complex to implement correctly,  
and developers need to understand cryptocurrency to a high degree to build applications  
that interface with the P2P network and integrate into their existing frontend and server  
systems securely.
For SPV to be available ‘on the wire’ and not through centralized proxies, it needs to be  
available and tightly integrated into the new decentralized access method and also  
extended to include verification of Accounts and their data objects via the blockchain.
Elements to a solution:
1. To remove the need for an intermediary between the p2p network and the majority of  
end­users, we enable direct connection to the network by end­users using the most  
common and understood protocol, HTTP/HTTPS. This protocol is the most used  
protocol for applications by far and the standard communication protocol used by  
websites and mobile applications and is therefore relatively censorship resistant.
2. To reduce the complexity of interfacing with the protocol, nodes will provide an API using  
XMLHttpRequests over HTTPS that accept and return JSON forms of Accounts and their  
Objects which can be easily integrated into existing websites and application using  
common design patterns and user primitives.


www.dash.org  

Dash Evolution
10

3 Accounts
Accounts are the foundation to user access in Evolution, enabling users to cryptographically  
prove ownership of a pseudonymous identity that they create, and persist public and private  
data related to that identity on the blockchain.
Accounts are data structures stored on the blockchain that represent the various new types of  
Users that Evolution serves, such as End­users (e.g. Consumers) and Applications (e.g.  
Businesses), who pay fees to the network in return for adding and updating their Account’s data,  
essentially as s
 ubscribers t o the network.
3.1 Account Subscriptions
Users create Accounts by registering subscription data directly on the blockchain in the  
metadata of specially constructed transactions called Subscription Transactions that also burn a  
minimum amount of Dash via a provably unspendable null­data pubkey script in one of the  
transaction’s outputs.
Subscription data for an account consists of a identified for the type (e.g. a User), a unique key  
for that type (e.g. a username), and a public key that lets the user prove they are the owner of  
the account by signing challenges, and maintain a “fee­balance” consisting of a tallied quantity  
of Dash burned on the account that is spent to miners for including updates to the account state  
in blocks. Note that the public key for the Account is purely for authentication and not to be  
used as a payment address.
Account subscription data is stored within transaction metadata as the blockchain provides the  
most security and durability in terms of maintaining a consensus on subscription data, and is  
accessible and verifiable at an SPV level using existing tools. Also, as the amount of  
subscription data each account needs to store in transactions during its life cycle is minimal, the  
penalties of adding this data to the blockchain are minimal too with the amount of additional data  
stored in transactions scaling linearly to the number of user signups.
Using null­data pubkey scripts in a transaction output to register Accounts also has the benefit  
that funds can be burned at the same time that are provably unspendable and can then provide  
a “Fee Balance” for the account (the use of which is to incentivize miners to include changes to  
an Account’s state in blocks). However, rather than storing the metadata after an OP_RETURN  
opcode, we introduce the new opcode OP_SUBTX that mirrors OP_RETURN but is dedicated  
to Subscription Transactions. The reason is that OP_RETURN is already used generically by  
3rd parties in the Dash ecosystem with an 80 byte max length and may also be pruned (as there  
are no functions dependant on this data within the Dash protocol). Using a dedicated opcode  
such as OP_SUBTX, the data is compartmentalized so that nodes can identify and validate  
Subscription Transactions with specific length and validation rules and without danger of pruning  

www.dash.org  
Dash Evolution

11

or invalid registration data being stored on the blockchain, making filtering and handling of the  
transactions more efficient and ensuring integrity of the Subscription Transaction dataset.
3.1.1 Registration
The transaction metadata needed to create a new Account with sample data is as follows:

Registering an Account with a Subscription Transaction
Action  : An integer representing the type of Subscription Transaction, enumerating to the types  
“Register”, “Topup”, “ChangePubKey” and “Deactivate”.
Type  : This is an integer representing the type of account as defined in a JSON protocol called  
the Schema which governs all data­structures in Evolution and is described later. For now we  
assume all accounts are a type “User”, the main account type and targeted at end­users. The  
account type cannot be changed by subsequent subscription transactions for this Account.
AccKey  : A string of characters which must be unique within the total Account set on the  
blockchain for the specified Account Type (similar to a primary key in a database). The AccKey  
cannot be changed by subsequent subscription transactions for this Account.
PubKey  : The owner’s public key for the Account, enabling proof of ownership of the account for  
the private key holder by verifying their signature against the specified PubKey. This should not  
be used to hold funds in a Dash address, and is purely for authentication purposes.
Signature  : A signature of the hash of the preceding fields signed by the owner with the private  
key for the specified PubKey
Burn Amount  : This is the amount of Dash burned in the transaction output. It must be greater  
than or equal to a minimum fee for registering the specified Account type (0.005 for a User  
Account). The amount of Dash burned above the minimum fee is credited to a ‘fee balance’,  
and is spent on updates to the account later.
3.1.2 Subscription Status
To identify Alice’s account in Evolution, the AccKey (“Alice”) and the account type (e.g. “User”)  
are both required in combination, similar to a dual­primary key in a database, alternatively, the  
hash of the initial registration transactions can be used.
3.1.3 Nodes can then validate the subscription status of an Account by querying the blockchain  
for all subscription transactions with a given Type and AccKey combination, for example,  

www.dash.org  
Dash Evolution

12

returning all subscription transactions of type ‘User’ and with the username ‘Alice’, and check  
that the Account was registered and has not been closed.  
3.1.4 FeeBalance
In the above registration example the current balance of the account is derived from the total  
burned on the account (0.01 Dash), minus the minimum fee for signing up a user at 0.005 Dash  
(plus any Account update fees which are described later) to tally the account’s balance at 0.005  
Dash.
Nodes tally this balance by summing the amounts credited in the initial registration transaction  
and subsequent top­up transactions existing in the blockchain, and then deducting the sum of  
debits from the account in the form of fees deducted in object state transitions for the subscriber  
in the blockchain (described later).
Note that the only funds under the control of the subscriber's public key are those available as  
the account fee­balance, and those funds can only be spent by updating objects under the  
control of that account. Subscribers cannot change data in other subscriber accounts (without  
their private key) and cannot transfer fee­balances to another account, to minimize incentives to  
hack someones account. Therefore, if a subscriber’s private key is compromised, the attacker  
can only use the available fee­balance to that Account’s data, or to deactivate the Account.  
3.1.5 Account TopUps
Account owners can top­up the fee­balance by creating a “Topup” subscription transaction with  
the registration transaction’s hash, and burning any additional amount of Dash, which is credited  
to the subscriber’s account balance when tallying the current account subscription state. It does  
not need to be signed by the Account owner, i.e. anyone can topup an Account’s fee­balance

Topping Up an Account’s Fee­Balance

www.dash.org  

Dash Evolution
13

3.1.6 Changing the public key
The public key for an Account registration can be changed by submitting an additional  
registration transaction with a “ResetKey” action, specifying the new public key and signing with  
the public key from the last registration transaction for that account.

This enables e.g. a user to change their password. The latest public key in the blockchain for  
this account is the proof of ownership, provided the registration transaction was valid and signed  
for the pubkey from the previous registration transaction for the subscriber.
3.1.7 Closing an Account
In the case that a subscriber's private key is compromised, and the attacker then changes the  
public key, the subscriber can create a new subscription transaction using the “Close” method  
and signing for a previous public key, effectively deactivating the Account and preventing any  
updates to it’s data in future.

To validate the deactivation, nodes will check back for a set amount of time (e.g. 6 months) and  
allow deactivation on any public key within that period (and 3rd party apps could check further if  
required).

www.dash.org  
Dash Evolution

14

This enables users to prevent unauthorized access to their account, as accounts cannot be  
re­opened. It also means an attacker with the user’s private key could deactivate their account  
but this just prevents the rightful owner from updating any objects using the account because  
the public key doesn’t hold funds, and the user can copy the account data and register a new  
account.
3.2 Account State
An Account’s State is a representation of the current set of data and metadata related to an  
Account subscription.
When an Account is registered, it is in the ‘null­state’, until the Account owner creates data for  
the account.
Updates to the Account data require a valid signature of the hash of the Owner state properties  
(which include a hash of the data) for the public key of the Account derived from the subscription  
transaction set.
Meta state is created by miners who set the status of the account and the fee­balance, based on  
the Account’s activity and the consensus rules, described later.

Account State derived from Subscription Transactions





www.dash.org  

15
Dash Evolution

3.3 Account Data
Data for an Account is stored as a collection of data structures called   Objects   that Account  
owners can create and update within State Transitions.
Objects have a Header and a Data section that contain data fields called Properties. The  
Header includes a Property containing the hash of the Data section Properties, so an individual  
Data section can be matched to a header e.g. when stored in different locations, and the Header  
itself can be hashed to provide a single hash of all Properties in the Object.

Object Authentication & Verification
Account owners prove ownership of Objects by signing the header properties with their private  
key, which includes a hash of all property data, which nodes can independently verify using the  
blockchain.
3.3.1 Object Tree
The Objects within an account are hashed in a Merkle Tree whose root resolves to the  
DataHash property in the Header section of an Account State object.
Because Account State Transitions are stored in blocks, clients can prove that an Object was  
part of an Account State committed to a block by hashing the Object locally and checking a  
Merkle proof for the Object’s branch in the merkle tree.

www.dash.org  
16
Dash Evolution



Account Data Objects hashed in Merkle Tree
3.4 State Transitions
A State Transition, or Transition, is defined by the change in state of an Account’s owner data  
and metadata from an old state to a new state.   The full set of properties in a Transition are:

Each new transition references the last transition agreed by network consensus with the  
PrevSTHash property, and PrevDataHash references the last DataHash.

www.dash.org  

Dash Evolution
17

Certain state transitions can exist without an Owner State, called Delegate State Transitions,  
where only the meta state is amended using network consensus, for example to ban the  
account with a sufficient consensus majority.
Accounts exist purely as a sequence of State Transitions and their associated data, providing a  
cryptographic proof of the sequence of transitions, the validity of each transition’s data via its  
hash, and proof the data in each transition was created by the Account owner by verifying the  
signature using the public key for their subscription.

3.5 Data Transitions
Each Account State Transition contains only a differential set of data Objects that were added or  
changed in the transition, called a Data Transition.
The root of the Merkle Tree of all Objects in the Account resulting from the transition is stored in  
the DataHash property in the Owner State.


www.dash.org  
Dash Evolution

18

4 Schema
The types of Objects that Accounts can store, rules as to how they should be validated and their  
permitted relationships within an Account and to other Account’s Objects, are defined in a  
JSON­specified protocol known as the Schema.
The Schema is defined in a single reference JSON specification that nodes and clients can  
interpret programmatically or manually through successive versions and JSON is the native  
format for interoperation using Objects across all Dash nodes and clients, that is to say that  
every Object owned by an Account in Evolution can be expressed and validated as a JSON  
Object as defined in the JSON Schema and the relationships and constraints defined in the  
Schema are used as reference whenever an Object needs to be validated, stored or acted  
upon.
We use a programmatically interpretable Schema specification because it enables us to  
decouple Object implementation from Object validation, relay and storage, for example, core  
code can validate an object based on the Schema rules, whilst remaining agnostic to the  
specific functionality required to handle that Object Type in higher tiers such as the  
Decentralized API or client applications. This also enables us to modify the Schema and add  
new Object types in future without having to re­engineer large amounts of code, and for  
example have separate teams working on the Schema design (in JSON) and the various  
implementations in nodes and clients. The Schema architecture can also be extended in future  
to allow 3rd party apps to implement their own sub­schemas to integrate functionality  
customized for their own requirements.
Some additional benefits of using a ‘dynamic’ design­time protocol such as the Schema for  
Objects are:
●
●
●
●
●
●
Core/DAPI are object agnostic
End­to­end reference spec for object validation
Decouple business layer from data access layer
OO enables code reuse
Enables polymorphic code use in Clients e.g. reuse functionality that processes base  
class properties and constraints on derived classes
Enables programmatic code generation for e.g. Client SDK object sets
The Schema has the properties of both an entity relationship (ER) model and a unified  
modelling language (UML) model used in Object Oriented Programming (OOP). This means  
that Dash Evolution functions somewhat like an decentralized object­oriented relational  
database application for end­users and 3rd party applications, where the table rows are instead  
Objects defined by the Schema’s JSON definition at design­time.
In this section we present the basic Schema elements, followed by walkthrough of an example  
implementation of a real­world use­case using the Schema to signup a user and friend another  

www.dash.org  
Dash Evolution

19

user and pay between themselves using automatically generated Dash addresses that aren’t  
linked to their Account data.
4.1 Interoperability
The native format for Objects in the Schema and the Schema definition itself is JSON 1 enabling  
Objects must be interoperable universally throughout the Dash network and ecosystem from  
fullnodes to clients, for example:  
●
●
●
●
●
●
DashCore can relay Objects with other fullnodes using P2P messages
DashCore can store and retrieve Objects using local storage
DAPI can read and write Objects to and from DashCore using RPC and ZMQ
DAPI can handle HTTPS XHR requests and responses containing native JSON Objects
Client Wallets can request and receive Objects from DAPI and can backup a user’s  
Object Set in native JSON format
Client Applications can request and receive Objects in native JSON format
4.2 Schema Base
To prevent duplication of properties in the Schema definition we can implement a form of OOP  
inheritance by specifying a base class from which Objects inheriting the base will gain its  
properties. Inheritance is also useful in Clients to avoid duplication of code and enable  
integration using polymorphic code that handles Dash Objects from e.g. a wallet GUI through to  
a merchant backend system.
The Schema Base is the definition of abstract Object rules and properties that are hardwired  
into the core protocol in terms of how derived Objects are created, updated, validated and  
stored by nodes.
On top of the Schema Base is the Schema Model, which defines Objects derived from  
SchemaBase classes and that are specific to an instance of the blockchain (i.e. the forthcoming  
Dash Evolution).
All Objects in the Schema inherit from the ObjectBase, and we refer to Object definitions in the  
Schema as Object Classes. Actual Object data which instantiate the class definitions are  
referred to as Object Instances, or just Objects.
1
 R
 FC 7159 ­ The JavaScript Object Notation (JSON) Data ... ­ IETF Tools

www.dash.org  
Dash Evolution

20

//
 A
 bstract
 B
 ase­Object
 C
 lass
 d
 efinition
ObjectBase
: {



   /
 /
 R
 ules
   C
 reateFee:
 1
 ,
   /
 /
 #
 o
 f
 c
 redits
 t
 o
 c
 reate
 t
 his
 O
 bject
 t
 ype
   U
 pdateFee:
 1
 ,
   /
 /
 #
 o
 f
 c
 redits
 t
 o
 u
 pdate
 t
 his
 O
 bject
 t
 ype
   M
 axSize:
 1
 000
,   /
 /
 m
 ax
 d
 ata
 s
 ize
 i
 n
 b
 ytes
   M
 axCount
 :
 1
 ,
   /
 /
 1
 =
 U
 nique
 i
 nstance,
 >
 1
 =
 m
 ulti­instance,
 n
 once
 b
 ased
   P
 runeDepth
 :
 0
 ,
 /
 /
 b
 lock
 d
 epth
 w
 hen
 d
 ata
 t
 ransitions
 c
 an
 b
 e
 p
 runed
   R
 ateTrigger:
 0
 ,
 /
 /
 O
 bject
 c
 an
 r
 ate
 r
 elated
 a
 ccounts?

   /
 /
 P
 roperties
   H
 eader:
 {

     R
 egTX:
     {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 H
 ash
 o
 f
 t
 he
 r
 egistration
 t
 x
     A
 ccNonce:
   {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 S
 tate
 t
 ransition
 n
 once
     O
 bjNonce:
   {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 R
 evision
 o
 f
 t
 his
 O
 bject,
 p
 revents
 r
 eplay
     T
 reeIDX:
   {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 I
 ndex
 i
 n
 t
 he
 O
 bject
 M
 erkle
 t
 ree
     D
 ataHash:
   {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 H
 as
 o
 f
 t
 he
 D
 ata
 p
 roperties
     R
 elations:
 {
 /
 *
 r
 ules
 *
 /
 }
 ,
   /
 /
 F
 oreign
 O
 bject
 c
 onstraints
     S
 ig:
     {
 /
 *
 r
 ules
 *
 /
 }
     /
 /
 O
 wner
 s
 ig
 o
 f
 t
 he
 h
 eader
 h
 ash
   }
 ,
   D
 ata
 :
 {

     B
 lobs:
 [
 ]
   /
 /
 P
 roperties
 e
 ncrypted
 f
 or
 r
 elated
 A
 ccount
 o
 wners
   }

},


From the JSON:
●
●
●
Rules are optional in derived Object types, and if not specified, the base default is used
All header properties must be respecified in derived Object type definitions.
The Data section contains a collection of encrypted Blobs that the Account Owner can  
encrypt for themselves (blob[0]) or for foreign Accounts as specified in the Relations  
property (blob[1..n]) and is described later.
4.2.1 Root Objects
Now we have defined the base for all Objects, the second important Object is the  
RootBasewhich is always at index 0 in the Account State’s Merkle tree and is the class definition  
for the type of Account which is the same ‘Type’ set in the Account’s registration transaction, for  
example a User, App or Masternode Account.

www.dash.org  

Dash Evolution
21

//
 A
 bstract
 A
 ccount
 R
 oot­Object
 C
 lass
 d
 efinition
RootBase:
 {

   I
 nherits:
 O
 bjectBase
,   /
 /
 I
 nherits
 a
 ll
 r
 ules
 a
 nd
 p
 roperties
 f
 rom
 t
 he
 b
 ase
 t
 ype

   B
 anParticipation:
 0
 ,
   /
 /
 M
 in.
 %
 o
 f
 M
 Ns
 n
 eeded
 i
 n
 b
 an
 v
 ote
   B
 anMajority:
 0
 ,
       /
 /
 M
 in.
 %
 o
 f
 M
 N
 m
 ajority
 t
 o
 b
 an

   /
 /
 M
 eta
 S
 tate
 f
 rom
 t
 he
 l
 ast
 s
 tate
 t
 ransition
   S
 tate:
 {

     R
 ating:
 0
 ,

     B
 alance:
 0
 ,

     S
 tatus:
 0

   }

}
4.2.2 Leaf Objects
Leaf Objects are non­root Objects, i.e. with an index > 0 in the Object Merkle tree
//
 A
 bstract
 A
 ccount
 L
 eaf
 O
 bject
 C
 lass
 d
 efinition
LeafBase
 :
 {

   R
 ootObject:
 {
 }
 /
 /
 U
 sed
 i
 n
 C
 lients
 t
 o
 a
 ccess
 t
 he
 p
 arent
 A
 ccount
 f
 or
 a
 n
 O
 bject
}

For now, we leave the LeafBase empty, apart from a RootObject reference that is used in
Clients.
4.3 Schema Model
The Schema Model contains Object type definitions derived from the SchemaBase Objects,  
such as base classes for User, App and Masternode account types. This enables Nodes to  
remain agnostic to Objects defined in the Schema Model, processing them based on the rules in  
the current Schema Base, with most of the functionality provided between Clients tightly coupled  
to the current Model protocol number. This also enables a lot of flexibility, because  
improvements can be made to the Schema model such as expanding Object types or adding  
new user interactions, without having to re­engineer the core system.
The full Schema Model for Dash Evolution will be detailed later, but for now we can specify the  
most fundamental Account type which is a Root Object that can represent a User, ownership of  
which will enable the user to login, store & retrieve data, and connect and communicate with  
other users in the system, with all User data persisted on fullnodes, except for the Account  
owner’s private keys.
Note that the User Object Type is one of the 3 Root Object types In the DashPay schema in  
Evolution V1,the other two being App and Masternode Objects which are described later.

www.dash.org  
Dash Evolution

22

//
 U
 ser
 R
 oot
 A
 ccount­Object
 C
 lass
 d
 efinition
User
: {

   I
 nherits
: R
 ootBase,
 /
 /
 D
 erive
 f
 rom
 t
 he
 b
 ase
 c
 lass
 f
 or
 A
 ccount
 r
 oot
 o
 bjects
   H
 eader
 :
 {

     /
 /
 I
 nherited
 p
 roperties
   }
 ,
   D
 ata
 :
 {

     S
 ummary
: {
 /
 *
 r
 ules
 *
 /
 }
 ,
     I
 mgURL
:   {
 /
 *
 r
 ules
 *
 /
 }
 ,

     B
 lobs
: {

       S
 elf
: {

           H
 DRootPubkey
: T
 ypes.XPubKey,
   /
 /
 P
 ublic
 H
 D
 s
 eed
 f
 or
 t
 he
 A
 ccount's
wallet
           B
 lockedUsers
: T
 ypes.TXHash[]
   /
 /
 L
 ist
 o
 f
 r
 eg
 t
 xs
 f
 or
 d
 eclined
requests
       }

     }

   }

}
4.4 Payment Objects
The Schema also provides a 3rd type,   Payment Objects  , which are read­only abstractions of  
the existing Tier­1 (payments level) blockchain data structures derived from confirmed  
transactions and made available as Evolution Objects, to enable applications to access these  
structures easily if required and for Objects to reference them internally:

●
●
●
●
Block, Tx and Address are the main object types used in the current payment level of  
the current Dash protocol
Subscription Transactions are null­data transactions with subscription metadata  
indicating the ownership, status and fee­balance of an Account
Collateral are UTXO with specific metadata used in Evolution, and are used for  
Masternode Shares.
SuperBlocks (SBs) that pay winning Proposal Objects are created deterministically by  
Miners in Evolution, with payments added as outputs in the SB’s coinbase transaction.

www.dash.org


23
Dash Evolution

4.5 State Sequences
Now that have defined the abstract types in the Schema Base and introduced the Schema  
Model, we can derive some example types of Object in the model to illustrate how Object  
functions are implemented and the sequence of states that multi­party interactions observe  
using a basic test use­case of user friending for private C2C (consumer­to­consumer)  
payments.
4.5.1 Requirements
As a simple test case, we want a user to be able to register an account, request to ‘add’ another  
user as a contact, at which point the other user is able to accept or decline that request. If  
accepted, both users will be able to see the other user in a list of their contacts. If the request is  
declined, the requesting user doesn’t have the option to request again, and the requested user  
doesn’t see the request anymore.  
For our test case, we know we have to store the data using Account Objects, but we don’t care  
how or where the objects is stored, which is described in the next section. Instead we care  
about what data is stored and what is the sequence of states in both user’s Account Objects at  
each state throughout the ‘friending’ process. Therefore in this case we assume all Objects can  
be read and written to the network using the rules defined thus far, and as a trustless virtual  
decentralized database with all data secured by blockchain consensus.
4.5.2 Centralized Solution
In a centralized, trusted relational database with users connecting through a server, this would  
be very common requirements that are simple to implement in a database, for example:

Users (e.g. Alice and Bob) can both signup with their UserKey via the server, which creates both  
rows in the User table. If user Alice wants to request a contact with Bob, she instructs the  
server to create a new row in the UserContact table, with her key in the RequesterKey column,  

www.dash.org  
Dash Evolution

24

and Bob’s key in the RequestedKey column, and with the Response column’s value set to 0,  
signifying no response.
Bob can then be alerted of the request, by scanning the UserContact table for any request to  
himself without a response, and then set the response to either 1 for “no” or 2 for “yes”. If “no”,  
the server can filter out the request the next time Bob accesses it, and can prevent Alice from  
resending a request to Bob. If “yes”, the server can return the User details to each other as an  
approved contact.
The validation is also taken care of by the database itself, by ensuring only valid data types can  
be entered, preventing duplicate users or contact requests through the use of foreign key  
constraints on the primary keys of both tables
4.5.3 Decentralized Design
To implement the use­case in a fully decentralized and trustless way using the Account, Object  
and Schema concepts detailed above, we can use a similar approach to the centralized / trusted  
database model, but with some limitations:
●
●
There is no trusted server with the permission or access to update the data; Both Alice  
and Bob can only update the data in their own Accounts after proving ownership by  
signing for their Account’s public key.
Data isn’t stored in single tables, it’s stored in Objects within the user’s accounts.  
Therefore both users need to be able to query Objects from all users that are referenced  
to them.
The solution to this lies in enabling Account owners (who can only change their own data) to  
reference data Objects to foreign accounts (such as a prospective or connected contact) within  
their own Object data that the foreign account can then detect, and respond with data in their  
own Object set related to the original user.




www.dash.org  
Dash Evolution

25

4.5.4 UserContact Object
The first step to a decentralized solution is to create a UserContact Object that can represent a  
user Account’s requested or confirmed relationship to another user’s account.
//
 U
 ser
 A
 ccount's
 C
 ontact
 R
 equest
 /
 R
 esponse
 t
 o
 a
 nother
 U
 ser
 A
 ccount
UserContact:
 {

   I
 nherits:
 L
 eafBase
,
   M
 axCount
 :
 1
 ,

//
 U
 sers
 c
 an
 h
 ave
 o
 nly
 1
 C
 ontactObject
 p
 er
 c
 ontact
   R
 ateTrigger:
 1
 ,
     /
 /
 T
 his
 O
 bject
 c
 an
 c
 ontain
 r
 atings
 f
 or
 r
 elated
 A
 ccounts
   H
 eader:
 {

     R
 elations:
 {

       C
 ontact:
 {

           F
 oreignKey:
 U
 ser.AccKey,

//
 K
 ey
 m
 ust
 m
 ap
 t
 o
 a
 v
 alid
 U
 ser
 A
 ccount
 k
 ey
           R
 ating:
 T
 ypes.Byte
     /
 /
 L
 eave
 a
 r
 ating
 f
 or
 t
 he
 C
 ontact
     }

   }
 ,
   D
 ata:
 {

     /
 /
 P
 roperties
 e
 ncrypted
 f
 or
 r
 elated
 A
 ccount
 o
 wners
     B
 lobs:
 {

       S
 elf:
 {
 }
 ,
       C
 ontactKey:
 {

           H
 DPubSeed:
 T
 ypes.XPubKey
 /
 /
 E
 xtended
 p
 ublic
 k
 ey
 t
 he
 f
 oreign
 A
 ccount  
                           /
 /
 t
 o
 d
 erive
 p
 ayment
 a
 ddresses
 f
 or
 t
 his
 u
 ser
       }

     }

   }

}





www.dash.org  
26
Dash Evolution


4.5.5 Static Structure
Next, we can illustrate the static structure view of the JSON, showing the inheritance between  
the Object classes we have defined so far in the Schema Base & Model.

Example Schema: Static Structure
Note that nodes or clients are not required to implement inheritance to satisfy consensus rules,  
it is essentially an aspect to the Schema design that can be leveraged to reduce duplication and  
complexity of code implementing the Schema if desired.



www.dash.org  
27
Dash Evolution


4.5.6 Referential Integrity
Finally, we can illustrate the referential relationships between the Objects defined so far from the  
JSON, which  is key to maintaining a global Object set that is correct that does not have  
duplication or invalid data that would break Client applications, along with other benefits such  
as:
●
●
●
●
●
●
Enables validation of Object relations, to ensure integrity of the consensus Object Set
Prevents duplicate objects instances or misuse of the Schema, e.g., inserting custom  
data
Enables complex queries to be run on objects that can be aggregated and correlated
Enables extraction of the Object Set to a relational database for integration, analysing or  
warehousing scenarios
Enables optimizations in retrieval through optimization of indexing strategies
Enables optimizations in storage footprint by indexing Object relations

Example Schema: Entity Relationships
In the diagram, the ContactKey must relate to a valid User Account, meaning the network will  
reject new Objects with the key of a User Account that doesn’t exist or exists and is closed (note  
that Accounts cannot update relations once created).
The foreign key relation with BlockedUsers to a valid User Account is implied, meaning it cannot  
be validated using a network consensus, because the key is within an encrypted blob only the  
User can decrypt, meaning relational integrity is reliant on correct Client implementations.  
Interestingly, as Blob contents can never be validated using a network consensus, Client  
applications could implement their own custom functionality for state­sequence base Account  

www.dash.org  
Dash Evolution

28

interoperation within the Objects and relations defined in the public model, using encrypted  
Blobs as the storage unit containing custom properties, although size of Blob’s can be limited.
4.5.7 State Sequence
With the Objects and relations defined, we can explain the key concept in understanding how  
Evolution implements database­like functionality for end­users but in a decentralized and  
trustless way, which we call a State Sequence.
The State Sequence enables sets of Accounts who want to interact to communicate information  
by only changing their own Object data, with states transitioning in the kind of sequences found  
in a centralized database application, but with users only changing their own data.
The design pattern is essentially a semaphore, where users change their own state referencing  
a foreign account who is observing any changes in account objects related to their account.
For the user­friending case we are describing, we introduce a diagrammatical format to  
represent the changes in interrelated account state transitions through a Schema defined state  
sequence, called a State­Sequence diagram, below.
Each row represents a state in the sequence, showing only new or updated Objects in each  
account being sequenced.
The first column, Named State, represents a predefined state (in the Schema) that represents  
some meaningful state in real­world use cases, that can be determined by nodes and clients  
based on the state of an Account’s objects.

State Sequence: Friending Process (Simple Example)

www.dash.org

Dash Evolution

29

From the diagram:
1. Signed Up:   Both Alice & Bob have created Accounts of type User with a subscription
transaction and their name in the AccKey.
2. Contact Requested: Alice creates a UserContact Object in the system, inserting Bob’s
AccKey in the ‘ContactID’ property, which is a relation property in the Object’s header
definition.
3. Contact Accepted: The next time Bob uses a Client, his client queries for all updated
Objects with Bob’s AccKey and detects Alice’s new UserContact Object as such.   Bob
interprets this as a request to connect with Alice, and (in this case) chooses to accept
the request, which he confirms by creating his own UserContact Object with the
ContactID referenced to Alice.   If Bob chose to decline Alice, he could update a
‘BannedUsers’ list in his private blob in his User object, that Clients can use to know not
to show Alice’s request.
4. Both Alice and Bob store an extensible HD pubkey for each other in the blob in their
UserContact Object, this lets the other user derive future payment addresses without
needing to re­exchange addresses (not shown in diagram) and as the data is encrypted
by each user for the other user, the transactions are not linked to the user’s Account
data, unless the private keys for either user’s Account PubKey are compromised at
some point the future.




www.dash.org  

Dash Evolution
30

5 Consensus
Evolution adds consensus rules to the Dash protocol governing, generally:
1. Consensus on the existence, status and ownership of Accounts, based on the sequence  
and funding of an Account’s Subscription Transactions
2. Consensus on the sequence and validity of Account State Transitions
3. Consensus on the state of Objects within each State Transition
4. Consensus on the content of the Active set of Account States
5. Consensus on Object definition and validation rules defined in the Schema
5.1 Persistence Strategy
Before detailing the consensus rules, we can identify essentially 3 new persistence  
requirements based on the above design for Accounts, their state transitions and data, each  
with very different characteristics:
1. State Transitions (Transitions between Account States) require a small, fixed amount of  
data (~200 bytes) to be stored when account data or metadata is changed, regardless of  
the amount of data changed. Accounts may batch updates into a single State Transition  
and fullnodes must persist a full set of historical transitions across all Accounts to  
validate new states to satisy the consensus rules.
2. Data Transitions which are the differential Object datasets introduced by each  
Account’s State Transition, consist of a hash of the data and the data itself. The Object  
hashes and data are not needed for historical validation using consensus rules and can  
be pruned (after a minimum time when some data is required for triggers, e.g. 1 month).  
The data can be of variable size, sometimes large (e.g. 10Kb) depending on the amount  
of Objects comprising the State Transition data.
3. Active Dataset   is the dataset of Objects for an Account representing the current (or  
‘Active’) set of the Account’s data is derived and made available by traversing previous  
differential data transitions and updated whenever a new State Transition occurs that  
changes the Account’s data.  
Discounting Data Transitions which can be discarded after a short amount of time, we are faced  
with 2 different types of persistence requirement; State Transitions must have full durability  
across all nodes and cannot be pruned (except for Closed Accounts), which scales linearly to  
the number of transactions (generally, and presuming transitions are being used as essentially  
metadata facilitating transactions and not wastage). The Active Dataset, however, represents  
just the upto­date state of user Account data, and therefore scales linearly to the number of  
users (generally speaking).

www.dash.org  

Dash Evolution
31

For these reasons, we store State Transitions directly in blocks in a process analogous to  
transactions, as full durability is required and the impact on block size is comparatively small ,  
with state transitions being smaller than transactions and less frequently created.
For the Account Data, i.e. the Active Dataset of Objects derived from state transition data and  
pruned to a recent depth to represent only the current state of constantly changing user data,  
blocks would be unsuitable due to their immutability, and therefore we need a more efficient  
storage mechanism that essentially stores sets of Objects that are mutable based on the state  
transitions in new blocks, which we call the Drive, or ‘DashDrive’ for end­users
5.2 State Transitions
Transitions represent the sequence in changes to an Account’s state and metadata are added  
to blocks by miners, and include a hash of the previous transition for the account.

The properties in the transition are grouped into 3 independent data items as follows:

www.dash.org

Dash Evolution

32

5.2.1 Owner State
Owner state contains the properties controlled by the owner of the Account
● The previous root hash of the Account Data, i.e. the source state
● The current root hash of the Account Data, i.e. the destination state
● The nonce is the transition number for the Account Data, and must be greater than the  
nonce in the last transition in a block (except for Delegate State Transitions, described  
below).  
5.2.2 Meta State
Meta state contains metadata data is set by the Miner based on the transition consensus rules
● A rating is set when other Accounts in the block have Objects with Ratings set on this  
Account Object. The miner must calculate the correct rating by averaging the total score  
value (sum of 0­10 rating scores) with the total score count which is specified in the  
previous transition for this Account
● A balance is set, equal to the balance at the last Transition in a block, plus the sum of  
any topup subscription transactions, minus the fees for this update
● The status is an integer determining if the account is active (e.g. not closed or banned),  
and more statuses can be added later
5.2.3 Quorum Sigs
●
Quorum Sigs are signatures for the Transition Data section only from the Masternodes  
that submitted the transition, and are used for Proof­of­Service and can be pruned after  
the next MN reward cycle concludes (e.g. 2 weeks)





www.dash.org  
33
Dash Evolution


5.2.4 Transition Structure

Section
Owner State
(Created by
Account
Owner)
Meta State
(Created by
Miner)
Quorum
Commit
(Created by
DAPI
Quorums)
Field Name
Type
Size
Description
RegTX  uint32_t  32  Hash of the Account’s initial subscription
transaction (the registration tx)
DataHash  uint32_t  32  Root hash of the Account data for this
transition
PrevDataHash  uint32_t  32  Root hash of the Account data before
this transition
AccNonce  uint32_t  4  Incremented integer for each Transition
for each account
DataSize  uint32_t  4  Size of account data being committed in
this Transition. Used for fee calculation
Sig  char[]  71­73  Signature of the data for the most recent
pubkey entered in a Subscription
Transaction  
PrevTsHash  uint32_t  32  Hash of the previous Transition for this
Account
RatingCount  uint32_t  4  RatingTotal  uint32_t  4  Updated rating based on other accounts
commit Objects that rate this account in
this block
FeeBalance  uint32_t  4  Updated fee­balance after this state
transition fee is deducted for inclusion in
a block.  
QuorumSigs  char[]  213­
219  Sig of Owner State hash by Quorum
Masternodes. Excluded from State
Transition hash and used only for
Proof­of­Service.   Can be pruned after
the next MN reward cycle.

www.dash.org  
Dash Evolution

34

5.2.5 Incentives
The incentive for Masternodes to form quorums to accept and propagate state transitions and  
store their data is because Masternode rewards are dependant on achieving a minimum quota  
of state transition in blocks based on the total Masternode activity within a fixed time.  
The incentive for Miners to add state transitions to blocks is because they earn fees on each  
transition added which are deducted from Account’s tallied balance and issued to the miner in  
an additional coinbase output paying the sum of all fees on state transitions included in the  
block.
5.2.6 Transition Verification
Each node verifies a transition as follows:
Owner State
●
●
●
●
●
●
●
●
●
●
●
Check if the state transitions are well formed and use the correct syntax
Check the associated Account is valid and open status based on the associated  
subscription transactions starting with the referenced registration tx hash
Check the previous data hash maps to last data hash confirmed in a block for this  
Account
Check the AccNonce is greater than the AccNonce in the previous transition  
Check if the Account fee­balance can afford the commit cost (balance ­ fees > 0)
Add the fees (fee * numUpdates) to the coinbase (this is deducted from the user’s burn  
tx by checking the blockchain)
Check the datasize is valid for the total data size of Objects committed
Verify Objects provided with the transition are well formed and use the correct syntax  
Validate Object properties using the rules in their associated Schema definitions
Verify that relations in Object headers map to valid Objects in the Active Set o
Verify the owner sig is the transition hash signed for the public key associated to the  
pubkey in the account’s active subscription state (in the metadata of the most recent  
‘register’ or ‘resetkey’ subscription transaction)
Meta State
●
●
Check the previous transition hash is the last transition for this account
Check the rating data & fee­balance (described later)
Quorum Commit
●
Note, quorum sigs are pruned after the next Masternode reward cycle, so this verification  
only applies to new blocks or blocks within the current reward cycle:

www.dash.org  
Dash Evolution

35

●
●
Determine the correct quorum for the Account at this height
Verify the quorum sig based on pubkeys of a minimum of 3 out of 5 Masternodes  
5.3 Block Protocol
Consensus rules on the validity and sequence of State Transitions added in blocks are  
analogous to those of Transactions in the existing Dash protocol.
Miners collect new Transitions into a block where they’re hashed into a Merkle Tree with only  
the root included in the block’s header, enabling Clients to validate whether a specific Account  
State exists in a block using a simplified verification process and enabling state transitions for  
closed accounts to be pruned.
The root hash of all Transitions in the block is hashed as part of the block header during  
Proof­of­Work to gain consensus on the new state of all accounts that have transitioned since  
the previous block.

State Transitions in a Block
The solution is scalable because each Account can have only one State Transition per block  
regardless of the amount of data that changed, and thus we minimize the impact on block  
growth to (around) 200 bytes per Account whose state has changed per block. If there were 1  
million unique users updating their Account once per day, this would add roughly 347Kb per  
block with Dash’s average of 576 blocks per day at the 2.5 minute target interval.




www.dash.org  
36
Dash Evolution


5.3.1 Block Header
Block headers for Evolution blocks are defined as:
Status
Existing
Dash/Bit
coin
Protocol
Evolutio
n
Protocol
Field
Type
Size
Comments
version  int32_t  4  Block version
prev_block  char[32]  32  The hash of the previous block  
merkle_roo
t  char[32]  32  The reference to a Merkle tree collection which
is a hash of all transactions related to this block
timestamp  uint32_t  4  A timestamp recording when this block was
created  
bits  uint32_t  4  The calculated difficulty target being used for
this block
nonce  uint32_t  4  Nonce used to generate this block
txn_count  var_int  1  Number of transactions, always 0  
tsn_root  char[32]  32  The reference to a Merkle tree collection which
is a hash of all State Transitions related to this
block
5.4 Block Validation
Additional consensus rules for Block validation are:
●
●
●
●
Verify each transition
Check only one transition is added per account  
Verify the transition merkle tree root hash by hashing the transitions
Check the coinbase transaction fee output amount is the sum of all transition fees
Altered consensus rules for Block validation:
●
Header hash must include the tsn_root




www.dash.org  

37
Dash Evolution

6 Drive
Drive is the storage mechanism for Account Objects, which are the data notarized in the  
Transitions between account states stored in blocks.
6.1 Data Types
For each State Transition added in a new block, Drive stores:

1. The merkle tree hashes for all Objects in the Transition
2. The Object headers, containing properties for identity and relational validation
3. The Object data, containing public and private properties for the Account and related  
Accounts


www.dash.org  
Dash Evolution

38

6.2 Committing Data
Objects are committed to drive within differential data transitions that accompany Account State  
Transitions in each new block. By traversing all data transitions for an Account since it was  
registered, an ‘Active’ state can be resolved to represent the full current set of an Account’s  
data, which is updated whenever a new block contains a transition for an Account.


Resolving the Active State from differential data transitions


www.dash.org  
Dash Evolution

39

6.3 Scalability
This provides a scalable solution because nodes only need to keep the current state of an  
Account‘s data, i.e. the Active dataset, and update this on new State Transitions. This means  
that the data for many differential states can be pruned, for example a user updating their profile  
100 times results in only 1 copy of the profile Object being stored on nodes.
An exception to this is that some differential states need to be kept for a limited period for block  
validation, based on the prune depth definition for the Object type in the schema.
This design is aimed at every­day user access patterns, because for example, a typical user  
won’t care about seeing all past revisions of their profile information, they are just concerned  
with the active set that other users will see and nodes will independently verify. If data revisions  
from the inactive set are needed by a user and they were pruned from the network, only a single  
copy of a revision is needed to restore the state because the hash of the data can be validated  
against the associated State Transition in a block and the signature validated against the user’s  
Subscription Transactions. This means that users who want to keep revisions can recover the  
data from e.g. a user’s local backup (which a Client could be configured to keep), or from a  
website listing historical data and validate the Object’s authenticity and presence on the  
blockchain. Alternatively, users wishing to keep every revision can run their own fullnode with  
pruning disabled on their account.
Effectively, Account data are notarized in blocks, with the notarized data stored in parallel  
storage that’s pruned to a set­size relating to the number of end­users rather than the number of  
transactions (and therefore new addresses) that they are adding to the chain.
The key reason to use a secondary store that extends each block is that blocks are best suited  
to retain full data on the transitions between states, whereas Drive’s main use­case is to  
maintain a current ‘Active’ state for Account data, with deprecated states being pruned at a  
certain depth, e.g. after 1 month (depending on the Object definition in the Schema) .







www.dash.org  
40
Dash Evolution


6.4 Storage Architecture
The requirement of how to store Data Transitions in Drive, i.e. storing differential sets of Objects  
associated to transitions of Account states included in blocks, is similar to storing rows in a  
database table but in a database that keeps past versions of the row as new transitions arrive  
with the last row added considered as the active or latest row. This is similar to the properties of  
a Data Cube, where data are partitioned into dimensions that maintain historical as well as  
current versions of the data.
Using this principle, we can note that all Objects in Drive are owned by Accounts, meaning  
Accounts are the top level partition, or dimension, for Objects in terms of organizing their  
storage strategy.
The second dimension in drive occurs from the fact that data is always added in conjunction  
with a new block, with the Objects tied to a state transition in a block through the root hash of all  
Objects related to that transition.
When new Accounts are registered in a Subscription Transaction, a new Account partition is  
created in Drive. As Account owners create state transitions, the corresponding data transition  
for new or updated Objects in the Account are committed to Drive as a new row, with the first  
row representing the resolved Active State of all past data transitions for the Account.
We can model this structure as a data cube, with the total set of registered Accounts forming the  
X­axis, and the Z­axis representing historical additions and updates of Objects leading up to the  
current, active state.

2­dimensional storage of Account Data Transitions

www.dash.org  
41
Dash Evolution


We can create a 3rd dimension in the data cube on the Y­axis by grouping Objects by Schema  
type, which enables optimizations to be made based on the different usage and verification  
requirements of types, for example constructing and verifying meta state transitions for Object  
ratings requires fast searching of Rating trigger Objects by miners preparing a new block to  
minimize verification costs.


3­dimensional storage of Account Data Transitions by Object Type

The diagram shows Objects within a data transition segregated by Schema type, with types  
separated along the Y­axis of the data cube.




www.dash.org  
42
Dash Evolution


6.5 Data Partitioning
Because Object data are always grouped by Accounts that are atomic (that can only be updated  
by Account Owners without any transfer of data ownership or rights), the Drive data can also be  
pruned on a per­account basis on Nodes without the capacity to store the full Object dataset,  
either randomly or based on Accounts that are closed or haven’t had activity for a long time (e.g.  
12 months).
In such a case, only a single node with a copy of the data is required to restore it, or the data  
can be recovered from a backup or archive source.
One limitation to partitioning the data as such, is that the Object headers containing the foreign  
key relations are needed to perform relational validation historically, for example, to validate a  
contact request from Alice to Bob, the consensus rules dictate that Bob must exist to maint the  
relational integrity of the overall Object set in Drive.
Below we illustrate (generically) depths at which Object transitions can be pruned based on the  
PruneDepth specified in the Object’s Schema definition, shown on the Z­axis of the data cube.

Pruning Transitions by Object Type






www.dash.org  
43
Dash Evolution


The main reason to use an informal, ad­hoc approach to partitioning instead of a formal  
sharding strategy is that formal sharding can weaken the durability of the data because  
attackers can target specific data with knowledge of the subset of nodes that are storing that  
data. There is also an overhead to formal sharding with the need to organize and rebuild shards  
as nodes turnover, as opposed to nodes for example randomly pruning old Accounts when they  
run low on disk space.
The diagram below shows an ad­hoc Account pruning strategy, with 2 of the 6 accounts pruned  
along the X­axis of the data cube.

Account Pruning





www.dash.org  

Dash Evolution
44

7 Decentralized API
DAPI is the decentralized Application Programming Interface that enables Evolution end­users  
and applications to connect directly to the Dash P2P network to read/update Account data and  
read/create Transactions using HTTPS enabled clients such as browsers and mobile  
applications.
7.1 Network Architecture
DAPI is part of a re­architecting of Dash’s network design in Evolution to introduce a way for  
Clients to access the P2P network securely and directly.
This is because in the current design inherited from Bitcoin, there is really no suitable protocol  
level definition of a Client as there is in most service models (such as Client­Server), as every  
user is expected to access via a P2P node and interact as a peer directly, which is an  
understandable assumption in the early versions of Bitcoin, where every node was a miner,  
auditor, and maintained a full local copy of the blockchain, without foreknowledge of the  
segregation of roles introduced with developments such as pools, asics and non­mining  
(non­rewarded) fullnodes, and a mass exodus of desktop users to browser/mobile based  
applications since Bitcoin was launched.
There are different modes of P2P node that users can operate under in the existing  
architectures, with the closest thing to a client being a user operating a node without a copy of  
the blockchain and using SPV validation over P2P messaging, essentially a selfish node, and  
when mediated via a centralized proxy, referred to as a ‘lite client’, e.g. Electrum.
For these reasons, DAPI is really the endpoint for a new Client Protocol that is adjacent to the  
existing P2P Network Protocol, with Clients being any device running software that connects to  
DAPI over HTTPS using the correct interoperation protocol.
7.1.1 Security Model
The security model for DAPI and its clients is based on this non­P2P selfish SPV node model,  
whereby Clients can connect to Nodes and add data to the blockchain (Transactions and  
Transitions) without needing to participate as peers and using the most commonly supported  
and censorship resistant protocol, HTTPS. Clients can also access any node in the network to  
validate Transaction and Transition data using SPV over HTTPS.
Another aspect to mention regarding DAPI’s security model is that it is based on full ownership  
of private keys by client users, with private keys never entering DAPI, i.e. DAPI nodes cannot  
steal users funds. DAPI nodes also never serve code or content, e.g. JavaScript or HTML to a  

www.dash.org  
Dash Evolution

45

browser; DAPI is purely an XHR over HTTPS based API accessed by (ideally)  
deterministically­built open­source clients.
In addition, DAPI nodes must work together in 3­of­5 Masternode Quorums and agree on the  
validation of Client requests and the content of Client responses, with the Quorum formed  
deterministically based on a hash of the user’s Account public key and a recent block hash to  
prevent pre­planned targeted attacks on individual sets of nodes. DAPI Quorums provide  
redundancy to uncommitted Client session data and reduce the chance of malicious nodes  
wasting Client time with responses that Clients subsequently invalidate using SPV (with the  
Client SPV process being applied externally to the Client’s Quorum, i.e. network wide).
7.1.2 Network Topology
The connection topology for Dash then bifurcates into 2 rings in the network. The current P2P  
network topology (technically a partially­connected mesh) becomes the inner ring consisting of  
P2P nodes that validate, persist and provide the blockchain to other P2P nodes, with an outer  
ring consisting of individual Clients connected directly to a cluster of collateralized P2P nodes  
serving HTTPS requests (Client / Multi­node­server) instead of intermediary proxy services that  
connect P2P on the backend, which we call the Client­to­Peer (C2P) network, technically a  
Client­to­collateralized­Peer­quorum network, also known as Tier­3 in Dash.
One issue with this structure is the incentives model or specifically lack of incentivizes for  
fullnodes to support a very­large amount of selfish nodes; currently the incentive model is pure  
p2p based, i.e. overall the p2p network survives with enough nodes seeding (operating as  
relaying fullnodes accepting inbound connections) in the network to handle the additional traffic  
from a relatively small amount of leechers (mostly desktop wallets, centralized proxies such as  
SPV proxies, web wallets and payment processors) which end­users and applications connect  
to. By removing the need for leechers to connect via proxies (i.e. the majority of end­users not  
wishing to participate or support the P2P network directly) , and therefore resulting in a large  
increase in selfish nodes (clients who can now access the network directly instead of via  
centralized SPV or Web wallet proxies), the cost to running a fullnode increases and the  
incentives model of the P2P network is broken.
In Dash obviously nodes are incentivized to provide non­mining services but not specifically to  
handle this new topology, i.e. currently nodes could still be rewarded without provably serving  
these end­users honestly. To solve this, we alter collateralized nodes (Masternodes) rewards to  
be provisional on the amount of Client data they add to the blockchain (technically, the quantity  
of Account State Transitions), which provides incentives to users who choose to operate nodes  
that will serve HTTPS Clients and a deterministic way to ensure only nodes providing an  
adequate (and honest) Client­service level are rewarded.



www.dash.org  
Dash Evolution

46

7.2 Client Protocol
There are two modes of connection Clients can use:
●
●
Passive Sessions   ­ Anonymous, read­only access to the Account API (to query
Account data), and anonymous read/write access to the Payment API (to query the
blockchain and create transactions)
Interactive Sessions   ­ All the abilities of a passive session plus the ability to
pseudonymously update the data for Accounts to which the user holds the private key.
Requires nonce­based mutual authentication between the Client and the Masternode
quorum maintaining the session.
When an Account Owner wants to start an interactive session (i.e. update their Account state)  
with DAPI from a Client, there are several steps they need to perform. Note this is not needed  
for reading data on any Account in Dash, as clients can query any DAPI node for the data  
anonymously. Also, this does not preclude users accessing DAPI from a local fullnode if they  
want full validation of all interaction using a full copy of the blockchain and related data.
The first step is to obtain a list of valid Masternodes, and secondly to determine which  
Masternodes their client must connect to to be able to update their account state.
7.2.1 Obtaining the Masternode list
A Client can connect to any number of nodes in the Dash network to obtain the Masternode list  
and validate its contents using SPV, using essentially the same security model as SPV/Electrum  
clients but with a much higher degree of decentralization, i.e. Clients can access any node in the  
Dash network instead of having to proxy through a small set of centralized layer­2 servers, and  
as that access is HTTPS based, it is available from any HTTPS enabled Client, such as a web  
browser
Clients can use HTTPS DNS seeds that the community setup to build an initial list of  
Masternodes to connect to in the same way as the core wallet today.
Once connected to some initial DAPI nodes, the Client can construct a list of all Active  
Masternodes and their IPs and VINs, and validate the Active status using SPV on the  
Masternode Account Objects to avoid spoofed nodes from a DNSseed.




www.dash.org  

Dash Evolution
47

7.2.2 Quorum Sessions
Quorums are determined using the same rules as the current Dash protocol but based on the  
RegTX hash of the Account who is accessing the Quorum.
Once a Client has constructed a valid active Masternode list, they determine their quorum and  
connect to it using an authentication process specific to DAPI: DAuth, or Decentralized  
Authentication.
The principle of DAuth is that, instead of a one­way authentication as in the case of  
client­server, where the server authenticates the client, both parties in a point­to­point  
connection between two parties (e.g. Client to Node in DAPI) authenticate each other, and using  
a decentralized source (e.g. SPV verifiable data sourced from any node in the network).
Once both parties (e.g. client and masternode) have authenticated each other, they  
communicate through a secure channel where each message has to incorporate a deterministic  
challenge in the payload derived from an HD seed provided on session initiation with all  
messages signed by and encrypted for the other party to prevent MITM and Spoofing attacks,  
similar to an HMAC.
Note: Typically Alice would be an Evolution client like a web browser and Bob would be a  
Masternode, but these roles are not hardcoded into the protocol.

This provides a high level of security in terms of message authenticity and integrity and is the  
basis of Client­node connections in DAPI.

www.dash.org  

48
Dash Evolution


7.3 Quorum State Transitions
7.3.1 Creation
Transitions from users on their Accounts are created during authenticated interactive  
Client­Quorum Sessions, during which time the designated Quorum caches any updates to the  
Account’s Data Objects and at set intervals (2.5 minutes) propagates these as a batch  
representing a new Account State to nodes in a State Transition data structure.  
The State Transition must contain a complete updated Owner State, and a partial data section,  
containing only new/updated Objects, and their index in the derived merkle tree. The merkle  
tree hashes between the root and leaf nodes aren’t needed within the data as validating nodes  
can build these locally.

If validated by a miner, the State Transition has its data included in a block by miners for a fee  
deducted from the Account’s tallied fee­balance, and its data (that was notarized by the  
transitions inclusion in the block) included in Object Storage by Masternodes who must provably  
facilitate a minimum quota of State Transitions per payment cycle relative to the total volume  
and number of nodes, to receive rewards from the infrastructure portion of the block reward.
We call the 2.5 minute State Transition propagation by Quorums a ‘heartbeat’, which is  
designed to minimize the frequency of transitions to an account on the blockchain (and fees  
deducted from the Account), whilst still occurring frequently enough to provide usability. Client’s  

www.dash.org  
Dash Evolution

49

have the ability to control this frequency, or raise a state transition at any time to ‘save’ their data  
in the next block..
As users can connect with multiple devices under the same Account, in such a case the  
Quorum also resolves a single update path to the Account Object set with the client first to  
construct a single Transition, as blocks must contain zero or one Transition objects per Account  
per block.
7.3.2 Transition Authorization
Once the Quorum has assembled the State Transition, it sends the header (which includes the  
root hash of the data) to the Account’s connected Clients for authorization using a web­socket  
callback or as part of a poll response.
Each Client then compares the root hash of the merkle tree for their local Object set to the root  
DataHash in the State Transition header, and if the header is valid, signs the header hash with  
their Account private key and sends it back to the quorum who propagates it to the P2P  
network.
7.4 Simplified Verification
The SPV implementation for the Client protocol is being prepared in a separate paper.



www.dash.org  
Dash Evolution

50

8 DashPay Model
DashPay is the JSON Schema Model that specifies the Object types and rules that implement  
the Dash Evolution features to end­users. DashPay is in effect a decentralized application  
running on top of Dash where end users and applications can interact trustlessly via state  
sequences within the rule­set defined in the Schema.
We can represent the Objects in the Schema Model at a high level using a UML  
Composition­style diagram:

DashPay Schema Model Composition
Note: JSON and State Sequences for the Model are being prepared in a separate paper.



www.dash.org  

Dash Evolution
51

9 Triggers
Triggers are Objects types that have hardwired functions in nodes, such as rating accounts,  
budget cycle and masternode payments, meaning essentially consensus rules depend on the  
data a small number of types in the Schema.
We don’t need to wire the actual derived object types, just signify in the Schema that certain  
Objects are types that cause triggers.
For example, if a User rates an App in the header of their UserApp Object, that Object has a  
type in the Schema signifying it has a header that raises ratings.
Therefore, nodes can remain agnostic to the specific Schema data model, but when Objects  
have Trigger types, use those Object data for predefined trigger functions.
Note that trigger objects will usually have a higher prune depth, for example, all Budget objects  
need to be kept for at least 1 budget cycle (roughly 1 month) for nodes to be able to validate  
Superblocks using consensus rules.
9.1 Ratings
Ratings are intrinsic to Evolution, as they allow a democratic and decentralized way for users to  
apply a score to other Accounts (such as Users and Apps) that they use, and to report users  
that break the Dash Terms of Service which can then be closed by Masternode voting, if a  
minimum consensus is reached.
Note that having an Account closed can never result in the loss of funds by the Account owner  
or preventing an Account from moving their funds, instead, the Account is banned from creating  
any State Transitions for the Account, meaning they cannot create or update new data Objects.

www.dash.org  
Dash Evolution

52


Absentee Account Rating via a Delegate State Transition
In the above example, the process is:  
1. Alice and Bob both set a rating on their UserApp Object for Charlie’s App, which is  
selling widgets.  
2. A miner collects the State Transitions for Alice & Bob’s update into the block and detects  
the presence of Rating values in the Object dataset
3. The miner must tally the ratings (based on the total ratings for Charlie’s App to date, and  
the average rating, combined with any new ratings in this block).
4. Because Charlie has not updated his Account in this block, the miner must create a  
Delegate State Transition in his absence, which updates the metadata but leaves the  
Data Transition as null (or just a hash of the last state transition by Charlie
5. Validating nodes must repeat this process to validate that the miner has included the  
Delegate State Transition for any State Transitions the miner included that contain  
ratings for Charlie’s account
6. This incentivized the miner to create the Delegate State Transition data even though  
they are not claiming a fee directly, because they can’t include the State Transition from  
Alice & Bob’s accounts without this Delegate State Transition to handle the change in  
Charlie’s rating meta state.




www.dash.org  

Dash Evolution
53

9.2 Masternode Shares
Masternode shares enable Account holders to group together to collateralize a Masternode  
Account operator (which links to a physical Masternode instance in the P2P network), instead of  
only users with 1000 Dash being able to operate a Masternode. In this Schema design, share  
owners receive their share of the rewards deterministically but only the Masternode account  
holder can vote on behalf of the node, but that can be added in the Schema later.
1. Users who wish to obtain MN shares (e.g. Alice & Bob) send an amount (e.g. 500 Dash  
each), to an address to which they own the private keys themselves with specific  
metadata signifying this as a collateral TX, and with a CheckTimeLockVerify preventing  
movement of the funds for 30 days (this is a measure to reduce turnover rate of MN  
share owners initially, if they stay with a particular MN operator for over 30 days they can  
move the funds instantly)
2. A Masternode Account owner (e.g. Charlie) then ‘claims’ Alice & Bob’s collateral TX by  
adding the TX hashes to a new CollateralStatus Object in his dataset (or updating the  
existing CollateralStatus Object if one exists), provided no other Masternode Account  
has claimed them.
3. To verify that the Masternode Account is fully collateralized, nodes check the Account’s  
CollateralStatus Object data as follows:
a. The Collateral TX’s listed in the Object are unspent and not claimed by any other  
Masternode Account.
b. The Collateral TX’s sum to at least the minimum collateral requirement (1000  
Dash)
4. The maximum number of shares is 20 per Masternode, but individual shares can be any  
amount over 5 Dash. The Masternode Account owner must provide at least 10% of the  
total requirement themselves, to increase the cost to setup a malicious masternode.
5. Note that the share owners are not risking funds as they do not share their private keys,  
apart from opportunity cost or the risk of the Masternode operator not running their node  
with enough uptime or service level to receive the reward.
9.3 Proof­of­Service Verification
Proof of Service is a deterministic verification technique that ensures that Masternodes are  
providing adequate service levels to the network before being paid any reward, and is key to  
maintaining the incentives model of Dash Evolution. This is because if we don’t force  
Masternodes to provide an adequate level of service in providing DAPI to Clients as well as their  
new increased storage obligations, they can lower their costs by bypassing checks and not  
providing services and storage that will cost them additional bandwidth, disk and CPU/Memory,  
which results in less throughput, durability and availability for the Dash Network which translates  
to a less secure and less available service for Clients ­ it is fundamental to the long term  

www.dash.org  

Dash Evolution
54

economic viability of Dash as a decentralized cryptocurrency, and to date there has been no  
deterministic solution, i.e. bypassable with enough effort (even though there has been no  
indication that any nodes have been modified to bypass existing non­deterministic PoSe).
1. Tally the total number of State Transitions in blocks within the current Masternode  
payment cycle (e.g. 2 weeks)
2. Divide this number by the average number of active Masternode Accounts within the  
current MN pay cycle.
3. Tally the total number of State Transitions by the Masternode Account being verified for  
PoSe, determined by the quorum signatures on the State Transitions (which can be  
pruned after a single MN payment cycle)
4. If the total is less than a % of the average (e.g. median * 0.5), the MN fails PoSe for that  
cycle.
Therefore, Masternodes must be committing State Transitions to a minimum quota to get  
rewarded in a payment cycle, and as each transition has a fee, so Masternodes cannot create  
the State Transitions themselves as it would be a zero­sum­game (and also the MN operator  
cannot predict which Accounts he/she is designated to serve as quorum determination is based  
on a recent block hash).
9.4 Masternode Rewards
As each Masternode has an Account object in Dash Evolution, we can get a deterministic list of  
all active Masternodes (and quorums) at any block going back through the chain (since  
Evolution was activated). Therefore the list can be retrieved by querying all active MNs at a  
given block height and checking their active state. A short term PoSe verification is performed  
(e.g. hourly) that just checks if any State Transitions have been handled by a MN, to see if the  
MN is active, alternatively allow direct query to the MNs using the existing MNAnnounce  
message data.
9.5 Governance
There are 2 Trigger Objects used for the Governance budget cycle:   AppBudgetProposal and
MasternodeBudgetVotes Objects, handling budget proposal creation and voting by
Masternodes.   The state of these Objects across the global Object set then determines
Superblock creation and validation by nodes, which pay out the 10% of Block rewards each
month to the winning proposals (using the existing consensus rules for SB validation).
9.5.1 Proposal Creation
Apps can create a new budget proposal by creating a new AppBudgetProposal and setting the  
properties for Name, Description, URL, PayDate, NumPayments, PayAmount, PaymentAddr.

www.dash.org  

Dash Evolution
55

9.5.2 Voting
To vote on a proposal, a Masternode Account owner updates their MasternodeBudgetVotes  
Object with a reference to the AppBudgetProposal, and adds their yes/no/abstain via the Vote  
property. Note that the MasternodeAccount has to pay fees for a State Transition to cast the  
vote (unless a solution can be found).
9.5.3 Super Blocks
Superblocks are created deterministically by Miners in Evolution, by the Miner querying and all  
MasternodeBudgetVotes Objects within State Transitions included in blocks since the last  
budget cycle period and tallying the votes.  
The miner then adds the appropriate associated payments in the coinbase transaction (using  
the budget finalization rules from the current system) with the process repeated for verifying  
nodes.
Note that nodes must maintain full object data for all governance objects to a certain depth (e.g.  
not prune until 1 month)
9.6 System Admins
In Evolution version 1, administration of the system (i.e.deactivating Subscriber Objects) will be  
a simple function of achieving a certain % of Masternode votes (e.g. 25%) to ban Accounts,  
however, in future a more granular and incentivized approach can be taken.
To ban an Account, users can ‘report’ an Account using a similar system to ratings. Masternode  
admins can then vote on these bans using their AdminVotes Object, which miners must create a  
delegate state transition for the Account being voted on with a ban rating and total in the next  
block. When the votes reach a ban level, the miner of the next block must set a delegate state  
transition for the Account being reported with the Status set to ‘Closed’ for the block to be  
accepted via consensus rules.

www.dash.org
