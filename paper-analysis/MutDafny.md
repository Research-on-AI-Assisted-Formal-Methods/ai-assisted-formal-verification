Paper Name: MutDafny: A Mutation-Based Approach to Assess Dafny Specifications


ArXiv Link:  https://arxiv.org/abs/2511.15403

PDF Link: https://arxiv.org/pdf/2511.15403

HTML Link: https://arxiv.org/html/2511.15403v2

My own high-level analysis: Given a formal artefact (i.e., a formal spec, and a implementation), mutation testing will modify the implementation, to test whether the specification was strong enough to detect ta
 'change' in the implementation, if the spec is too permissive, it will fail to detect those changes. In this paper it was able to detect around 82% of the injected faults. This is a
testing process downstream of formal artefact generation, and can test the quality of the 'spec' in a (formal spec, formal artefact) pair.
