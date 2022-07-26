## MSc Bioinformatics
### QA and Assembly

<!-- We will need a Flash presentation from you which should be a maximum of 5 minutes with a brief introduction and synopsis of your career plus your work/involvement in the areas this workshop is covering and a maximum of 4 slides - this needs to be with us by latest 8 September (apologies for the short notice). -->

<small style="float: right;"><a href="//bmpvieira.com/assembly14" target="_blank">bmpvieira.com/assembly14</a></small>
<br>

<img style="width: 30%; float: right; padding-right: 1em;" alt="bmpvieira" src="img/bmpvieira.png" />

[Bruno Vieira](http://bmpvieira.com) | <i class="fa fa-twitter"></i> <a href="//twitter.com/bmpvieira" target="_blank">@bmpvieira</a>

Phd Student @ <a href="http://www.qmul.ac.uk" target="_blank"><img style="width: 25%; float: right; padding-right: 1em;" alt="QMUL" src="img/Queen_Mary,_University_of_London_logo.svg" /></a>

Bioinformatics and Population Genomics

<span style="font-size:0.8em;">
Supervisor:  
Yannick Wurm | <i class="fa fa-twitter"></i>  <a href="//twitter.com/yannick__" target="_blank">@yannick__</a>
</span><br>
<small>
<!-- Before:
<br>
<a href="http://www.ciencias.ulisboa.pt" target="_blank"><img style="max-height: 7%;  float: left;" alt="FCUL" src="img/fcul.png" /></a>
<a href="http://cobig2.com" target="_blank"><img style="max-height: 7%; float: left;" alt="CoBiG2" src="img/cobig2.png" /></a>
<a href="http://eseb2013.com" target="_blank"><img style="max-height: 7%; float: left;" alt="eseb2013" src="img/eseb2013.png" /></a>
<a href="https://geekli.st" target="_blank"><img style="max-height: 7%; float: left;" alt="geeklist" src="img/geeklist.png" />
<a href="http://www.bbsrc.ac.uk" target="_blank"><img style="max-height: 7%; float: left;" alt="bbsrc" src="img/bbsrc.png" /> -->

</small>
<div style="position:absolute; top: 82%; font-size:.35em;">
© 2014 <a href="http://bmpvieira.com" target="_blank">Bruno Vieira</a> <a href="http://creativecommons.org/licenses/by/4.0/deed.en_US" target="_blank">CC-BY 4.0</a>
</div>


---

### Download data

[bit.ly/ant-reads](https://bit.ly/ant-reads)

### Useful books

* [The Command Line Crash Course](http://cli.learncodethehardway.org/book/)
* [Bioinformatics Data Skills](http://shop.oreilly.com/product/0636920030157.do)

### Papers
[*De novo* genome assembly: what every biologist should know](http://doi.org/10.1038/nmeth.1935)

[Assemblathon 2: evaluating de novo methods of genome assembly[...]](http://doi.org/10.1186/2047-217X-2-10)

---

## Genome Assembly

<center>
<a href="http://doi.org/10.1038/nmeth.1935" target="__blank"><img src="img/assembly-paper.png" /></a>
</center>


---

<img src="img/assembly.png" />
<small>
[Chen 2011](http://bioinformatics.udel.edu/sites/bioinformatics.udel.edu/files/pdfs/AnnotationWrkshp/GenomeSequenceAssembly-Chen.pdf)
</small>

---

### Types

**Algoritms**

* Overlap Layout Consensus
* De Bruijn


**Strategies**

* De Novo
* Reference guided

<br>
<small>
[Assembly paradigms](http://www.nature.com/nrg/journal/v14/n3/box/nrg3367_BX2.html)
</small>

---

### Overlap/Layout/Consensus

<img style="width: 100%;" src="img/olc.png" />

---

### Overlap/Layout/Consensus

<img style="width: 70%;" src="img/olc.png" />
<ul>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">A node corresponds to a read, an edge denotes an overlap between two reads.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">The overlap graph is used to compute a layout of reads and consensus sequence of contigs by pair-wise sequence alignment.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">Good for sequences with limited number of reads but significant overlap. Computational intensive for short reads (short and high error rate).</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">Example assemblers: Celera Assembler, Arachne, CAP and PCAP</li>
</ul>

<small>
[Chen 2011](http://bioinformatics.udel.edu/sites/bioinformatics.udel.edu/files/pdfs/AnnotationWrkshp/GenomeSequenceAssembly-Chen.pdf)
</small>

---

### de Brujin

<img style="width: 100%;" src="img/brujin.png" />

---

### de Brujin

<img style="width: 70%;" src="img/brujin.png" />

<ul>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">No need for all against all overlap discovery.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">Break reads into smaller sequences of DNA (K-mers, K denotes the length in bases of these sequences).</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">Captures overlaps of length K-1 between these K-mers.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">More sensitive to repeats and sequencing errors.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">By construction, the graph contains a path corresponding to the original sequence.</li>
<li style="font-size: .5em; line-height: 1.2;" class="fragment fade-in">Example assemblers: Euler, Velvet, ABySS, AllPaths, SOAPdenovo, CLC Bio</li>
</ul>

<small>
[Chen 2011](http://bioinformatics.udel.edu/sites/bioinformatics.udel.edu/files/pdfs/AnnotationWrkshp/GenomeSequenceAssembly-Chen.pdf)
</small>

---

<img src="img/brujin-graph-complex.png" />
<small>
[Schatz 2012](http://schatzlab.cshl.edu/presentations/2012-09-27.BtG.Assembly%20Primer.pdf)
</small>

---

<img src="img/brujin-graph-simple.png" />
<small>
[Schatz 2012](http://schatzlab.cshl.edu/presentations/2012-09-27.BtG.Assembly%20Primer.pdf)
</small>

---

### Too many assemblers
[seqanswers.com/wiki/De-novo_assembly](http://seqanswers.com/wiki/De-novo_assembly)

<br>
A5, ABySS, ALLPATHS, CABOG, CLCbio, Contrail, Curtain, DecGPU, Forge, Geneious, GenoMiner, IDBA, Lasergene, MIRA, Newbler, PE-Assembler, QSRA, Ray, SeqMan NGen, SeqPrep, Sequencher, SHARCGS, SHORTY, SHRAP, SOAPdenovo, SR-ASM, SuccinctAssembly, SUTTA, Taipan, VCAKE, Velvet

---

### Benchmarking

* [Assemblathon 1](http://assemblathon.org/assemblathon1)
* [Assemblathon 2](http://assemblathon.org/assemblathon2)
* [GAGE](http://gage.cbcb.umd.edu)
* [Nucleotid.es](http://nucleotid.es)

<br>
[Why we need the assemblathon](http://assemblathon.org/post/44431925986/why-we-need-the-assemblathon)

---

### Assembly quality assessment

<ul>
  <li>
    Accuracy or “Correctness”
    <ul class="fragment fade-in">
      <li class="fragment fade-in">Base accuracy – the frequency of calling the correct nucleotide at a given position in the assembly.</li>
      <li class="fragment fade-in">Mis-assembly rate – the frequency of rearrangements, significant insertions, deletions and inversions.</li>
    </ul>
  </li>
</ul>


---

### Assembly quality assessment

<ul>
<li>
  Continuity
  <ul>
    <li class="fragment fade-in">Lengths distribution of contigs/scaffolds.</li>
    <li class="fragment fade-in">Average length, minimum and maximum lengths, combined total lengths.</li>
    <li class="fragment fade-in">N50 captures how much of the assembly is covered by relatively large contigs.</li>
  </ul>
</li>
</ul>

---

<img src="img/n50.png" />

---

### Assembly quality assessment

* N50
* NG50

<br>
### [N50 must die?](http://korflab.ucdavis.edu/datasets/Assemblathon/Assemblathon1/assemblathon_talk.pdf)


---

### Assembly quality assessment

<ul>
  <li class="fragment fade-in">**Fragment analysis** - Count how many randomly chosen fragments from species A genome can be found in assembly</li>
  <li class="fragment fade-in">**Repeat analysis** - Choose fragments that either overlap or don’t overlap a known repeat</li>
  <li class="fragment fade-in">**Gene finding** - How many genes are present in each assembly? ([CEGMA](http://korflab.ucdavis.edu/datasets/cegma/#SCT2))</li>
</ul>

---

### Assembly quality assessment

<ul>
  <li class="fragment fade-in">**Contamination** - “all libraries will contain some bacterial contamination”</li>
  <li class="fragment fade-in">**Mauve analysis** - Uses whole genome alignment to reveal</li>
  <li class="fragment fade-in">**BWA analysis** - Align contigs to genome</li>
  <li class="fragment fade-in">**[Optical Maps](http://en.wikipedia.org/wiki/Optical_mapping) / [Irys](http://www.bionanogenomics.com/technology/why-genome-mapping/)**</li>

</ul>



---

<img src="img/ingredients.png" />

---

### [FastQC](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

<img style="width: 70%;" src="img/fastqc.png" />

[FastQC Documentation](http://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules)

---

<img src="img/fastx-quals.png" />

---

<img src="img/trim.png" />

---

### [Diginorm](http://arxiv.org/abs/1203.4802)

>"(...)systematizes coverage in shotgun sequencing data sets, thereby decreasing sampling variation, discarding redundant data, and removing the majority of errors."

---

### [Diginorm](http://arxiv.org/abs/1203.4802)

>"(...)reduces the size of shotgun data sets and decreases the memory and time requirements for de novo sequence assembly, all without significantly impacting content of the generated contigs."

<span class="fragment fade-in">Magic?</span> <span class="fragment fade-in">No, [Bloom filters](http://en.wikipedia.org/wiki/Bloom_filter)</span>

---

### [Diginorm](http://arxiv.org/abs/1203.4802)

<img style="width: 50%;" src="img/raw-coverage.png" /><img style="width: 50%;" src="img/norm-coverage.png" />



[What is digital normalization, anyway?](http://ivory.idyll.org/blog/what-is-diginorm.html)

[Why you shouldn't use digital normalization](http://ivory.idyll.org/blog/why-you-shouldnt-use-diginorm.html)

---

### Fast<span style="color: green;">a</span>

<img src="img/fasta.png" />

---

### Fast<span style="color: green;">q</span>

<img src="img/fastq.png" />

---
### Fast<span style="color: green;">q</span>

<img src="img/fastq-id.png" />

---

<img src="img/quals.png" />

---

### Interleaved format

<img src="img/fastq-interleaved.png" />


---

### Practical

[bmpvieira.com/assembly14-practical](http://bmpvieira.com/assembly14-practical)

---
