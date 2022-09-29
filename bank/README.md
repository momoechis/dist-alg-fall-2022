## Distributed Algorithms Fall 2022 Project #1: “The Bank”

**Model a bank with multiple distributed ATMs/branches**

From the syllabus:

> follow a specification to build a working example of a distributed system and identify the tradeoffs of their design (SO 2, 6)

## Project overview

For this project you will develop a distributed system that models a bank.

The bank has branches where customers can create accounts, close accounts, check balances, deposit cash, withdraw cash, and transfer money to other accounts. The bank also has ATMs where customers can check balances, deposit cash, and withdraw cash from their accounts. 

To simplify the problem, we will assume that there is no such thing as checks, debit cards, or banking fees. The only way to move money to and from an account is to visit a branch or ATM and make a deposit, withdrawal, or transfer. 

Each branch and ATM maintains a “cash on hand” amount so that the cash dispensed is never more than the cash-on-hand. If a transaction would leave the branch or ATM with a negative cash-on-hand, the transaction is cancelled and the customer is told “Sorry, that is more cash than we have on-hand. Try another branch or try later.”

When a customer creates an account at a branch, the customer should be able to access that account from any branch or ATM. Transactions to an account made at one branch or ATM should be visible from any other branch or ATM. 

Recently, there have been periodic network issues between the various branches and ATMs. These network issues can result in brief net-splits, message loss, and messages arriving out-of-order. To reduce customer frustration while minimizing financial losses, the bank has defined some special rules to be applied to transaction during net-splits. If a customer is at a branch or ATM that is net-split, the last known balance will be used but the customer will be allowed to overdraw by the average of their last three known deposits. If after the net-split heals, it is determined that an account is overdrawn the bank will send a nasty letter and will disallow all withdrawals from that account until the balance is positive.

The bank managers are worried the network issues may be the work of hackers. They would like to run reports that gather transactions from all branches and ATMs to ensure that money is not disappearing. They would also like to implement safeguards to prevent fraud from going undetected.

The simulation begins with the bank having three (3) branches, seven (7) ATMs, no (0) customers. Each of the branches and ATMs begin the simulation with $1,000 cash-on-hand.


## How to get started

* Open a terminal and `cd` into the directory where this "README.md" exists.
* In the terminal run `mix deps.get` to pull any needed dependencies.
* In the terminal run the unit tests `mix test`
* The instructors have written tests and implementations for many of the local banking functions. These tests should pass.
* The instructors have written one test that requires replication and it will fail until you implement some basic replication
* Over the next weeks, the instructors will add additional tests to exercise your design.


You can interact with your system by writing additional unit tests. A good place to start would be "test/bank_test.exs". 

As you ar building out your design, you may also use the Interactive Elixir shell ("iex"). You can open iex and have it load your project by running `iex -S mix` from the terminal.

For example:

```
iex -S mix
Erlang/OTP 25 [erts-13.0.3] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [jit:ns]

Interactive Elixir (1.13.4) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> Bank.Branch.check_cash_on_hand(1)
{:ok, 1000}
iex(2)> Bank.Branch.open_account(1, 1002345)      
{:ok, :account_opened}
iex(3)> Bank.Branch.check_balance(1, 1002345)
{:ok, 0}
iex(4)> Bank.Branch.deposit_cash(1, 1002345, 100)
{:ok, :cash_deposited}
iex(5)> Bank.Branch.check_balance(1, 1002345)    
{:ok, 100}
iex(6)> Bank.Branch.check_cash_on_hand(1)        
{:ok, 1100}
```


## Additional tools

If you are the lack of static type checks makes you uneasy, you can run 
and optimistic static type check on the code using Dialyzer using the following
command from the terminal.

```
mix dialyzer
```

The first time you run this utility it will take a while (minutes) as it builds 
persistent lookup tables (PLTs) of all the types in libraries we depend on. 
Subsequent runs should be fast. It will display warnings of any function argument 
types or return types that it is sure are incorrect.