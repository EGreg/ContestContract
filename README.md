# ContestContract
Helps people contribute money to a project, and choose judges to distribute it for various solutions.

# Deploy
when deploy it is no need to pass parameters in to constructor

# Overview
once installed will be use methods:

## Settings
name|type|value|description
--|--|--|--
revokeFee|uint256|10e4|10% mul at 1e6. penalty for revoke tokens

## Methods
#### createContest
Params:
name  | type | description
--|--|--
stagesCount|uint256|count of stages for Contest
stagesMinAmount|uint256|array of minimum amount that need to reach at each stage. length array should be the same as stagesCount
contestPeriodInSeconds|uint256|duration in seconds  for contest period(exclude before reach minimum amount)
votePeriodInSeconds|uint256|duration in seconds  for voting period
revokePeriodInSeconds|uint256|duration in seconds  for revoking period
percentForWinners|uint256[]|array of values in percentages of overall amount that will gain winners
judges|address[]|array of judges' addresses. if empty than everyone can vote

#### isContestOnline
Checking online for Contest with number = ContestID
Params:
name  | type | description
--|--|--
stageID|uint256|Stage number
contestID|uint256|Contest number

#### pledge
can be used only with Constest.sol to send external token into the contract, and issue internal token balance
Params:
name  | type | description
--|--|--
amount|uint256|amount to pledge
stageID|uint256|Stage number
contestID|uint256|Contest number

#### pledgeETH
can be used only with ConstestEWETHOnly.sol to send ETH into the contract, and issue internal token balance
Note that ETH need to send with transaction/ not directly to contract via native `recieve()`
Params:
name  | type | description
--|--|--
amount|uint256|amount to pledge
stageID|uint256|Stage number
contestID|uint256|Contest number
    
#### delegate
Params:
name  | type | description
--|--|--
judge|address|address of judge which user want to delegate own vote
stageID|uint256|Stage number
contestID|uint256|Contest number

#### vote
Params:
name  | type | description
--|--|--
contestantAddress|address|address of contestant which user want to vote
stageID|uint256|Stage number
contestID|uint256|Contest number

#### claim
Params:
name  | type | description
--|--|--
stageID|uint256|Stage number
contestID|uint256|Contest number

#### enter
Params:
name  | type | description
--|--|--
stageID|uint256|Stage number
contestID|uint256|Contest number

#### leave
Params:
name  | type | description
--|--|--
stageID|uint256|Stage number
contestID|uint256|Contest number

#### revoke
Params:
name  | type | description
--|--|--
stageID|uint256|Stage number
contestID|uint256|Contest number

## Lifecycle of Contest
* creation Contest.  emitting event `ContestStart`. Starting Contest Period with `StageID` = 0
    * anyone can enter to contest calling method `enter()` and become contestant
    * anyone can leave to contest calling method `leave()` and remove itself from contestant list
    * anyone can pledge smth to contest calling method `pledge()` if didn't not become contestant earlier
* as soon as pledging by people become more than "stagesMinAmount" Contest Period will be extended for `contestPeriodInSeconds` seconds. and stop there
    * emitting `StageStartAnnounced`
* starting Voting period for `votePeriodInSeconds` seconds.
    * judge can vote for contestants
    * if judgeList was empty then anyone who pledged before can vote
* starting Revoking period for `revokePeriodInSeconds` seconds.
    * anyone who pledged before can revoke own tokens with revokeFee penalty
* Stage completed.(after any request with `stageID` and `contestID`).
    * emitting `ContestWinnerAnnounced`
    * if winners exists, then
        * reward winners
        * reward losers (if left smth from winners) they will get equally
    * if winners does not exists, then all who pledged will get equally
    * next stage will begin if current stage < `stagesCount`

