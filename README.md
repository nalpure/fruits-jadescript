# Fruits-Jadescript

**University Project - Multi-Agent System for Fruit Buying and Selling**

## Introduction

Fruits-Jadescript is a university project implemented in Jadescript, an AgentSpeak Java Interpreter. Jadescript is a programming language, released in 2022, which was designed to ease the development of multiagent systems. Internally it makes use of [JADE](https://jade.tilab.com/) agents and therefore complies with the [FIPA specifications](http://www.fipa.org/).

## Project Overview

The main goal of Fruits-Jason is to demonstrate a multi-agent system where the buyer agent interacts with seller agents to make optimal purchases based on price.

### Agents

- **Buyer:** Initiates the buying process, gathers information about available products from sellers, and makes purchases based on the best price.

- **Sellers (Alice, Bob, Charlotte):** Provide information about the products they have, including item type, quantity available, and price. Adjust their inventory and money based on successful transactions. 

The code of the three selling agents is almost identical, only differing in their initially available offers. Whilst agents can take *'on create'* arguments when being run, this feature is rather limited. Therefore this unpretty solution of copied code was chosen. 

### Buyer behaviour

1. The buyer agent sends a message to all the selling agents she knows and requests their offers for fruit.
2. The buyer activates a cyclic behaviour: whenever she gets a respond to previous request, she saves this offer in her properties.
3. The buyer waits some time, in order to ensure that all offers arrived.
4. For each needed fruit type, she does step 5.
5. Buys fruits of specified type, until she does not need more:
    - She selects the cheapest offer for this fruit currently known to her and sends a purchase request.
    - Upon confirmation of the purchase by the seller, she updates a) money, b) available offers and c) needed items in her properties. If she needs more fruits of specified type: repeat step 5 with updated quantity.

Step 5 was implemented using a cyclic behaviour. It destroys itself, when either no more fruits of specified type are needed, or the buyer is not aware of any offers for this fruit type. The behaviour results in a blocking call if no confirmation of the purchase arrives. This could be improved by introducing a timeout after a certain time of waiting. 

In an alternative implementation, the buyer could first decide how many units of which fruit to buy from which sellers, and only then send all purchase requests at once. However this approach is not open to certain extensions to the program, which are typical for multiagent-systems. E.g. the purchase takes time, during which the buyer learns of new offers, or existing offers change.

### Seller behaviour

1. The seller agent waits for requests about her fruit offers and responds upon such requests
2. The seller waits for purchase requests and upon such request updates her fruit offers and money, and sends a confirmation message to the buying agent

Both steps are implemented using cyclic behaviours. Note that purchase requests are simply ignored, if they do not match any offer of the seller.

## Output

The following are the outputs of the agents for the initial example configuration.

### Buyer

```
INFORMATION: Let's do grocery! This is what I need: [NeededItem("apple", 10), NeededItem("orange", 20), NeededItem("banana", 50)]
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 10 apples for a price of 1 each, from seller 'Alice@172.16.3.44:1099/JADE'. Left money: 990 ~~~~~~~~~~~~~~~~~
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 2 oranges for a price of 4 each, from seller 'Alice@172.16.3.44:1099/JADE'. Left money: 982 ~~~~~~~~~~~~~~~~~
INFORMATION: The seller did not have enough units. I still need 18 oranges.
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 20 bananas for a price of 1 each, from seller 'Charlotte@172.16.3.44:1099/JADE'. Left money: 962 ~~~~~~~~~~~~~~~~~
INFORMATION: The seller did not have enough units. I still need 30 bananas.
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 10 bananas for a price of 2 each, from seller 'Alice@172.16.3.44:1099/JADE'. Left money: 942 ~~~~~~~~~~~~~~~~~
INFORMATION: The seller did not have enough units. I still need 20 bananas.
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 18 oranges for a price of 6 each, from seller 'Charlotte@172.16.3.44:1099/JADE'. Left money: 834 ~~~~~~~~~~~~~~~~~
INFORMATION:  ~~~~~~~~~~~~~~~~~ BOUGHT: 20 bananas for a price of 3 each, from seller 'Bob@172.16.3.44:1099/JADE'. Left money: 774 ~~~~~~~~~~~~~~~~~
```

### Alice

```
INFORMATION: Hello, this is what I have to offer: [Offer("apple", 10, 1), Offer("orange", 2, 4), Offer("banana", 10, 2), Offer("peach", 20, 2)]
INFORMATION: Processing purchase of 10 apples, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("apple", 10, 1)
INFORMATION: Glad having done business with you. My new account balance is: 10
INFORMATION: Processing purchase of 2 oranges, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("orange", 2, 4)
INFORMATION: Glad having done business with you. My new account balance is: 18
INFORMATION: Processing purchase of 10 bananas, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("banana", 10, 2)
INFORMATION: Glad having done business with you. My new account balance is: 38
```

### Bob

```
INFORMATION: Hello, this is what I have to offer: [Offer("apple", 4, 2), Offer("banana", 2000, 3), Offer("avocado", 17, 1)]
INFORMATION: Processing purchase of 20 bananas, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("banana", 2000, 3)
INFORMATION: added: Offer("banana", 1980, 3)
INFORMATION: Glad having done business with you. My new account balance is: 60
```

### Charlotte

```
INFORMATION: Hello, this is what I have to offer: [Offer("apple", 400, 3), Offer("orange", 200, 6), Offer("banana", 20, 1), Offer("acai", 23, 8)]
INFORMATION: Processing purchase of 20 bananas, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("banana", 20, 1)
INFORMATION: Glad having done business with you. My new account balance is: 20
INFORMATION: Processing purchase of 18 oranges, bought by Buyer@172.16.3.44:1099/JADE
INFORMATION: removed: Offer("orange", 200, 6)
INFORMATION: added: Offer("orange", 182, 6)
INFORMATION: Glad having done business with you. My new account balance is: 128
```


## How to Run

Jadescript can be installed as a plugin in the [Eclipse IDE](https://www.eclipse.org/). When installed, one of the three selling agents must be run as a 'Jadescript Agent' and run in a 'new main container'. Now the remaining two selling agents and the buying agent can be run as jadescript agents in a 'new peripheral container'. For more information visit the official [Jadescript Github Repository](https://github.com/aiagents/jadescript) and their [programming guide](https://github.com/aiagents/jadescript/releases/download/v1.0.20221015/JadescriptProgrammersGuide_v1.0.20221015.pdf).
