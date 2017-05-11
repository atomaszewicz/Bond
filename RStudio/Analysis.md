# Analyzing the James Bond Film Franchise

We open RStudio, and begin by changing our directory to that which contains the James Bond data.

```R
setwd("C:/Users/Alex/Documents/Data/Movies/Franchise/Bond")
```

Then we load a package to handle Excel files (since I saved my cleaned-up Exif CSV as an Excel ".xlsx" file) and our favorite graph creating package.
```R
install.packages("xlsx") 
install.packages("ggplot2")
require("xlsx")
require("ggplot2")
```

Then we data frames from the relevant pages of 
