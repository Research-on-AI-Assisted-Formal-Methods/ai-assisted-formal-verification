paper title: Inferring multiple helper Dafny assertions with LLMs by Silva et al

ArXiv Link: https://arxiv.org/abs/2511.00125

PDF link: https://arxiv.org/pdf/2511.00125

HTML Link: https://arxiv.org/html/2511.00125v1i



## Background

 Dafny is a 'verification-aware' programming language which can compile to Python, Go, JavaScript, etc. It has native support for writing formal specs + a verification engine that can compare the specifications with the implementation. In addition, Dafny supports a toolbox for writing mathematical proofs about software including writing specs via pre-/post-conditions, loop invariants, termination conditions, etc.

*Note for our research* Our research is slightly different and more complex because we are targeting more expressive languages than Dafny such as Lean4, Rocq/Coq, where we attempt to do a whole-proof generation. Dafny, F* on the other hand attempt to generate a proof themselves just from the spec using tools such as Z3, but as a result their abilities are a bit lower.


### What are Dafny Assertions?

In Dafny, we only write the spec + implementation, and the verifier (using Z3 SMT solver internally) attempts to come up with a proof. The 'assertions' serve as intermediate proof hints for Z3 for complex proofs that it can not discharge on its own. 

(Also, these hints are written within the implementation, such as a main method calling the function.)

Internally, Dafny's compiler translates everything into verification conditions i.e., logical formulas that, if proven true, guarantee the implementation satisfies the spec. These formalae are given to Z3 which tries to automatically prove them.  

## Abstract

Writing assertions [ie as discussed above: intermediate proof hints] is tedious and error-prone. This paper attempts to automatically infer missing helper assertions using LLM assistance.

## Overview of Solution Proposed

The authors implement a system: DAISY (Dafny Assertion Inference SYstem), which uses a two-stage pipeline:

  1. Fault Localization: Where are assertions missing?
    - LLM-based (they call it LlmExfl): GPT-4.1 predicts likely insertion points
    - Heuristic-based (Laurelfl+): Analyzes Dafny verifier error messages to pinpoint locations
    - Hybrid: Combines both, achieving near-oracle performance. When is which used?
      both approaches are used in parallel, an the LLM + the heuristic-based approach both suggest locations on where to add new assertion. 
  2. Assertion Inference:  What assertions to insert?
    - For each location, first from the Dafny knowledge base, we pull up assertions similar to what might be used for us via  multi-embedding retrieval (combining code similarity + error-message similarity), this helps find relevant few-shot examples from a knowledge base.
    - The examples are given to an LLM that then generates 10 candidate assertions per location.
 

### Contribution of this paper

1. They are generating multiple assertions, compared to previous works only worked on one-assertion generation.
2. LLM + heuristc approach outperforms pure-LLM.
3. Detailed analysis on assertion 'localization' and which techniques work best.
4. They extended a DafnyBench standard benchmark.
5. They created a taxonomy of assertions; and an analysis on which categories of assertions are harder to infer via LLM
6. Open-source on github.


## Conclusions from the paper 

Direct quotes:
1. LLMs can effectively infer assertion positions, performing on par with or better than error message-based heuristics, and the two approaches are complementary, with their combination achieving performance close to that of a ground-truth oracle; 
2. the proposed methods can handle multiple missing assertions, although performance degrades substantially when all assertions are removed, and inference difficulty varies widely across categories, with test-like assertions being relatively easy and multi-step assertions the most challenging; and 
3.  retrieval-augmented generation improves inference by incorporating relevant examples into prompts. 


## Research questions asked and their proposed answer


1. RQ1. How effective is Dafny assertion inference using LLMs? 

Overall, DAISY verifies 63.4% of programs with one missing assertion and 32.3% for w/o-2 (two missing) and 26.7% for w/o-all ie, three+ missing. 


2. RQ2. How well do the different fault localization methods perform? 

LLM-based and heuristic-based localization succeed on different programs, hence their approach is complementary to one another.

3. RQ3. How well do different methods for extracting examples improve assertion inference? 

Few shot examples retrieved via multi-embedding similarity increase accuracy by 15% points over zero-shot prompting (51.5% -> ~68% on single-assertion cases), with the best strategy being 'MulEmb'


4. RQ4. Which types of assertions are harder to infer?

TEST assertions (unit-test-like) are easiest to infer (~70% success), 
INDEX assertions (array/sequence bounds) are moderate (~55%), 
and MULTI assertions (multi-line proofs with by clauses) are hardest (~25%) because they require complex multi-step reasoning that LLMs struggle to produce in Dafny's proof syntax.


----

## Relevant Details

1. One-shot prompting might not work: "we tried a straightforward one-shot prompting strategy: providing the complete failing program to the LLM and asking it to insert all missing assertions simultaneously. However, this approach was unreliable, as the model sometimes altered unrelated code."
2. Because of the above, they split the task into  'fault localization' and 'inference'. They claim that it reduces cost as well.
3. They created a standard prompt for LLM-based fault-localization.
4. They divided the expected assertions up into categories.  
- INDEX for assertions about sequences, sets, arrays;
- TEST: "Assertions resembling unit tests, often in Main."
-  MULTI: Assertions spanning multiple lines,
- OTHER
5. Combining multiple fault localization approaches gives higher performance.
6. Important note for us: LLM performance at multi-step assertion generation was quite poor aprox 25%.





















