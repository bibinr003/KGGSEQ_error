# KGGSEQ_error
Here i am attaching a solution for KGGseq R package error while installing KGGseq and running using R CMD Rserve 


To help others who struggle with installing and using KGGSeq, especially the R package dependency issue (countreg, maptools), which is not documented clearly in the official KGGSeq pages.

🚨 KGGSeq Common Installation Problem (and How I Solved It)
While running KGGSeq v1.2 (Jan 2022) for rare variant burden testing on human exome data, I ran into an annoying error that many other users likely face. Still, there's no proper fix documented anywhere.

❗ The Problem:
When you run KGGSeq with your-- ped-file,-- vcf-file, and other parameters set correctly, it stops with this error:
install.packages('maptools', dep=TRUE)
install.packages('countreg', dep=TRUE,repos='http://R-Forge.R-project.org')
Error in library(countreg) : there is no package called ‘countreg’
Even after installing these R packages from within R, KGGSeq keeps showing this error every time. This is confusing because you already installed them!

✅ The Root Cause:
KGGSeq internally calls R, but:
It doesn't use your user R installation (the one where you installed the packages),


It uses the system-wide R installation, which doesn't have your libraries!


You can confirm this using:
Rscript -e ".libPaths()"
sudo Rscript -e ".libPaths()"
You’ll likely see different paths.

🛠️ The Fix (Step-by-Step)
✅ Step 1: Install missing R packages system-wide
Start R with sudo and install the required packages into the system library:
sudo /opt/R/4.3.3/bin/R
Then inside R:
install.packages("maptools", dependencies = TRUE)
install.packages("distributions3")
install.packages("countreg", repos = "http://R-Forge.R-project.org")
Note: countreg requires distributions3, which must be installed before it.
After that, test them:
library(maptools)
library(countreg)
✅ Step 2: Make sure .libPaths() includes correct library
If needed, set this in your script before calling KGGSeq:
export R_LIBS="/opt/R/4.3.3/lib/R/library"
Or, hardcode it in your .bashrc if this is your main system R.

📌 Optional: Test inside R
Before running KGGSeq again, test from the terminal:
Rscript -e "library(countreg); library(maptools)"
If there's no error, you're all set ✅

🧠 Lessons Learned
KGGSeq uses system R, not user R.


countreg is hosted on R-Forge, not CRAN.


countreg depends on distributions3, which you must install first.


The error message in KGGSeq doesn't tell you it's using a different R path.



🧬 About My Use Case
I used this setup for analyzing rare variants in a family-based exome sequencing dataset on Ubuntu 22, using:
KGGSeq v1.2


R 4.3.3 installed at /opt/R/4.3.3


Java 8




🆘 If You Still Face Issues
Please drop a comment on this post or raise an issue on my GitHub. Happy to help anyone dealing with these annoying dependency problems.

