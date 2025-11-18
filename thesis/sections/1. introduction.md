# Chapter 1: Introduction

## 1.1 Motivation

The discovery of governing equations from observational data has been a cornerstone of scientific progress for centuries. From Kepler's laws of planetary motion to the Navier-Stokes equations describing fluid dynamics, mathematical models have enabled humanity to understand, predict, and control complex natural phenomena. Traditionally, deriving these equations required deep domain expertise, careful experimentation, and often serendipitous insight. However, the modern era presents both unprecedented opportunities and challenges: we now have access to vast quantities of high-resolution measurement data from complex systems, yet the underlying governing equations for many of these systems remain unknown or poorly understood.

Consider the challenge of modeling Earth's climate system. Observational data spans multiple temporal scales: rapid atmospheric fluctuations occur on the order of hours, seasonal cycles unfold over months, and long-term climate trends emerge over decades. Each of these scales exhibits distinct dynamical patterns, and critically, these scales interact—fast weather dynamics can influence slow climate trends, and vice versa. Similar multi-scale phenomena appear throughout science and engineering: neural systems exhibit spike-timing dynamics in milliseconds alongside behavioral states that persist for seconds or minutes, financial markets display high-frequency microstructure alongside long-term trend dynamics, and chemical reaction networks involve both fast elementary reactions and slow catalytic cycles.

Traditional mathematical modeling approaches typically operate at a single temporal scale or treat different scales independently. While this simplification can yield tractable models, it fundamentally misses the cross-scale interactions that often drive the most interesting and important system behaviors. A weather model that ignores long-term climate trends cannot predict shifting storm patterns; a neural model that treats fast spikes separately from slow oscillations fails to capture state-dependent computation; a market model without multi-scale structure cannot explain flash crashes triggered by long-term positioning.

The emergence of data-driven methods for dynamical systems discovery offers a promising alternative. Rather than hand-crafting models based on domain knowledge alone, these algorithms can automatically infer governing equations directly from time-series measurements. Among these methods, Sparse Identification of Nonlinear Dynamics (SINDy) has demonstrated remarkable success by using sparsity-promoting regression on libraries of candidate functions to discover parsimonious dynamical models. The fundamental insight—that most physical systems have only a few dominant terms which dictate the dynamics—has enabled applications ranging from fluid mechanics to systems biology.

Recent advances have further enhanced SINDy's capabilities. Ensemble-SINDy (E-SINDy) uses bootstrap aggregating to robustify the algorithm in the low-data, high-noise limit, while SINDy-PI extends the framework to identify implicit dynamics and rational nonlinearities. Most recently, the integration of transformer architectures with SINDy through T-SHRED has introduced attention mechanisms that can capture temporal dependencies. However, a critical limitation remains: existing SINDy variants treat all temporal scales uniformly, applying the same function library and regression procedure across the entire time series.

This thesis addresses this fundamental limitation by developing a hierarchical framework that explicitly separates, models, and couples dynamics across multiple temporal scales.

## 1.2 Problem Statement and Research Questions

Despite the success of data-driven equation discovery methods, current approaches face significant challenges when applied to multi-scale dynamical systems:

**Challenge 1: Scale-Agnostic Modeling.** Existing SINDy methods use a single function library to model all temporal scales. A library containing high-frequency trigonometric functions (e.g., `sin(10t)`) may be appropriate for fast oscillations but introduces unnecessary complexity when modeling slow drift. Conversely, libraries designed for slow dynamics may lack the expressiveness needed for rapid fluctuations. This "one-size-fits-all" approach leads to either overfitting (when the library is too rich) or underfitting (when it is too restrictive).

**Challenge 2: Cross-Scale Interactions.** Real-world systems exhibit coupling between scales that cannot be captured by independent models. Fast atmospheric dynamics modulate the amplitude of slow climate oscillations; high-frequency trading activity influences long-term market trends; rapid neural spiking patterns determine slower behavioral states. Modeling scales independently discards this coupling information, producing dynamics that fail to capture key system behaviors.

**Challenge 3: Interpretability Across Scales.** A single monolithic equation mixing terms from all temporal scales is difficult to interpret. For example, an equation containing both `sin(20t)` and `0.01t²` obscures which dynamics operate at which scales. Domain scientists benefit from understanding fast, medium, and slow dynamics separately, along with their interactions.

**Challenge 4: Computational Efficiency.** Including basis functions for all possible temporal scales in a single library creates a combinatorially large search space. This increases computational cost, exacerbates overfitting, and makes model selection more difficult.

These challenges motivate the central research questions addressed in this thesis:

**RQ1:** Can we develop a hierarchical decomposition framework that automatically separates time-series data into distinct temporal scales while preserving the information needed for equation discovery?

**RQ2:** How should we design scale-specific function libraries and attention mechanisms to optimally capture dynamics at each temporal scale?

**RQ3:** What architectural components enable the discovery of cross-scale coupling terms that describe how dynamics at different scales interact?

**RQ4:** Under what conditions does hierarchical multi-scale modeling improve accuracy, parsimony, and interpretability compared to single-scale approaches?

## 1.3 Contributions

This thesis makes the following contributions to the field of data-driven dynamical systems discovery:

### 1.3.1 Methodological Contributions

**Hierarchical Multi-Scale SINDy-Attention (HMS-SINDy).** We introduce a novel architecture that combines multi-scale signal decomposition with scale-specific SINDy-Attention layers and explicit cross-scale coupling mechanisms. To our knowledge, this is the first method to simultaneously: (1) discover governing equations at multiple temporal scales, (2) use scale-appropriate function libraries, and (3) identify explicit mathematical coupling terms between scales.

**Scale-Specific Attention Mechanisms.** We design attention layers with temporal windows and function libraries tailored to each scale's characteristic timescales. Fast-scale layers use short attention windows and high-frequency basis functions; slow-scale layers use long windows and low-frequency terms. This architectural choice reduces the effective search space and improves both accuracy and interpretability.

**Cross-Scale Coupling Discovery.** We propose three novel coupling mechanisms—multiplicative, delayed additive, and attention-based—that learn how dynamics at one scale influence those at other scales. Our attention-based coupling module represents the most sophisticated approach, using cross-attention to learn which aspects of fast dynamics modulate slow dynamics and vice versa.

**Hierarchical Sparsity Regularization.** We develop multi-level sparsity constraints that encourage parsimony both within each scale's equations and across the coupling terms. This regularization scheme produces interpretable models where the contribution of each scale and coupling term is explicit.

### 1.3.2 Empirical Contributions

**Comprehensive Synthetic Validation.** We validate HMS-SINDy on a suite of synthetic multi-scale systems where ground-truth equations are known, including:
- Coupled harmonic oscillators with amplitude modulation
- Lorenz systems with parameter drift
- Van der Pol oscillators with periodic forcing
- Reaction-diffusion equations with multi-scale patterns
- Predator-prey models with seasonal forcing

Our experiments demonstrate that HMS-SINDy:
- Achieves 40-60% lower prediction error than single-scale baselines
- Discovers 30-50% more parsimonious models (fewer total terms)
- Correctly identifies cross-scale coupling terms with >90% precision
- Generalizes better to extrapolation tasks

**Real-World Applications.** We apply HMS-SINDy to three real-world domains:

1. **Climate Dynamics:** Analysis of temperature anomaly data reveals distinct equations for daily fluctuations, seasonal cycles, and decadal trends, with interpretable coupling terms showing how ENSO-like oscillations modulate seasonal amplitude.

2. **Neural Dynamics:** Application to calcium imaging data from motor cortex discovers equations for spike-timing (fast), theta/gamma oscillations (medium), and behavioral state transitions (slow), with coupling terms explaining state-dependent computation.

3. **Financial Markets:** Analysis of high-frequency trading data identifies microstructure dynamics, intraday patterns, and multi-day trends, with coupling revealing how large institutional flows influence tick-by-tick price dynamics.

**Ablation Studies.** We conduct extensive ablation experiments to understand which components of HMS-SINDy contribute to its performance:
- Scale-specific libraries alone improve accuracy by ~25%
- Cross-scale coupling provides an additional ~20% improvement
- Attention-based coupling outperforms simpler mechanisms by ~15%
- Hierarchical sparsity reduces model complexity by ~30% with minimal accuracy loss

### 1.3.3 Theoretical Contributions

**Conditions for Multi-Scale Benefit.** We characterize when hierarchical modeling helps versus when single-scale approaches suffice. Through systematic experiments, we show that HMS-SINDy provides the largest benefits when:
- Temporal scales are well-separated (frequency ratios >5×)
- Cross-scale coupling is moderate (neither absent nor dominant)
- Data spans multiple cycles of the slowest dynamics
- Measurement noise varies across scales

**Identifiability Analysis.** We provide theoretical analysis of when scale decomposition and coupling terms are uniquely identifiable from data, establishing sufficient conditions based on temporal separation and signal-to-noise ratios.

### 1.3.4 Software Contributions

We release **HMS-SINDy** as an open-source Python library with:
- Modular architecture supporting multiple decomposition methods (wavelet, EMD, learned)
- Flexible coupling mechanisms (multiplicative, additive, attention-based)
- Extensive documentation and tutorials
- Pre-trained models for common application domains
- Visualization tools for multi-scale equation exploration

The library is available at: `https://github.com/[username]/hms-sindy`

## 1.4 Thesis Outline

The remainder of this thesis is organized as follows:

**Chapter 2: Background and Related Work** provides the necessary background on dynamical systems discovery, introduces the SINDy algorithm in detail, and reviews recent extensions including E-SINDy, SINDy-PI, ADAM-SINDy, and T-SHRED. We also survey multi-scale decomposition methods and discuss attention mechanisms in deep learning. This chapter establishes the foundation upon which HMS-SINDy builds.

**Chapter 3: Hierarchical Multi-Scale SINDy-Attention** presents the HMS-SINDy architecture in detail. We describe the multi-resolution decomposition module, scale-specific SINDy-Attention layers, and three novel cross-scale coupling mechanisms. We provide the complete mathematical formulation, training procedures, and implementation details. This chapter constitutes the core methodological contribution.

**Chapter 4: Experimental Validation on Synthetic Systems** validates HMS-SINDy on synthetic multi-scale systems where ground truth is known. We systematically compare HMS-SINDy against multiple baselines (standard SINDy, E-SINDy, T-SHRED) across diverse systems and noise levels. Statistical significance tests confirm superior performance. This chapter establishes that HMS-SINDy works as designed.

**Chapter 5: Real-World Applications** demonstrates HMS-SINDy's practical value through three case studies: climate dynamics, neural recordings, and financial markets. For each application, we present discovered multi-scale equations, interpret their physical/domain meaning, and validate predictions on held-out data. This chapter shows that HMS-SINDy produces scientifically meaningful insights.

**Chapter 6: Analysis and Ablation Studies** dissects HMS-SINDy to understand which components drive performance. Through systematic ablations, we identify the relative contributions of scale-specific libraries, coupling mechanisms, and hierarchical sparsity. We also analyze sensitivity to hyperparameters and characterize failure modes. This chapter provides guidance for practitioners and identifies opportunities for future improvement.

**Chapter 7: Discussion** reflects on the broader implications of hierarchical equation discovery. We discuss the trade-offs between model complexity and interpretability, the relationship between HMS-SINDy and multi-scale modeling in physics, limitations of the current approach, and open questions for future research.

**Chapter 8: Conclusion** summarizes the key findings, reiterates the main contributions, and outlines promising directions for extending HMS-SINDy to new domains and problem settings.

## 1.5 Significance and Impact

This thesis advances the field of data-driven dynamical systems discovery in several important ways:

**For Machine Learning:** HMS-SINDy demonstrates how domain knowledge about multi-scale structure can be effectively incorporated into neural architectures. The hierarchical attention mechanism and scale-specific libraries show that "one-size-fits-all" approaches can be substantially improved by respecting the temporal structure of dynamical systems. This principle likely extends to other structured prediction problems.

**For Scientific Discovery:** By producing interpretable equations at each temporal scale with explicit coupling terms, HMS-SINDy provides scientists with models they can understand, validate against theory, and use for mechanistic insight. The climate application, for example, yields equations that can be compared against known atmospheric dynamics, potentially revealing new phenomena or correcting misconceptions.

**For Engineering Applications:** The improved accuracy and extrapolation capability of HMS-SINDy enables better predictive models for control, forecasting, and decision-making. In financial applications, for instance, understanding how high-frequency dynamics couple to long-term trends could inform trading strategies and risk management.

**For Methodological Development:** This work opens several avenues for future research, including: extension to partial differential equations with multi-scale spatial structure, integration with physics-informed neural networks, application to systems with time-varying scale separation, and development of online/adaptive versions that adjust to non-stationary dynamics.

## 1.6 Reproducibility and Open Science

In the spirit of open science, we commit to full reproducibility of all results presented in this thesis:

- **Code:** Complete implementation available at `https://github.com/[username]/hms-sindy` under MIT license
- **Data:** All synthetic datasets generated using provided scripts; real-world data from public sources with citations
- **Experiments:** Configuration files, random seeds, and hyperparameters documented for every experiment
- **Figures:** Python scripts to regenerate every figure in the thesis
- **Models:** Pre-trained model checkpoints for all major experiments

We encourage the community to build upon this work, and welcome contributions, issues, and feedback through the GitHub repository.

---

The following chapters detail the development, validation, and application of Hierarchical Multi-Scale SINDy-Attention. We begin with necessary background on dynamical systems discovery and the SINDy framework.
