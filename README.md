# neutron-bomb-processing
Lockless, ordered, threaded, parallel chain reaction processing.
![Chain reaction](https://upload.wikimedia.org/wikipedia/commons/f/f0/Nuclear_fission_chain_reaction.svg "uranium chain reaction")

## What is nuclear processing ?

A processing methodology where one process triggers many different processes to start. It can be set up like a chain reaction,
where multiple processes can trigger multiple subsequent processes.

In the context of this project, fission processing has the following properties :
* lockless
* parallel
* ordered
* light weight
* low latency

## How can I think of it ?

Think of a chain reaction explosion where Uranium 235 triggers many other uranium atoms to split. One path of fission is dependent on only only prior computations in the fission chain. Computation is independent of other fission chains.

Each Atom waits for the prior atom in the chain reaction (the chain atom) to complete processing and signal it's completion. The Atom then
executes the process which most likely begins with a memory copy and then computation if necessary. On completion, it signals the next atoms
up the chain to begin processing.

The Atom uses a ThreadedMethod for performing the wait, process and wake loop. A Futex is used for waiting and waking.

## A history of loop fission computation

Loop fission goes back decades to the development of SPICE and possibly before that to parallel computation, where computational loops
loops are broken up into smaller loops which can be executed concurrently. If you are interested, see this [Wikipedia article on
loop fission](https://en.wikipedia.org/wiki/Loop_fission_and_fusion), which gives the following very basic example :
```C
int i, a[100], b[100];
for (i = 0; i < 100; i++) {
 a[i] = 1;
 b[i] = 2;
}
// is equivalent to
int i, a[100], b[100];
for (i = 0; i < 100; i++)
 a[i] = 1;                     
for (i = 0; i < 100; i++)
 b[i] = 2;
 ```
 These two loops can clearly be executed in parallel without effecting each other. However it has no reference to ordering.
