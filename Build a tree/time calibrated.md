# How to build a time-calibrated tree information collection

## So You Want to Make a Time-Calibrated Phylogenetic Tree
The site is useful [Make a Time-Calibrated Phylogenetic Tree]([url](https://pseudoplocephalus.com/2017/02/15/so-you-want-to-make-a-time-calibrated-phylogenetic-tree/)https://pseudoplocephalus.com/2017/02/15/so-you-want-to-make-a-time-calibrated-phylogenetic-tree/)
so I make some notes here. Thanks to [Victoria.arbour](https://pseudoplocephalus.com/)

### Good references:
#### materials
  [R Cookbook]([url](https://www.oreilly.com/library/view/r-cookbook/9780596809287/)https://www.oreilly.com/library/view/r-cookbook/9780596809287/)
> Learning basics of moving around in R
[Handbook of biological statistics]([url](https://www.biostathandbook.com/)https://www.biostathandbook.com/)

#### R packages
[Paleotree](https://cran.r-project.org/web/packages/paleotree/paleotree.pdf)
> a R package to make a magic time-calibrated tree
> The philosophy behind the package [A Tutorial to cal3 Time-Scaling Using a Real Dataset](https://nemagraptus.blogspot.com/2013/06/a-tutorial-to-cal3-time-scaling-using.html)

### pipeline-before analysis
#### What do you need to make a time tree in paleotree:
- a phylogenetic tree
- know the ages of taxa in your tree
- optional: know the ages of your nodes in your tree

#### Formating phylogenetic tree
[Mesquite](https://www.mesquiteproject.org/)
> export tree using .tre rather than .nex file
> make characters and taxon names simple (e.g. Genus_species)

#### Formation taxon age data
using Excel table

|Taxon names|Maximum age|Minimum age|
|-----------|-----------|-----------|

> There some tips:
> - Taxon names should match an exact order
> - Maximum age and Minimum age should be pretty self-explanatory
> After you have done this
> - save the second copy of the file
> - delete the column of Taxon names (options: delete the headers if you want)
> - save the file as .csv (rangs.csv)

#### Formate node age data
getMRCA
> To figure out the number of internal nodes
getMRCA(tree,c("taxonA","taxonB))
> find the most recent common ancestor of two tips on the tree
getDescendants(tree, node number)
> double-check nodes, the opposite of getMRCA()

### pipeline-analysis
#### using Rstudio

packages you will need:
- paleotree
- phytools

Let's start:
```
tree<- read.tree("tree.tre")
plot(tree) #check to make sure the tree is right
plot(tree[[1]]) #optional: check one of multiple trees
ranges<-read.csv(file="ranges.csv",header=F,row.names=1)
nodes<- read.csv(file="nodes.csv",header=F)

row.names(ranges)<-ranges[,1]
setdiff(ranges[,1],tree$tip.label)# check to make sure the names of the ranges and tree line up correctly
ranges[,1]<-as.character(ranges[,1])#If you do not get a “character 0” response and check setdiff() again
timeData<-ranges=ranges[,2:3] #read that information into timeData
```
if you use timePaleoPhy:
```
timePaleoPhy(tree, timeData, type=”basic”, vartime=NULL, ntrees=1, randres=FALSE, timeres=FALSE,
add.term=FALSE, inc.term.adj=FALSE, dateTreatment=”firstLast”, node.mins=NULL, noisyDrop=TRUE, plot=FALSE)
#If you have node ages, you’ll want node.mins=”nodes”. If you want to plot your tree, you can set it to plot=TRUE or plot=T.

#Make a new time-calibrated tree
tree1<-timePaleoPhy(tree, timeData, type=”basic”, vartime=1, ntrees=100, randres=F, timeres=F, add.term=F, inc.term.adj=F,
dateTreatment=”minMax”, node.mins=”nodes”, noisyDrop=T, plot=T)
write.nexus(tree1, file=”tree1.nex”)#save your tree
```
You can open your tree in Mesquite and figtree.

  
