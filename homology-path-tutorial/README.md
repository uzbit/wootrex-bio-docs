
# Homology Path Tutorial


## Intro

Welcome to Homology Path, a software suite designed to help you optimize your DNA based workflows and experiments. Depending on your experiment parameters, there are several ways to flow your data through this system. Let's start with a basic example: Oligo Design. 

## Oligo Design

### Basic Oligo Design

Assuming your sequences of interest are DNA, you can get a set of design files to synthesize those DNA sequences from single stranded oligonucleotides. Using the Oligo Design tab, click the “Choose Files” button to select your FASTA file containing the DNA sequence(s) you wish to synthesize. You’ll notice that there are a variety of parameters to modify the design. We’ll go over those in a minute. For now, just leave all the defaults as they are. Scroll down and you will need to enter your email and the Access Token provided by NinthBio. Next click “Run Oligo Design”. When the calculation completes, an image of the design is displayed and the design data is available for download. Let’s look at the design data files. 


### Design Data Files

In the zip file you’ll find several excel files as well as an SVG and a log file. The name of the excel files is taken from the “Design Name” field on the Oligo Design page. Since we left the defaults, this should be “design01”. Open the “design01.xlsx” file. 

The “Oligo Order Form” tab in this file has tabular information about all of the oligos required to order for the sequences provided. This should be used to order oligos from your favorite oligonucleotide manufacturer, for example, IDT, Twist, or Molecular Assemblies. There is a similar tab for design primers.

The “Oligo Plate Map” tab has information to tell automated liquid handlers what plate and well to source a given oligo from and what destination wells that oligo should go into. One oligo may go into many destination wells if its recycling is high across all the sequences given. Again, there is a similar tab for design primers. 

The other tabs provide various other design information that may be useful as well.


### Oligo Design Parameters

Now that we’ve got basic oligo design input and output, we can explore some of the more powerful features of the Oligo Design tool. First note, that under the “Design Type” heading, there are several options to choose from. These are “Maximize Recycling”, “Minimize Complexity”, “Gapped Design”, and “Naive Design”. We’ll go through these differences in more detail later. For now, let's scroll down and look at the parameters that are in common for all the design types. These start underneath the “Design Name” field. You can specify minimum, maximum, and target length for each oligo. Additionally, you can specify minimum and maximum overlap lengths. These give the Oligo Design algorithms some room to work with when discovering the optimal design for a set of sequences. Additional parameters are number of wells for the order plate size “Source Plate Size” and pooling plate size “Destination Plate Size”, the how to layout the wells in the design file “Plate Layout”, the initial primer size “Target Primer Size” and the temperature for optimizing the primer melting temperature “Primer Target TM Temp” in degrees Celsius. 


#### Maximize Recycling 

When designing libraries or other sets of DNA sequences with lots of homology shared between them, maximizing the recycling of oligos where possible will save both time and money. Currently, there is only one tunable parameter specific to “Maximize Recycling” and that’s “Estimated Identity”. This value is used to estimate how similar sequences should be in order to include them in the same partition when designing oligos. Trying different values between 0.8 and 1.0 could potentially improve the overall recycling efficiency, depending on your dataset. 


#### Minimize Complexity

If you have sequences that contain difficult to synthesize regions, such as repeats, hairpins, extremely high and extremely low GC regions, you can use this design type to help minimize synthesis issues. By default complexity minimized designs are done using gapped designs. There are seven “Minimize Complexity” parameters that allow the user additional control over this design type: “Min Z-Score Cutoff”, “Optimize Overlap TMs”, “Temperature (C)”, "Temperature Delta Cutoff (C)", "Concentration Na (mM)", "Concentration K (mM)", and "Custom Cuts File". 
* Min Z-Score Cutoff - used to determine when an oligo pairing free energy is decidedly an outlier, with lower (more negative) values filtering out more pairs. This is the most important parameter to tune when optimizing a design.
* Optimize Overlap TMs - oligo overlaps can be TM optimized so that they have near identical melting temperatures. Having similar overlap TM's will enable more successful synthesis.
* Temperature (C) - this temperature in degrees Celsius should be consistent with the temperature of your annealing step in assembly. This field is located below and is used for the primer temperatures as well as oligo overlap TM optimization.
* Temperature Delta Cutoff (C) - when optimizing overlap TM's, this is the allowable difference between the target temperature (above) and the current as seen for each overlap in the design being optimized.
* Concentration Na (mM) - this is the intended millimolar Na salt concentration.
* Concentration K (mM) - this is the intended millimolar K salt concentration. 
* Input Custom Cuts File - additionally there is an option to upload a custom cuts file that allows you to specify specific cut positions for each sequence, where a cut position is defined as either the 3p end or the 5p end of an oligo. This can be useful for ensuring that a oligos can split, or combine, some known complex region. The format of the custom cuts file can be seen in the example link where each line corresponds to a sequence id in the set of fasta files. Only the sequences you wish to specify custom cuts for need to be included here.


#### Gapped Design

This design type allows for having gaps between adjacent 5p and 3p oligos. This reduces the number of DNA bases required to synthesize a given sequence by allowing the polymerase step to fill in the missing regions during extension phases in the assembly process. There are five “Gapped Design” parameters that allow the user additional control over this design type: “Optimize Overlap TMs”, “Temperature (C)”, "Temperature Delta Cutoff (C)", "Concentration Na (mM)", "Concentration K (mM)", and "Custom Cuts File". See above for the description of these parameters.  


#### Naive Design

This design type creates a basic oligo design with no optimizations or gaps. Oligo recycling is still performed where possible.

## Sequence Complexity

Some DNA sequences of interest may contain characteristics that will cause a naive design and assembly to fail synthesis. Common causes are repeat regions, too high or too low GC content, or hairpins for example. These often result in truncated product, concatamers, or no product at all. Here, we term these types of sequence content 'complexity', in order to describe their affect on sequence synthesis difficulty. Using the Sequence Complexity tab, you can submit multiple DNA sequences for complexity assessment. Below is an example sequence showing all types of complexity that are assessed. 

![Sequence Complexity Example](images/sequence-complexity-example.png)

From top to bottom the types of complexity are:
* Interspersed repeat regions - shown in light green, these are regions that share homology with each other and are likely to cause mis-annealing events during oligo assembly and PCR.
* Palindromic repeat regions - shown in yellow, these are regions that could cause hairpin formations and are likely to cause oligos to become unavailable for expected annealing.
* Tandem repeat regions - shown in pink, these are regions have repeating k-mers. Homopolymers are a special case of k=1 (minimum count of 7 in a row) while the maximum reported kmer is k=6 (minimum of 2 in a row). 
* Low and high GC regions - shown in light blue and light red respectively, these are stretches of 40bp that have at most 30% (low) or at least 70% (high) GC content. High GC regions can cause issues with oligos finding their intended overlaps.
* Complexity Free Energy self pair - shown in dark red, these are designed oligos that have a free energy of annealing with itself (ΔG) that lies outside the z-score cutoff for the population of other oligos with themselves. 
* Complexity Free Energy disjoint pair - shown in dark green, these are designed oligos where the two indicated oligos have a free energy (ΔG) of annealing with each other that lies outside the z-score cutoff for the population of all oligo pairs that are not self or intended overlap pairs.
* Complexity Free Energy overlap pair - shown in dark blue, these are designed oligos where the two indicated oligos have a free energy (ΔG) of annealing with each other that lies outside the z-score cutoff for the population of all intended overlapping oligo pairs. These are oligos that are intended to overlap via the design and that their free energy was sufficiently low, such that the intended anneling may not occur. This complexity type is not shown in the example above.

### Sequence Complexity Parameters

You'll notice that the parameters available for Sequence Complexity are similar to the Oligo Design parameters. This is because, at the moment, the repeat and GC parameters are fixed. The parameters shown allow you to simulate a naive or gapped oligo design and see which, if any, oligos or region of the sequence will have some likelyhood to cause mis-annealing events (high free energy complexity scores above). 

### Sequence Complexity Interpretation

For the complexity assessment shown in the image above, you can see that the oligos making up the repeat regions have high free energy disjoint pair complexity with each other, indicating that these will likely mis-anneal. Also shown is an oligo that has high free energy self pair complexity, indicating a self-affinity that occurs right at the spot containing a palindromic region. This implies that the oligo will likely anneal to itself (hairpin) or other oligos of the same sequence. 

### Design Suggestions Based on Sequence Complexity

At the moment, there are two options to work around sequence complexity. 

1. If you have a hard requirement on keeping that specific DNA sequence, meaning changing the sequence is not an option, you can use the Minimize Complexity design type for Oligo Design. This design type uses the Complexity Free Energy information for a given design and attempts to minimize the number of oligos that may cause mis-annealing events. See the Minimize Compleity design type description above for more details on running this type of design.

2. If you are only concerned about the protein sequence and the specific DNA sequence is of no major concern, you can use the Sequence Design page Minimize Complexity design type to help remove the complex regions from the sequence. This design type takes either protein or DNA and uses codon usage tables along with Complexity Free Energy information to minimize the regions with offending sequence complexity. See the Sequence Design section below for more information.

## Sequence Permutation Generator

If you're designing libraries of DNA with variation across a specific sequence of interest, you may have already used Oligo Design to save on oligo costs by recycling oligos. As it turns out, some designs allow for creation of novel sequences to assay using oligos that you've already designed. This is where Sequence Permuation Generator comes in. Given a library design, you can submit that excel file, along with a list of sequence id's in that design file, to create a new set of unique sequences to assay using the same batch of oligos! We term this "oligo mining". It allows you to extract additional value out of library oligo designs that you already ordered or intend to order. 

![Crank Out Libraries Like A Boss](images/synbiobeta-2023.jpg)
*NinthBio + teselagen at SynBioBeta 2023*

### Sequence Permutation Generator Parameters

* Input Design File(s) - one or more design excel files produced by Oligo Design containing the oligos and sequences you would like to mine for additional value.
* Permutation Design Name - designate a new name for the unique set of sequence permutations.

There are currently two differen ways you can run SPG, the first is by knowing the set of sequence id's, for instance your experimental top hit sequences, or by using phylogenetic similarity to a given sequence. To select the latter options, check the Use Phylogenetic Similarity Selection checkbox. Otherwise the default parameters are as follows:

* Sequence Id List - this is the set of sequences that you'd like to mine oligos from in order to create new sequences. Commonly, people choose sequences with hits showing promise from assays already performed.
* Subsample Population - if the number of potential sequences generated is too high, use this parameter to evenly subsample the population generated.

For the Phylogenetic Similarity Selection option the parameters are as follows:
* Target Sequence Id - A single sequence id for which the N (Number of Related Sequences below) closest sequences in phylogenetic distance will be selected.
* Number of Related Sequences - Number of sequences closest to the target sequence to include from the original design file given.
* Max Hamming Distance Filter - A final filter after all sequence permutations are generated that allows one to select only sequence with less than or equal to this number of positional base pair differences between a given sequence and the target sequence. 

The output of an SPG job will be a new design file containing the plate mapping information needed to create the new sequences.

## Sequence Design

Let's say you have a set of protein sequences that you'd like to express, but you don't necessairly care about the specific DNA sequence. Choosing the "Minimize Complexity" Design Type on the Sequence Design page, will codon optimize your DNA sequences for those specific protein sequences in such a way to minimize issues when synthesizing the DNA sequences from oligos. You can also use specific codon usage tables, and set a list of restriction sites to avoid if you're going to be cloning these sequences later. Make sure to keep Use Static Seed **unchecked**.

In order to maximize recycling for library designs, you may want to ensure that codon usages are identical across all sequences. This is because having identical codon usage for a given AA will result in identical bases for each position across all sequences, which will then allow for maximum recycling of oligos. In order to do this, reverse translate your protein sequences with Use Static Seed checked. Alternatively, if you already have your sequences in DNA, but want to ensure same codon usage, first traslate, and then reverse translate those results with the Use Static Seed **checked**.

### Sequence Design Parameters

* Input Fasta File(s) - one or more fasta files containing your sequences of interest. These can be either protein or DNA sequences.
* Codon Usage Table - a dropdown allowing selection of common species codon usage tables, including "uniform" by default which gives equal probability to each codon for a given AA.
* Design Type - the type of Sequence Design: Minimize Complexity (takes either DNA or protein and returns DNA), Reverse Translate (protein -> DNA), Translate (DNA -> protein).
* Custom Kazusa Table Id - designate custom codon usage table for a specific species using Kazusa identifiers.
* Sequence Design Name - designate a new name for the sequence design.
* Avoid Restriction Sites - a comma separated list of restriction enzymes to specifically avoid when codon optimizing.
* Use Static Seed - If checked, tells the algorithm to pick the most commonly used codon based on the given codon usage table for a specific amino acid and use that everytime for this design, otherwise it picks a codon with the probability described by the codon usage tables for every choice.

## Fragment Design

**Fragment Design is currently experimental.** Golden Gate assembly, per sequence custom cuts, and the reworked break search are all new and have not yet been proven out against a real order, so treat the results as a starting point and check them before you order.

If you have a larger sequence than the recommended max ~2kb for a one-pot Oligo Design assembly, it's useful to break this into multiple fragments that can then later be assembled into the full sequence. Fragment Design helps with this process in that it automatically finds any complex regions in the sequence that would cause a standard Oligo Design assembly to fail and splits the sequence into fragments that will minimize total assembly complexity. Additionally, you can specify specific cut locations with the Custom Cuts parameter, if you desire fragments be split at certain locations.

The search over break positions is exhaustive rather than sampled, so the same sequence and the same parameters always give you the same fragments. Each break also costs the score of an evenly spaced cut, which means a complexity break has to be worth more than an arbitrary one before the design will make it, rather than the design running up against Max Number of Fragments whenever the size constraints leave room.

Along with the fragments, Fragment Design now writes the plan for putting them back together. Choose how with the Assembly Type parameter.

### Assembly Type

**Oligo Bridge** is the default and does not require any cloning enzymes. Each pair of neighboring fragments is joined by a bridging oligo pair, forward and reverse, spanning the junction: the last N bases of the left fragment followed by the first M bases of the right one. Each half is sized independently so that its melting temperature lands on the Overlap Melting Temp you ask for, within the Melting Temp Tolerance. If no overlap in the allowed size window reaches that temperature, the closest one is used and the design tells you so in the assembly notes rather than silently substituting it.

**Golden Gate** cuts the fragments with a Type IIS enzyme so they carry complementary four base (three for SapI) overhangs and ligate in the correct order in one pot, with no bridging oligos to order. Junction positions are allowed to move by up to Max Cut Shift to land on overhangs that are usable (not a homopolymer run, not their own reverse complement, and not badly AT or GC skewed), and each junction is checked against the others so no two fragments can swap places. **Your target sequence is never modified**: overhangs are read off the sequence as it is, and cuts move rather than bases changing.

Because the target is left alone, any Type IIS site already inside your sequence would be cut by the assembly. Those are reported for domestication in the assembly notes and in the assembly plan, in target coordinates, and the assembly will misfire until you remove them. Choosing the enzyme automatically picks whichever of the four needs the least domestication for your target, which is usually the right answer. The two ends of the target are junctions too, since they are what your backbone has to present, so if either end is not a usable overhang, or repeats an internal junction, that is reported as well. No cut can move them, so fixing that is up to the backbone design.

Assembly notes appear on the results page as well as in the workbook, so you can see cut moves, domestication and backbone requirements without opening the file.

### Fragment Design Outputs

The results zip file contains a fasta with all the sequence fragments, a GenBank per input sequence with its complexity assessment to allow you to interpret how the fragment cut locations were chosen, and one assembly plan workbook per input sequence, named `<Design Name> - <sequence id>.xlsx`.

For Oligo Bridge, the workbook has four tabs: "Fragment Sequence Info" with the fragments themselves, "Oligo Order Form" with the bridging oligos and their plate wells to send to your oligo manufacturer, "Assembly Plan" describing each junction, and "Assembly Worklist" giving the plate map for the bench. For Golden Gate, it has "Fragment Sequence Info" with the flanked parts to order as double stranded fragments, and "Assembly Plan" with the junctions, overhangs and any domestication needed.

### Fragment Design Parameters

* Input Fasta File(s) - one or more fasta files DNA sequences of interest. Specificially sequences larger than ~3kb.
* Min Size - The smallest fragment size in base pairs. 
* Target Size - The target fragment size in base pairs. 
* Max Size - The largest fragment size in base pairs.
* Max Fragments - The maximum number of fragments allowed in this design. This should be logically consistent with the size of the sequence and Max Size.
* Custom Cuts - A file indicating per input sequence user specific locations to make fragment cut locations. This allows users to customize fragment breaks for whatever reason. These will always be chosen over the closest complexity cut location. One sequence per line, as `sequence id: position, position`, for example `seq1: 65, 120`. A bare list of positions with no sequence id applies to every sequence you submitted. Cuts keyed to a sequence that was not submitted are reported back to you instead of being quietly dropped.
* Assembly Type - Oligo Bridge or Golden Gate, described above. The parameters below it change to match.

#### Oligo Bridge Parameters

* Min Bridge Overlap - The smallest number of bases of a fragment a bridging oligo may overlap.
* Max Bridge Overlap - The largest number of bases of a fragment a bridging oligo may overlap.
* Target Bridge Size - The preferred total length of a bridging oligo in base pairs. Half of it is taken from each fragment, clamped into the overlap window above.
* Overlap Melting Temp - The melting temperature in degrees Celsius each half of the bridge is sized to hit.
* Melting Temp Tolerance - How far in degrees Celsius an overlap may sit from the Overlap Melting Temp and still be accepted.

#### Golden Gate Parameters

* Type IIS Enzyme - BsaI, BsmBI, BbsI, SapI, or "Choose automatically", which picks whichever needs the least domestication for your target.
* Max Cut Shift - How far in base pairs a fragment break may move from where the complexity search put it in order to land on a usable overhang.
* Avoid Overhangs - A comma separated list of overhang sequences to keep out of the design, for example ones already used by your backbone or by another assembly sharing the pot.





