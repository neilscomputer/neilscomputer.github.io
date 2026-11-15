---
layout: page
title: VCD report
description: What tricks can help an AI vision model to stop "seeing things"?
importance: 1
---

**[Read the full report (PDF)]({{ '/assets/pdf/vcd-report.pdf' | relative_url }})**: _Pixels Are (Probably) All You Need_, final project for ECE 209-AS: Trustworthy AI, April 2026.

### What it's about

Vision Language Models (VLMs) combine a visual encoder with an LLM. This allows you to form a multimodal prompt that
involves text and an image; it's really useful if you want to query an image, like "How many cows are in this picture?"
To design a cow-counting system without a VLM is quite an undertaking, and would involve creating and training a custom
cow-detector model, among a number of other pieces. But instead, you just download a model like `LLaVa`, hand it a picture,
and ask it any old question!

It's like magic! Only, these sorts of models are prone to hallucinating. Sometimes it hallucinates that a cow is there when
it isn't. Or it isn't when it is. Or it confuses a cow for a chicken, or a space ship. And show it a picture of Cthulu, and
it goes bonkers. It'd be swell if we could reduce the hallucinations without training a completely new model, or making the
model so large it doesn't run on commodity hardware, or require so much training data that it requires Capital Investment
in order to build it. This is where Visual Contrastive Decoding (VCD) comes in.

Back up one step to how language models work -- they predict the next token of text given the past tokens that are in
the context window; most people that "do AI" are familiar with that. However, to do that prediction, at each step the model
generates a probability distribution across _all possible_ tokens, and then samples from that distribution. That sampling
could just take the most probable token, or it could do a random sampling, or something more sophisticated called
"beam search", but at heart you are just running a sampling step for each token, in order. Obviously it matters a great
deal what that probability distribution is -- and that's actually the output of the model itself: given a context window,
output the probability distribution of that next token. It's the wrapper around that model that performs the sampling,
adjusts the window, and then repeats until the model outputs the "done" token.

Contrastive decoding is a technique that adjusts the distribution at each step. Essentially, you take your original prompt ($O$), and then you take a mutated prompt ($M$), compute the next token's probability distribution for both $O$ and $M$,
and then you adjust $O$'s distribution based on $M$. The reason you do this is because you can (in theory) remove
statistical bias that is inherent in the training data.

For example, take the word `cat`. The word `dog` will tend to appear more frequently in English text with `cat` than
would, say, the word `clown`. One hypothesis for hallucination in VLMs is that this statistical bias bleeds into the VLM
itself: show a picture of a cat, then ask if it has a dog, and the model will be more likely to say "yes" simply because
`cat` (from the picture) is related to `dog` (from the text). So, if you take the same dog-based text prompt, but show
it the original picture and something totally unrelated (say, some noise) then you can down-weight the biased token
probabilities. That's the basic idea behind VCD.

The original paper that proposes VCD demonstrated this in action by mutating the prompt by taking the original text,
and then running the original image through a number of steps of diffusion noising (essentially the reverse of what
"Stable Diffusion" does for generating images). What my project paper covers is the robustness of the results to the
noising method. In particular, what I found was that noising the image was unnecessary, and a blank image actually
worked better. It also compared different types of prompt mutations, and studied the extent to which even simple prompts
affected benchmark results.


### Observations

I think this is what one might call "an intermediate result" and it's in an odd spot. It doesn't
introduce a significant new approach, but it does alter an existing one to be more efficient. That's
something...it's not nothing. I probably should reach out to the original VCD authors and start a discussion,
and I might.

In my opinion, though, the real value of project work like this is that it opens up a bunch of new directions
that are probably more interesting to pursue:

* The benchmarking methods aren't really sufficient to give any indication on real world performance. The benchmarks are also weak, with some models achieving performance over 80% without any modifications.
* Reproducability in this area is poor, and Someone Should DO Something. Multiple published papers (I think mostly at conferences?) listed conflicting measurements of, purportedly, the same thing. And in some cases, the primary results that those respective papers claimed to find do not hold up on reimplementation. This ain't my first rodeo and it's somewhat encouraging to meet an old problem in a new context.
* Research software rots quicker than "industrial" software. It may need to do something really unusual that the public APIs of a package (like PyTorch) don't have a facility for, and it may be written for the resources that the researcher has available. The problem with this is, again, reproducability.

The irony that the code that generated the results in this paper is private, in a private repository, and will likely
not become public, is not lost on me. If anybody asks for it, I can probably move it over to a new repository, but
as run it was part of my "UCLA grad school" repository, which includes all my homeworks, notes and other projects which
are not supposed to be public (eg, the homework problems and the notes are not my intellectual property to give away).

