---
title: "Delta"
date: 2021-08-12T22:37:30-05:00
tags: ['options', 'trading']
draft: true
---

## Rate of Change

Delta is the measure of the rate of change in the price of the option per $1 change in the underlying.

- If the option is very deep ITM, the value changes at a rate almost 
identical to that of the underlying.
- If the option it far OTM, the value may change only slightly, even with a 
large change in the price of the underlying.
- Many consider delta to also be viewed as the "probability the option will
finish ITM". So a 50 delta option is seen as having 50/50 chance of finishing 
ITM. All options that are ATM have a delta of 50.

In theory, an option can never gain or lose value more quickly than the 
underlying, so delta always has an upper bound of 100. A call cannot move in 
the opposite direction of the underlying market, so the delta of a call has a lower bound of zero.

### Examples:

A call with a delta of 25 can be expected to change its value at 25% of the 
rate of the stock. If the stock rises or falls $1.00, the option will be expected
to rise or fall $0.25. A 75 delta call can be expected to change at 75% of the 
rate of the underlying. So, if the underlying rises $0.60, the call option can be expected to gain $0.45.

### Puts

Puts have characteristics similar to calls, except put values move in the 
opposite direction of the underlying market. Deltas for puts range from 0 to -100.
A put with a delta of -10 can be expected to move at 10% of the rate of the underlying.
So if the underlying moves up $0.50, the put will lose $0.05 in value.

## Hedge Ratio

If we wish to hedge an option position against the underlying contract, the 
delta tells us the proper ratio of underlying contracts to options required to 
establish a neutral hedge. An underling always has a delta of 100, so the proper
hedge ratio can be found by dividing 100 by the options delta. ATM options have
a delta of 50, so the hedge ratio is 100/50 or 2/1.

**For every two call options purchased, we need to sell one underlying to have a neutral hedge**.

- This is for hedging the purchase of calls. If we buy calls, we sell stock to hedge.
- If we buy puts, we buy stock to hedge.

For puts, since they have negative deltas, we will be required to purchase the 
underlying to hedge the position. A -75 delta put will require the purchase of
75 shares of the underlying for every put purchased. Or viewed another way, 100/75,
it will require 3 contracts of the underlying (300shares) for every 4 puts.
