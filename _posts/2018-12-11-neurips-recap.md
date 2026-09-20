---
layout: post
title: "NeurIPS 2018 Recap by Forge.AI"
comments: true
---
With quick reflexes and a fortunate server error, I was lucky enough to get a ticket to the 2018 Neural Information Processing Systems Conference (NeurIPS). It was with great excitement that I attended to represent Forge.AI this year. NeurIPS provides its attendees with a week of talks, demonstrations, and incredible networking opportunities. I was able to catch up with old friends, and meet new friends and potential collaborators. For those of you who weren’t lucky enough to score a ticket, I thought it would be useful to provide a collection of highlights from the conference.

<!--more-->

![]({{ site.url }}/assets/neurips_2018/nips_sign_edit.jpg)

## Scalable Bayesian Inference
On my first day at NeurIPS, I was fortunate to attend a tutorial by Professor David Dunson from Duke University. 

Professor Dunson gave a beautiful overview of techniques for scaling markov chain monte carlo (MCMC) to both large datasets and high model complexity. Optimal transport methods based on Barycenter calculation in Wasserstein space were discussed at length, with scaling results that look extremely promising and relevant to some of the inference tasks Forge.AI is tackling.

Professor Dunson opened a discussion about the high model complexity limit, coarsened Bayes (c-bayes), modular Bayes, and other techniques. In particular, the idea of c-Bayes is both philosophically disconcerting and aesthetically beautiful. I’ve personally always considered Bayes’ theorem to be on the same footing as Kepler’s law, so making minor modifications out of modeling convenience feels strange particularly because Bayes’ theorem provides a mechanism to have statistical strength from the observed data dominate the model structure when the signal is strong enough; and this modification down-weights that mechanism. 

![]({{ site.url }}/assets/neurips_2018/dunson.jpg)

Of course, that’s not to say that I feel like this is a bad idea. Ultimately, the use of this theory makes parameter estimation possible without having to necessarily worry about small amounts of data-noise. It appears particularly convenient, especially when the data noise model isn’t available or easy to infer. I will have to explore the technique more to understand the settings where it’s preferable to use c-Bayes rather than explicitly model data-set noise, but I have a hunch that c-Bayes will be useful in knowledge graph construction tasks where I have a small amount of string noise (typos and abbreviations) without having to provide explicit string noise models.

## Causal and Counterfactual Inference Techniques
Later the same day, Professor Susan Athey from Stanford University gave a wonderful overview of causal and counterfactual inference techniques. In her presentation, she discussed many of the algorithms and applications with very specific example use-cases in mind. This really helped to ground a difficult-to-pin down talk concretely and succinctly. 

The professor’s talk made it painfully obvious how Forge.AI can combine knowledge graphs with counterfactual inference to perform AI guided speculative analyses. For instance, automatically answering the question “what would happen to Tesla’s stock price if there was an uprising in the Democratic Republic of the Congo?”.

## Other Highlights
The rest of the week was filled with interesting talks, posters, and conversations. For instance, I ran into the Alexandria team at the Microsoft booth. They’re focusing on applying probabilistic programming to high precision knowledge graph construction. Both are projects close to my heart, and I loved hearing about how they combined them together. It was particularly exciting to learn how their token-based string model combined character-based likelihoods with token and dictionary based likelihoods to automatically learn format models. Using these models to achieve a precision greater than 95% would represent a true step forward in automated knowledge graph construction, and I can’t wait to read the paper.

![]({{ site.url }}/assets/neurips_2018/kg_poster.jpg)

I also attended the workshop on challenges and applications of AI in the Financial Services space. It was an absolute treat to learn about how the researchers in the the finance sector envision bringing in ML techniques. It was incredibly useful to see how important fairness, privacy, and explainability are in making day-to-day algorithmic decisions. As a data-provider with a prominent vertical in the financial services industry, it was useful to understand precisely what was meant by the term explainability. On multiple occasions, both the panel speakers made and the invited speakers the point that explainability was mostly desirable due to regulatory constraints and audit protections. 

Even though everyone was in the same industry, explainability meant different things to different parts of the industry. There are many situations where individual decision makers are personally liable, and being able to provide the analyst with the ability to explain a potential poor decision by diagnosing a tool is highly desirable. Explainability in the credit card applications space tends to focus on generating adverse action codes to explain a decision, to provide the end user with a view of how they can remedy any defects with their applications.

Additionally, it was useful to hear a repeated emphasis on uncertainty predictions and the usefulness of understanding how to leverage uncertainty in making business decisions whether those decisions are underwriting a mortgage, offering a credit card, or making a trade. I found this personally validating because Forge.AI has constantly pushed to keep track of confidences and transparently report them, to inform our customers and their models of any downstream uncertainties that we may have.

NeurIPS was an amazing experience this year, and I look forward to returning next year with a larger Forge.AI cohort. Hopefully we’ll even be presenting some of our interesting work. We’ll probably have to write a bot to sign us all up so that we all can actually get tickets, but that sounds like a perfect task for a tech company like us. Maybe we’ll even get mugs next year.

Note: This post was originally published on the Forge.AI blog:
[https://www.forge.ai/blog/neurips-2018-recap-by-forge.ai](https://www.forge.ai/blog/neurips-2018-recap-by-forge.ai)
