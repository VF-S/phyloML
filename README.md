# An OCaml Phylogenetic Tree Library for Phylo-Lib-Web

### Spring 2020, CS 3110 Final Project
### by Vaishnavi Gupta, Shiyuan Huang, Felix Hohne

___ 
### What are phylogenetic trees?

Scientists often wish to infer evolutionary history between different organisms.
In order to determine the closeness of species such as birds or fish, 
historically the similarity in physical characteristics was analyzed. 
With the advent of modern computers and DNA analysis, the field has moved 
towards using DNA. 

Two species with more similar DNA are assumed to be more closely related. By 
analyzing these similarities and differences in the DNA, we can generate a 
hypothetical evolutionary tree, called a phylogenetic tree, that estimates, to 
the best of our ability, what the actual historical evolutionary tree would have 
looked like. 


___ 
### Summary of Library Functionality

In this library. we use OCaml's Streams and Buffers to efficiently parse DNA sequences such as H1N1 or the X chromosome of a fruit fly, use the dynamic programming algorithm of Needleman and Wunsch to find the global optimum pairwise alignment of DNA sequences, construct distance matrices for different DNA sequences, and use the Unweighted Pair Group Method with Arithmetic mean (UPGMA) algorithm to construct parsimonious, rooted phylogenetic trees. 

We also provide additional functionality to parse already constructed phylogenetic trees from PhyloXML into our library's custom n-ary treeand visualize them in ASCII format, display phylogenetic trees in ASCII, and output the constructed phylogenetic trees in the form of XML-format files to support compatibility with other biocomputational programs such as BioPython.

___ 

### Installation Instructions 

1. Ensure that a modern version of OCaml is installed. This library was written using OCaml 4.09.0 and has no additional dependencies, except for bisect to test code coverage. 
2. Clone this repository 
3. In the folder containing this respitory, run `make` in the command line
4. The REPL for OCaml, utop will compile the required modules and the functionality of this library will then be available. 
5. To compile the documentation, run `make docs`. 
6. To run tests, run `make test`.
___ 
### Simple Examples 


**Running the XML Parser:** Parse an amphibian species phyloXML file found in the Phylo folder 
   called frog.xml into our custom built n-ary tree, then pretty-print it using ASCII art.  
   
  ```OCaml
  let phylo1 = Phylo_parser.from_phylo "PhyloXML/frog.xml"
  Tree.print_tree phylo1.tree
   ```
| PhyloXML Input | N-ary Tree output |
| ----------- | ----------- |
| <img width="423" alt="Screen Shot 2020-06-14 at 8 57 31 PM" src="https://user-images.githubusercontent.com/58995473/84601604-b772f500-ae81-11ea-8721-a8c19faea1fe.png"> | <img width="460" alt="Screen Shot 2020-06-14 at 9 06 48 PM" src="https://user-images.githubusercontent.com/58995473/84601803-1d13b100-ae83-11ea-8d98-2c1272cd2af8.png">

<br/>

**Pairwise Alignment using the Needleman-Wunsch algorithm:** The Needleman-Wunsch algorithm is a globally optimal algorithm for finding the pairwise alignment of two strings using dynamic programming. Here we implement it to find an optimal alignment of two pairs of DNA sequences. We then pretty-print the final alignment. 

```OCaml
let pdna1 = Dna.from_fasta "FASTA/install_dna1.fasta";; 
let pdna2 = Dna.from_fasta "FASTA/install_dna2.fasta";; 
let paligned = Pairwise.align_pair pdna1 pdna2 1 (-1) (-1) |> fst;;
Pairwise.print_alignment paligned.(0) paligned.(1);;
```
<p align="center">
   <img width="572" alt="Screen Shot 2020-06-17 at 12 39 24 PM" src="https://user-images.githubusercontent.com/58995473/84888544-b74c4280-b097-11ea-8b2c-1de35df011cb.png">
<p/>

<br/>

**Construct a phylogenetic tree from DNA .FASTA Files:** We construct a phylogenetic tree based on the PB-2 gene for the H1N1, H5N1, and H3N2 viruses, focusing on the PB-2 gene. The source for this data is the National Center for Biotechnology Information (NCBI). The resulting tree shows that H1N1 and H3N2 are more closely related as they are swine flue, while H5N1 is an avian flu.

```OCaml
let d1 = Dna.from_fasta "viruses/h5n1.fasta"
let d2 = Dna.from_fasta "viruses/h1n1.fasta"
let d3 = Dna.from_fasta "viruses/h7n7.fasta"
let d4 = Dna.from_fasta "viruses/h7n9.fasta"
let d5 = Dna.from_fasta "viruses/h3n2.fasta"
let mat = Distance.dist_dna [| d1; d2; d3; d4; d5 |] 1 (-1) (-1)
let virus_names = [|"H5N1"; "H1N1"; "H7N7"; "H7N9"; "H3N2"|]
(Phylo_algo.upgma mat virus_names) |> Tree.print_tree
```

| Sample DNA Input | Constructed Tree|
| ----------- | ----------- |
| <img width="402" alt="Screen Shot 2020-06-17 at 12 51 17 PM" src="https://user-images.githubusercontent.com/58995473/84889521-486fe900-b099-11ea-802c-a8744b41db98.png"> | <img width="528" alt="Screen Shot 2020-06-17 at 1 02 06 PM" src="https://user-images.githubusercontent.com/58995473/84890505-ca144680-b09a-11ea-8bcc-b5b7c4b28491.png">

<br/>

**Save constructed phylogenetic trees as XML files:** Constructed phylogenetic trees can also be saved as XML files in phyloXML format. Here we construct a small phylogenetic tree and output the resulting tree in phyloXML format in `example_file.xml`.

```OCaml
let d1 = Dna.from_fasta "viruses/h5n1.fasta"
let d2 = Dna.from_fasta "viruses/h1n1.fasta"
let d3 = Dna.from_fasta "viruses/h3n2.fasta"
let mat = Distance.dist_dna [| d1; d2; d3 |] 1 (-1) (-1)
let tree = Phylo_algo.upgma mat [|"H5N1"; "H1N1"; "H3N2"|]
Phylo_printer.print_tree_xml tree "example_file.xml"
```

<p align="center">
   <img width="400" alt="PhyloXML output in example_file.xml" src="https://user-images.githubusercontent.com/19438967/85913359-23e6e000-b802-11ea-817b-dd3c11706f5a.png">
<p/>

For more examples and demos, see examples.txt. 
___ 


### Division of Work
- Parsing DNA Files: Felix Hohne
- Lexing PhyloXML Files: trio-programmed 
- N-ary trees: Vaishnavi Gupta
- Tree Pretty Printing: Vaishnavi Gupta
- Phylo_Parser to parse PhyloXML Files: Shiyuan Huang the rest was trio-programmed 
- Construction of Distance Matrices for UPGMA: trio-programmed 
- Pairwise alignment using Dynamic Programming : trio-programmed
- UPGMA: trio-programmed
- Bayesian Inference: Vaishnavi Gupta
- README: Felix Hohne
- Saving Constructed Trees as XML Files: Shiyuan Huang
