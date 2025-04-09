
| Category                | Key Points                                                                                                                                                                                      | Metrics                                                        | Comparison to Dual-AAV                                              | Notes                                                           |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------- |
| **System Development**  | Removed W3 element (250bp) to fit sgRNA in same AAV                                                                                                                                             | Complete system in one virus                                   | Simpler delivery                                                    | Significant technical achievement                               |
| **Editing Efficiency**  | **High Dose:**<br>Liver: 64%<br>Heart: 23%<br>Muscle: 7.8%<br>**Medium Dose:**<br>Liver: 55%<br>Heart: 13%<br>Muscle: 5.5%<br>**Equal Total Dose:**<br>Liver: ~66%<br>Heart: 33%<br>Muscle: 22% | Heart: 1.4-4.8× higherMuscle: up to 2.5× higher                | Superior at all dosesEspecially in less-transduced tissues          | Results indicate therapeutic potential for multiple diseases    |
| **Editor Variants**     | **SaKKHABE8e:** 3.16kb, PAM: N₃RRT**Nme2ABE8e:** 3.24kb, PAM: N₄CC**CjABE8e:** 2.95kb, PAM: N₃VRYAC**SauriABE8e:** 3.18kb, PAM: N₂GG                                                            | Collectively target 82% of all adenines in human genome        | More compact than traditional SpCas9-based editors                  | Provides broad genomic coverage with small editors              |
| **Disease Targets**     | **Human PCSK9****Mouse Pcsk9****Mouse Angptl3**                                                                                                                                                 | Editing efficiencies:44-61% in liver                           | Comparable or superior to dual-AAV at same doses                    | Cardiovascular disease-relevant targets                         |
| **Protein Knockdown**   | **Human PCSK9:** 99%**Mouse Pcsk9:** 91%**Mouse Angptl3:** 94%                                                                                                                                  | Nearly complete elimination by 4 weeks                         | Similar at high dosesSuperior at medium doses                       | Among highest knockdown efficiencies reported                   |
| **Therapeutic Effects** | **Human PCSK9:**24% cholesterol reduction**Mouse Pcsk9:**25% cholesterol reduction**Mouse Angptl3:**38% cholesterol reduction45% triglyceride reduction                                         | Final levels:45-53 mg/dL cholesterol25 mg/dL triglycerides     | Comparable therapeutic effect at high dosesSuperior at medium doses | Matches effects seen in knockout mice and LNP-delivered systems |
| **Dose-Response**       | **High Dose (5×10¹² vg/kg):**54% editing**Medium Dose (5×10¹¹ vg/kg):**38% editing**Low Dose (5×10¹⁰ vg/kg):**3.7% editing                                                                      | Single-AAV advantage at medium dose: 5.6× higher (29% vs 5.2%) | Advantage increases as dose decreases                               | Clinically relevant dose range                                  |
| **Safety Assessment**   | Low off-target editing (up to 0.45%)Normal liver morphology                                                                                                                                     | Limited to one predicted off-target site                       | Similar safety profile                                              | Highlights importance of off-target analysis                    |
| **Manufacturing**       | Simpler productionSingle viral packagingLower total viral load needed                                                                                                                           | Requires half the viral particles for same effect              | More efficient production                                           | Important for clinical translation                              |

This table summarizes the key achievements of single-AAV adenine base editors, demonstrating their superior efficiency, broad targeting capability, and therapeutic potential, particularly at clinically relevant doses where dual-AAV systems become limiting.


Paper 1 Protocol. How did they prodiuce the lentiviruses?
Based on the provided content from Nature Protocols, I can answer your question about the lentivirus production method.

The protocol uses a PEI (polyethylenimine)-mediated transfection method to produce lentiviruses. Here's the specific delivery method described:

1. Three plasmids are used for lentivirus packaging:
    - pMD2G (VSV-G envelope-expressing plasmid)
    - psPAX2 (HIV-1 gag pol-expressing plasmid)
    - LentiCRISPRv2-sgRNA (the vector containing the guide RNA)
2. The transfection procedure involves:
    - Using 293FT cells (a fast-growing derivative of HEK 293T cells that produces 4-5× higher lentiviral titers)
    - Preparing a PEI-DMEM mixture combined with the three plasmids
    - Transfecting 293FT cells at 80-90% confluency in poly-D-lysine coated plates
    - Adding sodium butyrate at 16 hours post-transfection to increase yield
3. The lentiviruses are then:
    - Collected from the supernatant at multiple timepoints (24h, 36h, 48h, 60h, and 72h post-transfection)
    - Concentrated using PEG-6000 precipitation method
    - Resuspended in CMGF(-) medium and stored at -80°C

This is a standard triple transfection method for lentivirus production, using PEI as the transfection reagent rather than commercial alternatives like Lipofectamine.