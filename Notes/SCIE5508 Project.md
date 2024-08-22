---
Class: University
Status: Working
Priority: High
Week: 
Lecture:
  - 🟥
Flashcards:
  - 🟥
tags:
---
> Error generating daily quote

---
# Notes for SCIE5508 Project
Idea 1: Producing Astaxanthin using Yeast
- Astaxanthin can come from y-Carotene [Pathway](https://www.kegg.jp/pathway/map=map00906&keyword=Astaxanthin)
- Find Yeast that naturally produces y-Carotene
	> The group of yeast that can synthesize carotenoids includes **Phaffia rhodozyma (and its teleomorph Xanthophyllomyces dendrorhous) and species of the genera Rhodosporidium, Rhodotorula, Sporobolomyces, and Sporidiobolus**
- Add enzymes required to convert y-Carotene to Astaxanthin into Geneious

## Choosing and creating promotor and its terminator
- Find suitable promoters using genebank - type promoter and organism
	- Promoters: TDH2, TEF1, TTEF2, PGK1, TPI1, ADH1, PYK1, HHF1
	- Example for searching: TDH2 AND saccharomyces cerevisiae[ORGN] 
		- TDH2 is promoter, sacchar.... is organism, include [ORGN] to specify we are searching for an organism
	- Note: Mrna dont have coding for promoter, can find through "Related information - Gene" right hand side of screen
	- Go to Genomic regions, transcripts and products.
	- Zoom out until entire promoter region can be seen. Click download. GenBank Flat file. Visible Range.
	- Drag and drop the .flat file you just downloaded into geneious.
	- Remove redundant nucleotides (nucleotides which dont belong to the promoter):
		- Click on the promoter, copy it (ctrl+c) (if it says its reversed, reverse it)
		- Paste the reversed into the directory
		- Convert to promoter (how the hell did he do that????)
		- Label the type as Promoter
		- Extract terminator
			- Zoom in to the promoter (go after the stop codon), show GC content to see promoter and terminator region (GC content drops at promoter and terminator region)
			- Copy everything from the promoter to the next encoded protein (this is the terminator)
			- Annotate the terminator as terminator

## Cutting plasmid
Example plasmid has existing promoter Padh1 and Tcyc ( we want to cut in the middle of that promotor and terminator). Promotor, gene, terminator, promotor, gene, terminator.
- Find restriction site to cut using restriction enzyme (use restriction analysis / scissor button in geneious) Use commonly used enzymes / commercially used enzymes. Click the restriction enzyme / site, click cloning, digest into fragments.
	- Due to some strange issue with restriction enzymes, you need to click more options, deselect all enzymes, then click the enzyme of interest. Otherwise it will use all compatible enzymes.

## Combining onto plasmid
Put all in 1 folder, select all:
- Backbone (plasmid that was cut) Promoter1 + Gene for enzyme 1 + terminator1 + Promoter2 + Gene for enzyme 2 + terminator2 ..... repeat for total number of enzymes.
	- You must use different promoter from the promoter list otherwise Geneious has a stroke and does not assemble. Promoters cannot be repeated.
- Click cloning, Use Gibson Assembly to combine all.

## Additional Notes
The scaling issue:
- Imagine in big tank, 1 or 2 cells have metabolic genes mutated, no need to make metabolite, they have growth advantage and take over majority of tank - Heng mentioned something about how to counteract this, spoke too fast, dont know what he said.

How to choose the best promoter:
[You cant? I guess....? What is Heng on about?](https://2012.igem.org/Team:TU_Munich/Project/Constitutive_Promoter)




---
# References for SCIE5508 Project
![[Paper 23 - Toda et al. 2018 - Programming self-organizing multicellular structures with synthetic cell-cell signaling.pdf]]