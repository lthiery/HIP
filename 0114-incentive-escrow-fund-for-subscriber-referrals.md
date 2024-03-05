# HIP 114: Incentive Escrow Fund for Subscriber Referrals

- Author: [@zer0tweets](https://github.com/zer0tweets)
- Start Date: 2024-03-12
- Category: Economic, Technical
- Original HIP PR: [#927](https://github.com/helium/HIP/pull/927)
- Tracking Issue: [#942](https://github.com/helium/HIP/issues/942)
- Vote Requirements: veMOBILE Holders

---

## Summary

Service Provider rewards will be used to programmatically issues various incentives (like referral fees) to grow Helium network subscriber base.
    
##  Motivation

Helium Network utility is a function of the number of subscribers sending paid data to the network. I.e. we want more subscribers. Per HIP53, service provider rewards bucket was originally designed to empower service providers to bring more subscribers to the network using token incentives.
Today, the service provider bucket is mostly unused. Some rewards go to Helium Mobile for the traffic, but 90%+ of it is just never emitted

We propose a referral incentive program that would channel rewards to the community referring users that take advantage of un-emitted MOBILE in the service provider bucket
    
## Stakeholders

-   Service Providers will have a new capability to create referral incentive programs using unused rewards from service provider bucket      
-   Builders will be able to earn MOBILE rewards for referring subscribres 
-   Subscribers may be eligible for "referral discounts" in the form of MOBILE, if they stay with the network for a period of time 
    
## Detailed Explanation

-   Service Providers get the ability to delegate a percentage of their rewards towards a programmatic “incentive escrow fund”

-   Incentive escrow fund is specific to a service provider and multiple funds can exist for different service providers. I.e. Helium Mobile can run its own, Telefonica (when and if approved as an SP) can run its own

-   It is up to the service provider to define and change the percentage at any time. I.e. Helium Mobile can start delegating 50%, keep it for 1 month and then change delegation percentage to 0
    
-   Funds delegated by a Service Provider towards the incentive escrow fund are matched 1:1 by any unused emissions from the Service Provider bucket. E.g. If a Service Provider is earning 20% of the total SP bucket, and they delegate 50% of their earnings to the incentive escrow fund (I.e. 10% of all emissions), then 10% of the unused emissions, if available, will match this and a total of 20% of all emissions will go to the incentive escrow fund. In the case where there are insufficient unused emissions, the maximum available will be contributed; in the case where multiple SPs are making contributions, their respective incentive escrow funds shall receive a pro-rated contribution of available funds according to their own absolute contribution (ie: the total MOBILE contributed determines their ratio, not the percentage of their allocation).

-   Service providers can define and run various incentive programs that would reward the community for growing subscribers, using MOBILE delegated into the incentive escrow fund

-   The Service Provider shall be responsible for monitoring escrow funds dedicated towards a subscriber referral program and is expected to limit or suspend the program before escrow funds can be entirely depleted.    
 
-   MOBILE in the escrow fund cannot be used for anything other than programmatic incentive programs aimed at new subscriber acquisitions (i.e. referrals or “new subscriber” bonus rewards)
    
-   Any MOBILE that has not been used by the referral program for a period of 12 calendar months from the moment of the original deposit into the fund is burned
    
### Example Incentive Program Proposed by Helium Mobile

Hotspot owners are incentivized to refer subscribers to the network. Each hotspot is able to generate a referral code, unique to that hotspot. For each successful and qualified subscriber that registered for the network and used said referral code, hotspot owner earns $10 in MOBILE immediately, then another $20 in MOBILE over 5 months, provided the subscriber is still active and in good standing. The gradual reward payout promotes subscriber retention and ensures a higher-quality subscriber base (an anti-gaming mechanism). Referrals are paid out of the incentive escrow fund.

## Drawbacks

This proposal introduces an additional layer of complexity to the already complex reward mechanism.

It may also be argued that allowing service providers the freedom to define the structure of a referral program is prone to gaming. Poorly designed and implemented referral schemes could result in exploits where referral payouts for fake users.

Finally, since this proposal does require that incentive programs launched by service providers require that referred subscribers send data to the Helium Network, service providers will be using “network’s emissions” to attract subscribers that will bring revenue to the service provider, but not the network.

## Rationale and Alternatives

An alternative would be for service providers to pay token rewards directly to network participants vs. using an incentive escrow fund. Depending on the regulatory framework governing crypto in the country where the service provider is domiciled this may involve varying levels of regulatory / compliance risk.

## Unresolved Questions

Do we need to implement a gating mechanism, such that referral payouts coming from the incentive escrow fund require that any referred user actually used data on the Helium network vs. just becoming a subscriber with one of the network approved service providers.

## Deployment Impact

Nova Labs will collaborate with the foundation engineering team to develop detailed architecture and roadmap.

## Success Metrics

This HIP would be considered successful if a referral program launched by at least one service provider resulted in at least 50,000 new network subscribers/users sending at least 6TB of paid data to the network per day.
