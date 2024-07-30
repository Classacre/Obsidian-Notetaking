---
Class: University
Status: Working
Priority:
  - High
Week: 1
Lecture:
  - 🟩
Flashcards:
  - 🟥
tags:
---
> Error generating daily quote

---
# Unit Guide
![[UNIT GUIDE SCIE5508 (2024)(3).pdf]]

---
# Key Takeaways
## Lecture 1
- Need to post a question on the discussion board weekly and attend the workshops to get attendance marks

## Lecture 2
### Two Key Motivations of Synthetic Biology
#### Motivation I: Build to understand
- Scientists break down biological systems to analyze parts within the system, this is a micro level approach to understanding how a biological system works. Alternatively, synthesis is using these analyzed parts and attempting to reconstruct the system and seeing if we are able to replicate the system - this is a macro approach to understanding how a system works.
	- One such example of a micro approach is: Jacques Monod and Francois Jacob looked at Lac operon in E . coli, they found that genes are usually in off state and represses gene activity. This was the start of their understanding of gene circuitry - the influence of a repressor substance of inducer complex on gene transcription affects production of lactose (this was a small part of the lactose producing system but identified one factor which was in charge of lactose production)
#### Motivation II: Build to improve & innovate
- Engineers use understandings from parts (analyzed by scientists) to try and recreate improved and novel systems. Manipulation and creation of these systems include: Building synthetic circuits, Metabolic engineering, synthetic gene and genome synthesis, minimal cells & proto cells and Xenobiology
	- One such example of motivation II is the construction of a genetic toggle switch in Escherichia coli. Scientists understand that E. coli uses two transcription factors to create GFP (cl-ts and lacl), these transcription factors produce repressors which repress on another. These repressors may be artificially induced into the system to controll the expression of repressors, which in turn controls the expression of GFP. Induction of heat into the system restricts the expression of the repressor produced by cl-ts, while induction of IPTG in the system restricts the expression of the repressor produced by lacl. This allows scientists / engineers to control GFP production.
	- More advanced systems can be manipulated such as oscillatory networks - can serve as model systems for circadian rhythms
### History of Synthetic Biology and Modules of Synthetic Biology
#### Building Synthetic circuits / signaling circuits
- construction of a genetic toggle switch in Escherichia coli. Scientists understand that E. coli uses two transcription factors to create GFP (cl-ts and lacl), these transcription factors produce repressors which repress on another. These repressors may be artificially induced into the system to controll the expression of repressors, which in turn controls the expression of GFP. Induction of heat into the system restricts the expression of the repressor produced by cl-ts, while induction of IPTG in the system restricts the expression of the repressor produced by lacl. This allows scientists / engineers to control GFP production.
	- More advanced systems can be manipulated such as oscillatory networks - can serve as model systems for circadian rhythms
#### Metabolic Engineering
- Synthetic biology has enabled researchers to design and build complex synthetic circuits within cellular systems, allowing for unprecedented control and programming of biological processes. At the heart of this field is the ability to engineer genetic components, such as promoters, genes, and regulatory elements, and assemble them into integrated circuits that can perform specific functions. Just as electronic circuits are built from transistors, resistors, and other components, synthetic biologists construct genetic circuits using well-characterized biological "parts" that can be combined in modular ways. These parts may include promoters to control gene expression, coding sequences for proteins with desired activities, and regulatory elements like riboswitches or transcription factors that can respond to environmental signals. By carefully assembling these parts into circuits, researchers can program cells to sense inputs, make decisions, and produce desired outputs, much like an electronic circuit.
	- ![](https://i.imgur.com/is0E6fR.png)In the top image, the researchers introduced the entire Artemisinin biosynthetic pathway directly from the plant Artemisia annua into E. coli. This allowed E. coli to produce artemisinin and related terpenoids through this plant-derived pathway.
	  
	  On the other hand, the bottom image shows the researchers engineering E. coli to utilize the mevalonate pathway, which is a different terpenoid biosynthesis route typically found in organisms like yeast. This heterologous mevalonate pathway was expressed in E. coli, in addition to the native DXP pathway.
	  
	  By having both the mevalonate pathway from yeast and the DXP pathway natively present in E. coli, the bacteria was able to more efficiently channel the carbon and energy resources towards the production of the desired terpenoid compounds. This multilayered metabolic engineering strategy likely provided more flexibility and higher flux through the overall terpenoid biosynthesis network.

#### Synthetic gene and genome synthesis
- Researchers leverage their ability to precisely engineer genetic circuits and pathways to program microorganisms for the production of valuable chemicals and biomolecules. Synthetic biologists can create microbial cell factories capable of efficiently synthesizing target compounds.
	- Researches took the genetic coding (3 DNA fragments F1-F3) of the poliovirus, synthetically transcribed the fragments, assembled them onto a cDNA  and expressed the viral RNA from a T7 promoter. Results show that it is possible to synthesize an infectious agent by in vitro chemical-biochemical means solely by following instructions from a written sequence
#### Minimal cells & Protocells
- Minimal cells are built to try and replicate the simplest origins of life by creating the simplest possible living organic system
	- Scientists encapsulated RNA in a lipid. Under high magnesium concentrations these lipids formed a fatty acid membrane, which mitigates destabilizing effect of Mg2+, allowing RNA templates to chemically copy itself inside of fatty acid vesicles.
#### Xenobiology
- The creation and engineering of alternative biochemistries that deviate from the natural biological systems found on Earth. Rather than solely working within the constraints of the canonical DNA-RNA-protein framework, xenobiologists seek to develop life forms that utilize novel genetic polymers, amino acids, and metabolic pathways.
	1. Replication: The DNA molecule, which serves as the genetic material, is replicated using the standard A-T and C-G base pairing.
	2. Transcription: The DNA is transcribed into RNA, which typically acts as the intermediate messenger molecule.
	3. Translation: The RNA is then translated into proteins, the functional components that carry out the biological activities.
	   
	   This new base pair, represented by the artificial base pair in the image, is designed to be orthogonal to the natural DNA base pairing. The goal is to create a self-contained, genetically isolated system that cannot easily interact or exchange genetic material with naturally occurring organisms.
	   
	   By engineering this alternative genetic code, xenobiologists aim to develop novel life forms that utilize these non-natural nucleic acids and amino acids, potentially leading to the creation of organisms with unique capabilities or the ability to thrive in environments that are inaccessible to natural life.


### Fundamental engineering principles underlying Synthetic Biology
#### Abstraction
- To make things simple
	- Facilitate complex biological processes through analogy to electrical engineering 
	- Think binary: genes in ON/OFF state
	- Ignore unnecessary details
#### Modularity
- To make things modular
	- Define modules as closed (insulated) sub-systems with specific input-output characteristics
	- Ignore minor interactions (cross-talk) with other module
#### Standardization
- Establish commonly used annotations, cloning standards, laboratory procedures, measurement protocols, etc. 
- Enable re-usability of well-characterized genetic material (=parts!) in different applications
#### Modelling & Design
 - Use mathematical models to describe biological function of parts, modules, etc.
 - Design novel functions based on model prediction
### The concept of the Design-Build-Test-Learn cycle
#### Design
#### Build
#### Test
#### Learn
### Global challenges that Synthetic Biology seeks to address
#### Biofuels and bioenergy
#### Bio-based chemicals
#### Biomaterials
#### Bioremedation
#### Biosensors
#### Biomedicine - Diagnostics
#### Food production
#### Drugs and Vaccines
#### Biomedicine - Therapeutics




---
# Notes for SCIE5508 Introduction
> [!PDF|yellow] [[SCIE5508_L1(1).pdf#page=2&selection=11,16,11,23&color=yellow|SCIE5508_L1(1), p.2]]
> > Germany
> 
> we are in good hands

> [!PDF|yellow] [[SCIE5508_L2.pdf#page=3&selection=2,0,2,42&color=yellow|SCIE5508_L2, p.3]]
> > Foundational concepts of Synthetic Biology
> 
> test

> [!PDF|yellow] [[SCIE5508_L2.pdf#page=5&selection=19,0,19,7&color=yellow|SCIE5508_L2, p.5]]
> > Example
> 
> looks life like (like mitotic spindle_ but underlying processes used to produce the pictures are very different than processes doing mitosis - hence the analogy is not proving the mechanisms going on in biology) Leduc used the wrong parts to build life, but he shared his motivation to "build to understand" life

![[SCIE5508_L2.pdf#page=6&rect=834,439,1064,584&color=yellow|SCIE5508_L2, p.6]]then see if our recronstrcted system works the same way / behaves the same way as the original system

![[SCIE5508_L2.pdf#page=7&rect=107,84,564,364&color=yellow|SCIE5508_L2, p.7]]Jacques Monod and Francois Jacob looked at Lac operon in E . coli, they found that genes are usually in off state and represses gene activity. ![](https://i.imgur.com/8o4yP2U.png)

> [!PDF|yellow] [[SCIE5508_L2.pdf#page=8&selection=15,0,15,19&color=yellow|SCIE5508_L2, p.8]]
> > Restriction enzymes
> 
> ![](https://i.imgur.com/GOJshDu.png)

![[SCIE5508_L2.pdf#page=16&rect=1059,481,1864,871&color=yellow|SCIE5508_L2, p.16]]
cl-ts inhibits PL
PL required to transcribe lacl
lacl inhibits Ptrc2
Ptrc2 required to encode cl-ts

as genetic engineers, we turn on and off the promoters (cl-ts and lacl) by modifying heat and IPTG, which changes GFP to either on or off.

![[SCIE5508_L2.pdf#page=19&rect=99,659,1219,894&color=yellow|SCIE5508_L2, p.19]] creating a cheaper way to create artemisinin (anti malaria drug) - artemisinin normally taken from plants (costs $10) had the idea to extract gene that made artemisinin and put in E. coli
![[SCIE5508_L2.pdf#page=19&rect=400,786,640,836&color=red|SCIE5508_L2, p.19]] **an organic compound involved in the production of cholesterol and other essential molecules**.
![[SCIE5508_L2.pdf#page=22&rect=67,556,740,895&color=red|SCIE5508_L2, p.22]]creation of whole genome from scratch
![[SCIE5508_L2.pdf#page=25&rect=1594,595,1775,755&color=yellow|SCIE5508_L2, p.25]]previously extinct virus

![[SCIE5508_L2.pdf#page=25&rect=1513,280,1771,409&color=yellow|SCIE5508_L2, p.25]]shows we can store information in DNA

![[SCIE5508_L2.pdf#page=26&rect=1238,682,1429,813&color=yellow|SCIE5508_L2, p.26]]cleave away unnecesary stuff and create a "minimal" cell

![[SCIE5508_L2.pdf#page=29&rect=34,60,707,825&color=yellow|SCIE5508_L2, p.29]]biochemists trying to find the origin of life through "bottom-up" approach, using molecules found in primordial earth to try and create simpel life. Scientists encapsulated RNA in a lipid. Under high magnesium concentrations these lipids formed a fatty acid membrane, which mitigates destabilizing effect of Mg2+, allowing RNA templates to chemically copy itself inside of fatty acid vesicles.
![[SCIE5508_L2.pdf#page=30&rect=1058,205,1301,466&color=yellow|SCIE5508_L2, p.30]]is it possible to create life from purely synthetic polymers instead of traditional DNA and RNA?

![[SCIE5508_L2.pdf#page=31&rect=932,594,1828,880&color=yellow|SCIE5508_L2, p.31]]capable of base pairing with DNA and RNA, base emoeity is identical and only backbone is changed. They have different properties than DNA and RNA (different helical twists) but can still bond with DNA and RNA










---
# Flashcards for SCIE5508 Introduction


---
# References for SCIE5508 Introduction
![[SCIE5508_L1(1).pdf]]
![[SCIE5508_L2.pdf]]