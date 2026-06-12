---
layout: post
title: "What today's hiring market and the 2008 financial crisis have in common. It's not what you think!"
date: 2026-06-11 21:56:00 -0700
categories: [AI, Hiring, Risk]
tags: [algorithmic monoculture, model risk, FICO, hiring, fairness]
description: "How a new Stanford study on algorithmic monocultures in hiring echoes the systemic model risks we saw in the 2008 financial crisis."
toc: true
---

If you have flashbacks to 2008 when you hear the words "model risk," you are not alone. I spent the early part of my career building and working with predictive models in financial services, watching the industry learn the hard way what happens when everyone leans on the same handful of risk scores and rating models.

Fast forward to 2026, and I now work at the intersection of AI, product, and predictive analytics in a large financial services firm, still very much a math geek at heart. This week I read a new Stanford paper, *Algorithmic Monocultures in Hiring*, and it hit uncomfortably close to those pre‑crisis days in credit and structured finance. The authors analyze roughly four million job applications screened by a single hiring vendor and find strong evidence of "algorithmic monoculture" in the labor market. [web:12][web:14][web:23]

The headline finding is simple and unsettling: many employers are using hiring algorithms from the same small set of vendors, and those algorithms are producing highly correlated decisions across the labor market. [web:14][web:20][web:22] In this dataset, applicants do not just face one biased model once. They encounter the same logic again and again across different employers, and some groups end up systematically rejected at rates that are statistically far above what you would expect if each employer made independent decisions. [web:14][web:19][web:20][web:42][web:46]

## Credit scoring as the original monoculture

If you lived through the 2008 crisis, this should sound familiar.

For decades, FICO has been the de facto "single factor" for retail credit, used across cards, autos, mortgages, and more, precisely because it provides a consistent way to rank order risk across lenders and products. [web:29][web:31][web:40] Lenders built their own models and policy rules, but those models almost always included FICO as a key feature or hard threshold. Anyone who has ever bumped up against the classic 620 mortgage cutoff has experienced that monoculture in action. [web:26][web:29][web:40]

After the fact, we saw that default rates rose across all FICO buckets during the subprime collapse, and in some cases higher score bands experienced larger relative jumps in default than you would have expected from the old models. [web:27][web:30] The fine distinctions that seemed so reassuring in backtests did not hold up under stress, because everyone was exposed to the same macro factors, the same underwriting practices, and the same scoring infrastructure. [web:32][web:35][web:36][web:38]

In other words, FICO gave us a shared latent function \( f(x) \) that everyone leaned on, so credit outcomes across lenders were never independent in the sense implied by many portfolio and securitization models. [web:29][web:31][web:40]

## 2008 as a correlated risk story

In residential mortgage‑backed securities and CDOs, both investors and rating agencies treated large pools of mortgages as if defaults were close to independent, given observable risk factors like scores and loan‑to‑value ratios. [web:32][web:35][web:36][web:38] When those factors moved together and the common modeling assumptions failed, the diversification story dissolved.

The post‑mortems are full of phrases like "underestimated correlation" and "overreliance on common rating models." [web:32][web:35][web:36][web:38] The lesson is not just that some models had bad parameters. It is that many institutions made similar bets on the same modeling worldview. When that worldview was off, everyone was wrong together.

That, in a nutshell, is algorithmic monoculture.

## Replaying the pattern in hiring

Back to the Stanford paper. Today’s hiring vendors occupy a position that looks uncomfortably similar to FICO and the rating agencies. A small number of providers supply screening algorithms that many employers use as gates, sometimes with light customization but the same core feature engineering and modeling philosophy underneath. [web:14][web:17][web:19][web:20][web:21]

The study documents three uncomfortable facts:

1. When you look at each position separately, a non‑trivial share of roles show adverse impact against specific racial groups under standard U.S. discrimination tests. [web:14][web:19][web:20][web:42][web:46]

2. When you link applications from the same person across positions, you see some applicants, particularly Black and Asian candidates, facing repeated rejection at rates that significantly exceed what a simple independence model would predict. [web:14][web:19][web:20][web:42][web:46]

3. Taken together, this is not just one company’s model being a bit skewed. It is a shared vendor ecosystem that creates correlated errors and concentrates harm on the same people again and again. [web:14][web:19][web:22][web:23]

From a modeling perspective, this is not mainly a story about poor AUC or sloppy feature engineering. The models can look "fine" on their own validation metrics. The deeper issue is system design. We validate each model in isolation and mostly ignore the fact that they are all children of the same few parents. Performance metrics that quietly assume independence of decisions across employers simply do not describe the world we have built. [web:14][web:19][web:21][web:24]

As someone who now spends his days shipping AI systems in production, this is what worries me. We are very good at asking "does this model fit the historical data for this use case." We are much less good at asking "what happens when everyone uses versions of this model at once, and what kinds of correlated blind spots does that create."

## Model fit is not ecosystem health

In 2008, we learned that you can have thousands of assets, but if they all depend on the same fragile assumptions, you are not diversified. [web:32][web:35][web:36][web:38] In 2026, we may be about to learn that you can have thousands of employers and millions of candidates, but if they are all mediated by the same scoring vendors, you are not getting independent opportunities. You are getting an algorithmic monoculture. [web:14][web:18][web:20][web:22]

The Stanford paper is careful and empirical, not alarmist. It shows that a single vendor’s centralized system can drive hiring decisions at many employers, and that this centralization magnifies racial disparities and systemic rejection beyond what any one employer sees in their own dashboards. [web:14][web:19][web:20][web:22] That is the subtlety most non‑technical conversations miss.

To put it bluntly: "our model passed the fairness check for this requisition" is the 2026 version of "these tranches are AAA because the expected loss model says so." The problem is not that those checks are meaningless. The problem is that they are incomplete.

## The policy questions we are not asking

So where does that leave us?

Are we comfortable with a small number of vendors effectively becoming the FICO of hiring, with all the correlated model risk that implies? [web:14][web:18][web:20][web:22]

Should regulators and employers be auditing these systems only at the level of individual models or positions, or should we start measuring systemic rejection and cross‑employer correlations in outcomes as a matter of policy? [web:14][web:19][web:20][web:22]

How do we explain to non‑technical leaders that good model fit on paper is not the same thing as a healthy ecosystem, and that correlated errors and shared blind spots are exactly how you blow through all the green checkmarks on your validation report and still end up in trouble? [web:14][web:19][web:21][web:24]

And maybe the most uncomfortable question of all. After everything we lived through in 2008, are we really willing to pretend that this time our centralized scoring infrastructure will behave differently, simply because it is scoring people instead of mortgages? [web:14][web:19][web:20][web:21][web:32][web:36]
