[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4316762.svg)](https://doi.org/10.5281/zenodo.4316762)
# GranarMecha

To run GRANAR and MECHA as one in the cloud, go to: https://mybinder.org/v2/gh/HeymansAdrien/GranarMecha/main
Select the Run_MECHANAR.ipynb and run the example.

The first time you launch this example, it might take a wild to load as the R library has to be install on the cloud environment.

To run this example localy, on your computer, you can:
- Download this GitHub repository
- Get Python 3, if you use [Anaconda](https://www.anaconda.com/products/individual) the following step would be easier.

then create an environment for python and add those dependencies:
  - numpy
  - scipy
  - matplotlib
  - lxml
  - pip
  
  and use pip to install
  
  - networkx==1.9.1
  
with anaconda:
- environment
- create
- (play button)  open terminal
```
conda install numpy
conda install scipy
conda install matplotlib
conda install lxml
conda install pip

pip install networkx==1.9.1
```

Those past step should allow you to run [MECHA](https://mecharoot.github.io/).

The next ones will allow you to run [GRANAR](https://granar.github.io/) and call MECHA in the R script.

Using R in [RStudio](https://rstudio.com/products/rstudio/) you can run this code:

make sure you have installed those library:
- tidyverse
- plyr
- deldir
- sp
- xml2
- viridis

```{r}
setwd("~/GitHub/GranarMecha/")
# Loading R library
library(tidyverse)
library(plyr)
library(deldir)
library(sp)
library(xml2)
library(viridis)

source("./GRANAR/R/granar.R")

# Load one parameter file for GRANAR
params <- read_param_xml("GRANAR/model_params/Zea_mays_1_Heymans_2019.xml")

# # # # # # # # # # # # # # #
#  To change paramter value   #
# # # # # # # # # # # # # # #

# Xylem size (diameter)
params$value[params$type == "max_size" & params$name == "xylem"] <- 0.043

# aerenchyma proportion
params$value[params$type == "proportion" & params$name == "aerenchyma"] <- 0.2
# number of lacuna
params$value[params$type == "n_files" & params$name == "aerenchyma"] <- 15

# Generate the anatomy
sim <- create_anatomy(parameters = params, verbatim=T, paraview = F)
# sim is a list
# sim$nodes is a data frame with most of the information
# sim$output is a data frame with summarize anatomical features

# To visualize the anatomy and the scenario that are going to be tested.
# you can use the plot_anatomy function.
plot_anatomy(sim, col = "segment", apo_bar = 0)
plot_anatomy(sim, col = "segment", apo_bar = 1)
plot_anatomy(sim, col = "segment", apo_bar = 3)

# To visualize cell type:
plot_anatomy(sim) # default type
write_anatomy_xml(sim, "MECHA/cellsetdata/current_root.xml")
aer_in_geom_xml(sim, "MECHA/Projects/granar/in/Maize_Geometry.xml")

# fill the dots with the appropriate path to your python in your environment
# here is the example if your using anaconda
system(" ........../AppData/Local/Continuum/anaconda3/envs/MECHA/python.exe MECHA/MECHAv4_light.py")

# Upload MECHA output 
Flux1 <- ApoSymp("MECHA/Projects/granar/out/Root/Project_Test/baseline/Macro_prop_1,0.txt")
Flux2 <- ApoSymp("MECHA/Projects/granar/out/Root/Project_Test/baseline/Macro_prop_2,1.txt")
Flux3 <- ApoSymp("MECHA/Projects/granar/out/Root/Project_Test/baseline/Macro_prop_4,2.txt")

# Plot Apoplastic and Symplastic fluxes
plot_water_flux(Flux1, apobar = 1)
plot_water_flux(Flux2, apobar = 2)
plot_water_flux(Flux3, apobar = 3)

```






