# HIP 53: Mobile subDAO

- Authors: [Boris Renski](https://github.com/zer0tweets), [@tjain-mcc](https://github.com/tjain-mcc), [@shayons297](https://github.com/shayons297), [@abhay](https://github.com/abhay), Joey Padden
- Start Date: 2022-01-04
- Category: Economic, Technical
- Orignal PR: https://github.com/helium/HIP/pull/341
- Tracking Issue: https://github.com/helium/HIP/issues/345
- Status: In Discussion

**Summary**

[HIP 51: Helium DAOs](https://github.com/helium/HIP/blob/main/0051-helium-dao.md) provides a general structure for onboarding new Decentralized Network Protocols (DNPs)  to the broader Helium Network, with mechanisms in place to ensure that protocol-specific attributes such as Proof-of-Coverage rules, Data Credit pricing, and block validation are within control of the emergent DNP DAO.

Today all mobile networks are built top down by service providers such as AT&T and Verizon. Future networks will likely be a hybrid combination of **macro operators** and **crowdsourced 5G/Wi-Fi hotspots** due to demands of consumer behavior (higher bandwidth and lower latency requirements) and the physics of wireless radio. More data on the wireless network requires higher frequency bands, which requires denser networks with more nodes, which further increases cost of site acquisition.

The crowdsourced model removes site acquisition costs from the equation. It is not realistic to blanket the world with Wi-Fi or CBRS in the near future and completely replace service providers altogether. Instead, we see service providers as a critical component of this ecosystem and design Helium Mobile DAO economics taking that critical tenet into account.

In this proposal, we specify the implementation of the structure proposed through a detailed onboarding proposal for the Helium Mobile Network.

# **Mobile subDAO core jobs-to-be-done**

We proposed in HIP 51 that each subDAO operate as a sovereign economics and governance layer. The Helium Mobile Network subDAO has seven core functions

1. **Economic Players**

The Helium Mobile subDAO governs the interactions between a number of entities performing functional and economic roles, critical to mobile data offload use case.

2. **Emissions Curve**

The Helium Mobile Network subDAO handles all MOBILE emissions, mining rewards, and programmatic treasury operations. The economic responsibilities around this involve managing the token issuance and distribution.

3. **Treasury Reserve and Market Making Curve**

subDAOs have full control over the prices at which the subDAO treasury provides quotes to holders of MOBILE who wish to redeem their holdings for underlying HNT. This can be a flat bid or a more complex curve.

4. **Oracle Specification**

Oracles perform work including verifying proof of coverage and data transfer. The oracles are also responsible for distributing DNT mining rewards to the appropriate parties. Responsibilities here include definition of oracle software, minimum stake amounts, and rewards for participation.

5. **Data Transfer Mechanism and Pricing**

Data transfer across subnetworks occurs via the process of procuring and burning data credits in the name of the hotspot or set of hotspots that provide coverage. Responsibilities here include Organizationally Unique Identifier (OUI) registration, state channel creation, and bandwidth capacity per data credit definition.

6. **Proof-of-Coverage Mechanism**

Most subnetworks will utilize a Proof of Coverage algorithm to verify on an ongoing basis that hotspots are accurately representing their location and the wireless network coverage they are creating from that location. Responsibilities here include Proof-of-Coverage challenge construction, target selection, reward scaling, and verification. Note that subnetworks can choose to skip this mechanism, but must provide reasoning as to why it is not necessary for proper functioning of the subnetwork.

7. **Governance Structure**

subDAOs retain control over critical components of the network, and subDAO members can propose and vote for changes to core parameters and mechanisms. Responsibilities here include specification of a formal on-chain voting process that is resistant to attacks.

The remainder of this proposal defines initial values for the subDAO given the aforementioned set of responsibilities.

### *Economics Overview*

There are a number of real world physical entities that comprise the network and perform the following functional and economic roles:
| Name              | Functional Role                                                                                                                                                                                                                                                                                                                                                                           | Economic Role                                                                                                                                                                                            |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subscriber        | Uses the network                                                                                                                                                                                                                                                                                                                                                                          | Pays service providers to access data on the network                                                                                                                                                     |
| Mapper            | Runs a network mapping app on the phone or uses a dedicated mapper device to verify coverage provided by the hotspot hosts.  Mappers are required to be a subscriber.                                                                                                                                                                                                                     | Receives MOBILE mining rewards for mapping networking coverage                                                                                                                                              |
| Service Provider  | Promotes network service to subscribers and mappers  Runs subscriber database and authenticates inbound subscribers and mappers into the network    Records data transfer transactions to state channels by running session purchaser instance Issues sim cards to subscribers and mappers Maintains their flavor of the witnessing app Responds to subscriber and mapper support queries | Receives payments for data access from subscribers and mappers  Burns HNT into Data Credits and settles data usage with hotspot hosts    Stakes MOBILE to receive % of rewards from MOBILE Emissions contract  |
| Hotspot Host      | Operates a  hotspot (can be 5G or Wi-Fi) and pays for backhaul. Note that there are two onboarding fees associated with the Hotspot Host: 1) Hotspot addition fees ($40) and 2) Assert Location fees ($10)                                                                                                                                                                                                                                                                                                                             | Receives MOBILE mining rewards for providing coverage  Receives MOBILE mining rewards for providing data access to subscribers                                                                                 |
| Hotspot Vendor    | Builds and sells hotspots  Supports hotspot hosts  Runs software to provide  updates of hotspots Maintain integrations with service providers                                                                                                                                                                                                                                             | Stakes MOBILE Receive % of rewards mined by hotspot hosts                                                                                                                                                   |
| Oracles           | Relay data to L1 emission contract Calculate incentive points for service providers and mappers                                                                                                                                                                                                                                                                                           | Stake MOBILE  Receive % of rewards from emissions contract                                                                                                                                                  |

To operate on the network, service providers and hotspot vendors must control a respective NFT. Getting this NFT requires hotspot vendors and service providers to:

1. Stake a minimum of 50M MOBILE in the case of hotspot vendors, and 500M MOBILE in the case of service providers
2. Obtain MOBILE DAO governance approval

The initial set of service providers and hotspot vendors are voted in by the Helium community together with DAO with no staking requirement, but these actors are required to stake MOBILE 6 months following the DAO launch.

Rewards from the emissions contract are distributed between service providers, mappers and hotspot operators as a function of the incentive points earned during a period of time. Service providers are allowed (and encouraged) to use the pool of MOBILE emissions they get towards growing their subscriber base by either sharing MOBILE directly with subscribers for usage via a witnessing app or discounting data plans.

Hotspot vendors can adjust what % of MOBILE generated by the fleet of hotspots deployed by their customers is kicked back up to the hotspot vendors and pursue different deployment strategies, from giving out free/highly subsidized hotspots to passing 100% or rewards to hotspot operators, but charging higher $$ prices for hardware.

Reward calculations that trigger distribution of tokens between service providers, mappers and and hotspot operators in the Helium Mobile DAO is performed by Oracles. At launch, network participants stake MOBILE to a genesis oracle for some predefined pro-rata share of emissions. Over time as new oracles onboard to the network, they are required to self-stake a minimum of 250M MOBILE in order to participate in rewards calculations, although the stake minimum is a parameter tunable by governance. Oracles continuously witness a stream of events coming from various DAO participants (such as mapper/hotspot attach events, data offload events, etc.) and perform reward distribution calculation. At the end of an epoch, Oracles come to consensus on proper distribution of MOBILE tokens based on events witnessed during the last epoch.

Below is a high level visualization of the rewards flow from the emissions curve to economic participants of the DAO:

![https://lh5.googleusercontent.com/FEecWNOLdSOvWk7fVFJ3ETXn_Qw7-CcuMGy67m4cHTzWvELd-OZzVWuijVZY7KVT8TNC0bBsUAI9k7Xmn9H3dZ6dNDdzfNjIfkej5ozfKgQF3PSLbIUMlIrMueAgaV99-WSJjgLvwbeZfZmDAA](https://lh5.googleusercontent.com/FEecWNOLdSOvWk7fVFJ3ETXn_Qw7-CcuMGy67m4cHTzWvELd-OZzVWuijVZY7KVT8TNC0bBsUAI9k7Xmn9H3dZ6dNDdzfNjIfkej5ozfKgQF3PSLbIUMlIrMueAgaV99-WSJjgLvwbeZfZmDAA)

Service providers will earn 20% of all MOBILE tokens minted by the DAO emissions curve, which will be distributed as a function of the number of incentive points accumulated by service provider participants during the given hour. Incentive points are issued using the following rules

- Service Provider receives 1 point for each of the mappers that used a SIM from this service provider and witnessed the network
- Service Provider receives 3 points for each $1 worth of DCs settled in state channels

Sim Authentication sessions from mappers are proxied through Oracles (but initially just the Helium Foundation) so that a service provider can’t lie about the number of mapping sessions they authenticated. Points for DCs settled in state channels are derived from cellular settlement service, which can be run by service providers themselves. Details of the cellular settlement service and its interactions with the service provider infrastructure (such as OCS/PCRF etc.) [are described here.](https://github.com/magma/grants/issues/14)

Individual hourly MOBILE earned by service provider during an epoch (initially 1 hour) is then calculated using the following formula:

![https://lh6.googleusercontent.com/EjhKTALD0rFg0XshYboRJhsVmfdXZkLNC1jlCKFEIBq_Uzw2BZoo8B12AHM_fyTVflSyUhVXHHlRLYROCoXzlzzgMVVALX61mXsp9Z_4Us-5LW7VrBb3lGB9eYWqJgxq7xITtnwm-p9PgD45Hw](https://lh6.googleusercontent.com/EjhKTALD0rFg0XshYboRJhsVmfdXZkLNC1jlCKFEIBq_Uzw2BZoo8B12AHM_fyTVflSyUhVXHHlRLYROCoXzlzzgMVVALX61mXsp9Z_4Us-5LW7VrBb3lGB9eYWqJgxq7xITtnwm-p9PgD45Hw)

Mappers will earn 10% of all MOBILE tokens minted by the DAO emissions curve. Tokens will be distributed to mappers as a function of the number of incentive points accumulated by all mappers on the network during an epoch. Initially, incentive points are issued to mappers using a simple rule:

- Mapper Receives 1 point each time it witnesses an eligible hotspot.

Later, the point system for mappers can be adjusted through DAO voting to introduce more complex mapping algorithms with elements of gamification.

Individual hourly MOBILE earned by a mapper is calculated using the following formula:

![https://lh4.googleusercontent.com/BKClIKjuGDz8okQ6cKAFoF30Z-7n7FtlsfAsJCnX-f1NniIHo8tzGH-UTsA3zH1DVzA2CgES0PbPSrJYvxllo_5wNCbjFpJwy_sIbqZEYXOW6pYWD-jxzECvqvAbGRoBPylqWlWxEuRsz_pS5g](https://lh4.googleusercontent.com/BKClIKjuGDz8okQ6cKAFoF30Z-7n7FtlsfAsJCnX-f1NniIHo8tzGH-UTsA3zH1DVzA2CgES0PbPSrJYvxllo_5wNCbjFpJwy_sIbqZEYXOW6pYWD-jxzECvqvAbGRoBPylqWlWxEuRsz_pS5g)

Hotspot owners will earn 60% of all MOBILE tokens for proof of coverage (PoC) related events (staying eligible and getting mapped) and for burning Helium data credits.

Total number of tokens distributed specifically for PoC events will vary from 20% to 60%, depending on the amount of data offloaded and number of tokens that went to data. PoC tokens PoC tokens are distributed to hotspot owners based on the number of incentive points accumulated by hotspot owners during a given epochhour. Incentive points are issued using the following rules:

1. Hotspot receives 1 point for each 4 consecutive hours of remaining eligible
2. Hotspot receives 3 points every time it was witnessed by a mapper
    
![https://lh4.googleusercontent.com/C-qT_FXuoqgiiQVfj29PUEtMI7G1SKIPHh1wWHJOUn2ykffTRR0TXyUgBkGFYNAnh01FogvbHStLq6KPfaz-1imYEFT8L9JMGH4ACiaSLth-XLwWYgUVdFtFpBqXJ0pO073at5zNOx1za7SLKQ](https://lh4.googleusercontent.com/C-qT_FXuoqgiiQVfj29PUEtMI7G1SKIPHh1wWHJOUn2ykffTRR0TXyUgBkGFYNAnh01FogvbHStLq6KPfaz-1imYEFT8L9JMGH4ACiaSLth-XLwWYgUVdFtFpBqXJ0pO073at5zNOx1za7SLKQ)
    

Tokens related to data credit burn are distributed in direct proportion to the number of DCs burned and are a function of amount of data offloaded by a particular hotspot and price of DC set by the DAO. See Data Transfer Mechanism and Pricing section for more details.

### *Emissions Curve*

There will be a max supply of 250B MOBILE.

The proposal is to have halvenings of MOBILE issuance every 2 years aligned with the HNT issuance halvenings. This requires a 1 year “stub” period from August 1, 2022 to August 1, 2023.

We also propose that 50B MOBILE are pre-mined at the launch of the network, and distributed to the subDAO operations fund administered by the Helium Foundation. This allocation is distributed over the course of the stub period to live Mobile Network hotspots on an epochal basis in roughly equal proportion (akin to universal basic income).

For clarity, the emission schedule is as follows:
| Year | MOBILE at the start of the year | MOBILE minted | Hotspot PoC  | Hotspot Data (excess to PoC) | Mappers | Service Providers | Oracles | veHNT Stakers |
|------|---------------------------------|---------------|--------------|------------------------------|---------|-------------------|---------|---------------|
| 1    | 50B                             | 116B*         | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 2    | 116B                            | 33B           | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 3    | 1495B                           | 33B           | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 4    | 182B                            | 16.5B         | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 5    | 198.5B                          | 16.5B         | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 6    | 215B                            | 8.25B         | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
| 7    | 223.25B                         | 8.25B         | 20%          | 40%                          | 10%     | 20%               | 4%      | 6%            |
	*50B pre-mine, 66B emitted in year 1

At the end of a given epoch, MOBILE oracles relay start balance, current balance, and total amount of MOBILE Data Credits created and relay to the L1 HNT emissions contract. The emissions contract subsequently distributes the determined amount of HNT as per the Protocol Score to the Mobile subDAO multi-signature wallet, the addresses of which comprise the set of oracles of the Helium Mobile Network.

The subDAO operations fund is intended to allow the DNP to perform bespoke operations to create and sustain network growth. The primary use case of the operations fund is to fund all state transition transaction fees to the L1, but can be deployed in any manner of ways as per subDAO governance. Such incentives could include

1. Surge-pricing style dynamic multipliers based on data transfer activity for individual regions
2. Bespoke incentives for oracles and manufacturers on the basis of changing network demands

Once emissions are distributed to hotspots and oracles, MOBILE owners can either redeem their holdings for underlying HNT against the treasury reserve automatically, hold for redemptions at a later time, or lock up their MOBILE for veMOBILE in a process similar to the veHNT mechanism described in HIP 51.

A user’s veMOBILE lockup power is determined by 1) the amount of MOBILE they lock up with, and 2) the amount of time they commit to locking up their MOBILE. The structure applies a linear multiplier of time to the amount of HNT locked up in the voting contract. For the maximum period of four years, users receive 100x the veMOBILE. For the minimum period of six months, users receive 1x the veMOBILE. Note that veMOBILE is **non-transferable**, and represented as a non-fungible coupon in the user’s MOBILE address.

Users can choose to delegate their veMOBILE for three core purposes:

1. Stakeholder Staking: Users are able to delegate veMOBILE in the name of:
    1. Specific hotspots in order to increase their hourly incentive points multiplier
    2. Specific service providers in order to increase their hourly incentive points multiplier

Users are able to stake either “for” or “against” hotspots or service providers. At the end of each epoch, stakeholders have a net veMOBILE score in which “for” staked amounts are added, and “against” staked amounts are subtracted from the running total.

Stakeholder veMOBILE scores are considered within a range of -10M and 10M. For the maximum amount of 10M veMOBILE staked, stakeholders receive 10x the incentive points multiplier. For the default amount of zero veMOBILE staked, stakeholders receive 1x the incentive points For the minimum amount of -10M, stakeholders receive zero incentive points. In either direction across zero from the y-axis, the incentive point multiplier varies linearly with the veMOBILE staked.

Users that delegate veMOBILE to stakeholders receive 50% of any incremental earnings through increased incentive multiplier points.

![https://lh5.googleusercontent.com/GnNkPkShSi3HFf2fwDLaXNXh8xk5LSinMETkSPFaCPKQflhtj-LPwADe3CuRW_jYUxpsefw7kQOobn4xMpAmrRnTTqhlawUyeh_9rZLlD_L0T5LA7zOqxu65CZuC6JHVTFWkOE3120E4nKvisw](https://lh5.googleusercontent.com/GnNkPkShSi3HFf2fwDLaXNXh8xk5LSinMETkSPFaCPKQflhtj-LPwADe3CuRW_jYUxpsefw7kQOobn4xMpAmrRnTTqhlawUyeh_9rZLlD_L0T5LA7zOqxu65CZuC6JHVTFWkOE3120E4nKvisw)


Stakeholders that are found to be gaming or cheating rewards are blacklisted from the network, and delegated veMOBILE is distributed as follows:

1. 50% of delegated veMOBILE is burned
2. 50% of delegated veMOBILE is distributed to users who staked against the hotspot

For example, consider the following closed system with Stakeholder A, Stakeholder B, User X, and User Y.

X delegates 1M veMOBILE “for” stakeholder A and 4M veMOBILE “against” stakeholder B.

Y delegates 5M veMOBILE “for” stakeholder A and 2M veMOBILE “for” stakeholder B.

Tallying both scores, we have a net score of 6M veMOBILE “for” stakeholder A and -2M veMOBILE “against” stakeholder B.

In this epoch, stakeholder A is expected to receive 6x the incentive multiplier under normal circumstances, and stakeholder B is expected to receive 0.8x the incentive multiplier it would be eligible for under normal circumstances.

If stakeholder A were to receive 120K MOBILE through rewards in an epoch, we could expect that it would have received 20K MOBILE under normal circumstances. User X would receive 1/6 of the incremental amount (8.33K MOBILE) and the hotspot owner would receive 5/6 of the incremental amount (41.67K MOBILE).

If hotspot B were convicted by governance of gaming rewards, 3M of the total veMOBILE would be sent to a burn address, whereas the underlying MOBILE within the 3M veMOBILE would be distributed to user X. If there were other users who staked against stakeholder B, this would be distributed pro-rata.

Note that users also have the option of burning MOBILE in the name of stakeholders, in order to receive an equivalent pro-rata share of staking rewards in perpetuity.

1. Oracle Delegation: veMOBILE holders can delegate their holdings to oracles in order to earn future emissions.
2. Governance: veMOBILE can be used to participate in subDAO proposals that impact core protocol parameters, mechanisms, and operating procedures. veMOBILE that is staked to a hotspot or to an oracle can still vote on governance proposals.

### *Treasury Reserve DNT Market Making Curve*

The Mobile subDAO sets the programmatic treasury formula in order to provide quotes to holders of DNT who wish to redeem their holdings for underlying HNT. Note that at launch of the subnetwork prior to any HNT emissions from the minting contract as per the protocol score, the Helium Foundation will make a donation of HNT into the subDAO treasury reserve in order to collateralize the airdrop specified in the emissions section.

We propose a constant function market making formula for the Mobile subDAO programmatic treasury defined as per the following specification.

![https://lh3.googleusercontent.com/E2RFzWF4IwlC3Vz33lWjDpJe8y9ZnHD2mWaUhRn541jicqxEbPqC1-Mt3-IZsLk0WfcelDGhrGt1hbuXUjp_CMgqMuHSZDky4RKPwiWA8Tg0dPr2IhG1lUAp-gVcaNptXbcuvLrxcC4DGQcTgg](https://lh3.googleusercontent.com/E2RFzWF4IwlC3Vz33lWjDpJe8y9ZnHD2mWaUhRn541jicqxEbPqC1-Mt3-IZsLk0WfcelDGhrGt1hbuXUjp_CMgqMuHSZDky4RKPwiWA8Tg0dPr2IhG1lUAp-gVcaNptXbcuvLrxcC4DGQcTgg)

At epoch T, we denote the value of H, S, and P as HT, ST,  and PT. The programmatic treasury formula at epoch T is a function in two variables, HT, ST defined as follows:

![https://lh3.googleusercontent.com/LDSGfLuE1FiCT0Fau-Ub05gqxFzr4xgX0wKsIhBw1Ghxvb3TOZZUpRRVwWGDgw92eKw62WNzjq8rrzLqiDdhhBlTup9RqGMvLE760qnMj76HI-Sgts-9F4pzrGspZBW6-DNm2_g69HsQnvhB8g](https://lh3.googleusercontent.com/LDSGfLuE1FiCT0Fau-Ub05gqxFzr4xgX0wKsIhBw1Ghxvb3TOZZUpRRVwWGDgw92eKw62WNzjq8rrzLqiDdhhBlTup9RqGMvLE760qnMj76HI-Sgts-9F4pzrGspZBW6-DNm2_g69HsQnvhB8g)

For example, consider at epoch #125, the total outstanding supply of tokens for the Mobile subDAO is 3.275B MOBILE and the total amount in the treasury reserve is 2M HNT. The programmatic treasury formula is given as follows

![https://lh4.googleusercontent.com/CBx8hTnPuS-zaW-AAxZPP1hKZzt82oZlNzuEeYXRF0Wg2kJ1UKtCO0g8xoqvWU1bZvTITYUeM8FSpvDPPtU9xwSkYmleoFoilYdoEwXTF3fFBpLVkNnRa4AT6AK-NcfBWb1Y3VlR_L2Kir2Qrg](https://lh4.googleusercontent.com/CBx8hTnPuS-zaW-AAxZPP1hKZzt82oZlNzuEeYXRF0Wg2kJ1UKtCO0g8xoqvWU1bZvTITYUeM8FSpvDPPtU9xwSkYmleoFoilYdoEwXTF3fFBpLVkNnRa4AT6AK-NcfBWb1Y3VlR_L2Kir2Qrg)

This implies that at epoch #125, any number of MOBILE tokens can be redeemed at a unit price of 0.00061068702 HNT.

Now consider at epoch #225, the total outstanding supply of tokens for the Mobile subDAO is 3.5B MOBILE and the total amount in the treasury reserve is 2.025M HNT. The programmatic treasury formula is given as follows

![https://lh3.googleusercontent.com/iFAF8EuGVpEUOc-Vb-rl9lxkJ7jjqsiyAo4q3VtqmUPhCGj4WEkdTDwlnqomQv5lmWP1QPUge_r4-JGFjE5JvPfCj2mXS3IC4L1yH5n6NNYTsbzbhkfyM7Ng80b0TEkCP9sfi9ASfZEgjjiuAg](https://lh3.googleusercontent.com/iFAF8EuGVpEUOc-Vb-rl9lxkJ7jjqsiyAo4q3VtqmUPhCGj4WEkdTDwlnqomQv5lmWP1QPUge_r4-JGFjE5JvPfCj2mXS3IC4L1yH5n6NNYTsbzbhkfyM7Ng80b0TEkCP9sfi9ASfZEgjjiuAg)

This implies that at 100 epochs after #125 at #225, any number of MOBILE tokens can be redeemed at a unit price of 0.00057857142 HNT.

In epoch #225, the total outstanding supply of MOBILE has increased, and given inflows to the treasury reserve in HNT the resulting unit price has decreased. If the rate at which HNT flows into the treasury reserve outpaces the emission of MOBILE tokens, the unit price of MOBILE increases.

Between epoch #125 and #225, a similar repricing takes place in each individual epoch given the amount of HNT in the treasury reserve and the outstanding supply of MOBILE tokens.

Clarifications:

- The supply of MOBILE tokens for the purposes of calculating the MOBILE/HNT price includes all locked MOBILE tokens.
- The subDAO updates the limit order price every epoch **before** any new MOBILE are distributed out to stakeholders for their activity during that epoch.

## *Data Transfer and Pricing Specification*

Data Credits are a universal unit of payment across all Helium DAOs and will be utilized by the Service Providers in the Helium Mobile DAO ecosystem as payment for packet transfers.

Service Providers operating in the Helium Mobile DAO will be required to either directly run an instance of the cellular settlement service or contract a third party entity, approved by the DAO to run an instance of the service for them. Detailed specification of the cellular settlement service and its interaction with service provider infrastructure and the Helium blockchain are [described here.](https://github.com/magma/grants/issues/14)

Helium Mobile Network Dao will operate a chain variable that will dictate the conversion ratio between Data Credits and MOBILE tokens, denominated in oracle USD price. Changes to the conversion ratios will be conducted following Sub DAO governance specification.

The initial price per GB of Mobile Network data is suggested to be set at $0.5 per Gigabyte, which means that 1 Helium data credit, when used for data on Mobile Helium DAO will convert to $.00000003 per LTE packet of 66 bytes.

## *Oracle Operations*

Mobile Network oracles perform the following functions:

1. Proxy mobile network signaling traffic that allows inbound data offload operators to authenticate subscribers in the HSS database to attach to the Helium Mobile Network hotspot nodes
2. Confirm transactions and add blocks to the Mobile Network L2 chain. They serve state data around Proof-of-Coverage challenges
3. Reward calculations that trigger distribution of tokens between service providers, mappers and and hotspot operators

Validation is performed by a set of rotating nodes known as the consensus group, which verifies transactions and ordering prior to forming a block and proposing it to the L2 chain. Consensus groups are elected once per epoch, and the number of members is given by the num_consensus_members chain variable (currently set at 43).

At launch, a single oracle operated by the Helium Foundation validates data transfer and proof-of-coverage across the Mobile Network. Over time, new oracles join the network while the subDAO defines consensus rules for the underlying protocol.

At the end of each epoch, mining rewards are distributed by the consensus group to the wallet addresses that have earned them.

Each one of the above activities is recorded in a block using the reward transaction. At the completion of each epoch, all the individual reward transactions are grouped in a rewards transaction at which point all HNT mined in that epoch are distributed.

Proof-of-Coverage Specification

The Mobile Network subDAO is required to constantly interrogate Mobile Network hotspots using the Proof-of-Coverage mechanism to ensure that hotspots are representing their locations accurately and providing functional radio coverage.

PoC implementation proposed in the Helium Mobile Network sub-dao is based on wireless coverage being verified through LoRa hotspots issuing “challenges” to “challengees” and “witnessing” over the air interface. However, for the 5G wireless protocol, radios cannot both challenge and witness the packets over the air, as the 5G/LTE and other cellular protocols are designed for the radio to interact with a UE (such as cell phone) vs another radio..

For Mobile Network subDAO we propose to separate the challenge and witness function(s) between the operator of a hotspot and a mapper device (such as a phone or a dedicated mapper) with a sim card that has been authorized to perform the witness function. During the launch stages of the DAO we propose a simplified approach to mapping coverage as follows:

The world is divided into hexes using the H3 geospatial [indexing system](https://h3geo.org/docs/). All hotspots exist as NFTs associated with their unique pub keys and will receive *Eligibility Rewards* and *Mapping Rewards*. All hotspots will receive eligibility rewards, hotspots mustas long as they meet the following minimum criteria:

1. Stay registered in a Spectrum Access System (SAS), as evidenced by the Domain Proxy software that’s operated by a hotspot vendor
2. Meet minimum backhaul QoS of 100Mps on the downlink and 10Mps on the uplink, as evidenced by the randomized backhaul challenges run by the hotspot firmware
3. Be located in a hex that’s been whitelisted for rewards

All hotspots that have remained eligible (i.e. received eligibility rewards) during the last 4 consecutive hours, will be eligible to receive mapper rewards.

Any device (first and foremost a phone) with the proper sim card can be a witnessing device on the network. To witness coverage a “mapper phone” must connect to a CBRS cell or Wi-Fi AP using sim based authentication with a sim card that has been granted mapping privileges by the service provider. Service providers will act as “validators of witness transactions” performed by mappers by authenticating mappers through their HSS via either s6a or sWx protocols.

During initial launch stages, when network density is low, to combat gaming, Helium Mobile DAO implements a simple limiting algorithm:

1. Same mapper can witness the same hotspot no more than once every 4 hours
2. Same cell can be witnessed by a maximum of 6 unique mappers every hour

During consequent launch stages, as the density of mappers grows, Helium Mobile DAO will look to implement a more robust algorithm, similar to Helium LoRa PoC, whereby only randomly challenged hotspots can be witnessed. Details of this mechanism along with analysis of various attack vectors have been previously published and discussed with the community  in HIP37

### *Governance Specification*

Mobile Network Network is under the control of the subDAO. All subDAO proposals must come attached with code to be approved.

We propose that veMOBILE governance is constructed in a manner identical to veHNT governance as specified in HIP51.

Users can choose to delegate their lockup power in veMOBILE to all Mobile Network DAO governance proposals. Proposals are assessed using majority and quorum thresholds defined in veMOBILE terms, initially proposed to be 67% and 100M MOBILE respectively.

Note that only proposals with code attached can be voted on.