---
layout: page
title: Smart Contract Optimization
description: Optimal Synthesis of Michelson Bytecode
img: assets/img/projects/logo-nl.png
importance: 1
category: work
related_publications:
---

# Background

A Smart Contract is a program that is executed by every node participating in a blockchain. To account for the computational cost of this execution, a smart contract consume gas, an abtract resource purchased by the users of the blockchain. They consume a lot of gas, an abstract resource purchased through cryptocurrency. There is therefore economic incentives to reduce gas consumption. Michelson is a stack-based, strictly typed language in which Smart Contracts of the Tezos blockchain are written to ensure the safety of the Tezos blockchain. This report implements a blackbox optimizer for Michelson programs based on S-metaheuristics.

A blockchain is a type of database. Blockchains store data in blocks that are then chained together.

A Smart Contract is a computer agreement or program designed to spread, verify or execute contracts in an information-based way. Smart contracts in blockchain have the following characteristics: Rules are transparent, and data in the contract are visible to the outside; All transactions are publicly visible, and there will be no false or hidden transactions, thus cannot be modified.

<div class="row justify-content-sm-center">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/blockchain.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/smartcontract.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Tezos is a decentralized, open-source *Proof of Stake* blockchain network and it supports Smart Contracts and *tez* crypto-currency (XTZ). Its characteristic is to natively support protocol updates without hard forks. The Tezos blockchain environment is based on OCaml. All the programs in this report are also implemented in OCaml with the help of some tools in the Tezos codebase.

On the Tezos network, Michelson programs consume **gas**, which is an abstract resource designed to bound Smart Contract computation time and thus (amongst other things) incentivize efficient use of on-chain computation. Specifically, **gas** represents computational cost related to a transaction, an amount of **gas** is assigned to different instructions. The main goal of **gas** is to be a security measure against DoS (i.e. Denial-of-Service attack), because an unbounded execution would block nodes and wouldn't allow the chain to move forward, so it offers liveness guarantee for blockchain network.

# Objective

The objective of this project is to optimize Michelson Bytecode with respect to gas consumed. Specifically, we study super-optimization(finding global program optimizations which might be missed by a smaller and simpler search for local optimizations). We aim for an heuristics-based method using S-metaheuristics to find the optimal bytecode in a fully blackbox way.

I was able to complete this project in the team of Nomadic Labs, a research and development company, which contributes in particular to the implementation of the software core of the Tezos blockchain, and to the development of the language of the associated smart-contracts, Michelson. 

# Contributions

The approach presented by this report is basically split into three phrases: (i) sampling, (ii) search, (iii) proof. 

## Sampling

To apply S-meraheuristics method, we need a cost function that aims to guide the search. To establish this cost function, we choose to take inputs-outputs relationships as arguments of it. Generation of inputs-outputs pairs is realized by a Monte Carlo-based sampler and a Michelson interpreter. The use of a sampler is needed, because manually defining the inputs for each contract is at best impractical. There is an existing value sampler in Tezos codebase and we adapt this sampler to generate the corresponding input value for each contract randomly.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/micheline.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/whyIO.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/sampling.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


## Search

The search starts from the empty program of Michelson language. Each program synthesized is scored by its distance of outputs with the expected one. Lower distance means higher score and a distance of zero is highly expected to obtain.

In my internship, ILS algorithm is implemented for this search process. The best programs found by Local Search are perturbed to more possible programs and applied Iterated Local Search. All programs with zero distance and less gas consumed are considered as candidates waiting for the proof of semantic equivalence with the original program.

<div class="row">
    <div class="col-sm mt-12 mt-md-0">
        {% include figure.html path="assets/img/projects/search.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

## Proof

Candidates found by the last step cannot be taken as correct solutions (i.e. optimized programs), because it is clear that having not enough inputs-outputs pairs can sometimes generate a program that is not equivalent, hence we implement Translation Validation (as defined below) to prove semantic equivalence between the source program and the candidate optimized program.

Translation Validation is a technique for ensuring that the target code produced by a translator is a correct alternative representation of the same computation. Rather than verifying the translator itself, Translation Validation validates the correctness of each translation, generating a formal proof that it is indeed a correct.

Translation Validation is proved by Z3 SMT Solver in this report. Z3 is an efficient SMT Solver freely available from Microsoft Research. It is usually used in various software verification and analysis applications. Working as an SMT Solver, it is able to decide the satisfiability of formulas in a variety of theeories. We choose this tool to achieve our requirements. With its OCaml API, we can apply it in Tezos ecosystem.

<div class="row">
    <div class="col-sm mt-12 mt-md-0">
        {% include figure.html path="assets/img/projects/translationvalidation.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/functionsinz3.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/examples.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

# Conclusions

We have presented a method for gas super-optimization of Smart Contracts based on S-metaheuristic in Tezos blockchain. Basically, our focus is on the stack operations for basic blocks of Michelson programs. This heuristics-based method offers one alternative way of superoptimizing Smart Contracts in terms of gas consumed. We build a basic tool in Tezos codebase. Currently, it can sample specific types of values and generate inputs-outputs relationships for a well-typed Michelson program; it searches programs by ILS algorithm and collect all possible candidates (i.e. consume less **gas** and qualify inputs-outputs relationships); it checks the semantic equivalence by Translation Validation between each candidate and original program, and returns optimized programs at the end. 

Talking about its efficiency and quality, unfortunately there is no benchmark for now. Judging from only a few examples, its results are accurate and programs synthesized is truly optimized in terms of gas consumed. For simple Smart Contracts(e.g. with instructions less than 10 lines), this work has a good expectation to synthesize optimized programs after acceptable time of search. While there are some limitations of this tool. Firstly, for big Smart Contracts, we have to adjust parameters (including the interval bounds design and core parameters like number of rounds, number of `lookahead`` execution times, etc.) and it would take much more time to search the space. The implementation of ILS algorithm in this work is still possible to be optimized. Secondly, this work considers only basic blocks of Michelson programs, which means there should be no control flow like for-loops or conditional control flow if we want to apply this tool. Thirdly, it lacks benchmarks to evaluate and improve this tool. 

Future work should focus on efficiency and benchmarks. It should be able to be find the optimized program faster, with a better implemented S-metaheuristics method (e.g. Metropolis Hasting and Simulated Annealing, etc.) or with a better design of parameters. Also, for scoring Michelson programs, arithmetic distance may not be good enough for more complex situations. It would perform better by combining multiple methods, e.g. a variety of types of edit distance, log-arithmetic distance, etc. This work has proved the feasibility of this approach to a certain extent. Optimizing the S-metaheuristics algorithms implemented and improving the search mechanism on the basis of the current work should finally get a more satisfactory and efficient optimizer for complex Michelson programs.
