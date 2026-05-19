paper title: Inferring multiple helper Dafny assertions with LLMs by Silva et al

ArXiv Link: https://arxiv.org/abs/2511.00125

PDF link: https://arxiv.org/pdf/2511.00125

HTML Link: https://arxiv.org/html/2511.00125v1i



## Background

 Dafny is a 'verification-awware' programming language which can compile to Python, Go, JavaScript, etc. It has native support for writing formal specs + a verification engine that can compare the specifications with the implementation. In addition, Dafny supports a toolbox for writing mathematical proofs about software including writing specs via pre-/post-conditions, loop invariants, termination conditions, etc.

### What are Dafny Assertions?

In dafny, we only write the spec + implementation, and the verifier (using Z3 SMT solver internally) attempts to come up with a proof. The 'assertions' serve as intermediate proof hints for Z3 for complex proofs that it can not discharge on its own.

Internally, Dafny's compiler translates everything into verification conditions i.e., logical formulas that, if proven true, guarantee the implementation satisfies the spec. These formalae are given to Z3 which tries to automatically prove them.  


## Abstract

Writing assertions [ie as discussed above: intermediate proof hints] is tedious and error-prone. This paper attempts to automatically infer missing helper assertions using LLM assistance.

## Solution Proposed

The authors implement a system: DAISY (Dafny Assertion Inference SYstem)


DAISY uses a two-stage pipeline:

  1. Fault Localization: Where are assertions missing?
    - LLM-based (they call it LlmExfl): GPT-4.1 predicts likely insertion points
    - Heuristic-based (Laurelfl+): Analyzes Dafny verifier error messages to pinpoint locations
    - Hybrid: Combines both, achieving near-oracle performance. When is which used?
      both approaches are used in parallel, an the LLM + the heuristic-based approach both suggest locations on where to add new assertion. 
  2. Assertion Inference:  What assertions to insert?
    - For each location, first from the Dafny knowledge base, we pull up assertions similar to what might be used for us via  multi-embedding retrieval (combining code similarity + error-message similarity), this helps find relevant few-shot examples from a knowledge base.
    - The examples are given to an LLM that then generates 10 candidate assertions per location.
 

### Contribution of this paper

1. They are generating multiple assertions, previous works only worked on one-assertion generation.
2. LLM + heuristc approach outperforms pure-LLM.
3. Detailed analysis on assertion 'localization'
4. Extended DafnyBench benchmark.
5. Taxonomy of assertions, and analysis of increasing inference difficulty.
6. Open-source on github.


















