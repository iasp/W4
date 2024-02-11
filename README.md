# WK4 JHU Data Project

1. Downloaded the zip file and used read.table() to load data into R:
   Test Set (X, Y and Subject)
   Train Set (X, Y and Subject)
   Variable and Activity Labels

2. Created VariableName vector and used it to change column names for both Test & Train Sets using names()
3. Binded X & Y for both sets using dplyr's bind_cols()
4. Used grep() to filter mean and standard deviation variables
5. Subsetted X & Y sets according to the mean and standard deviation variables
6. Binded X & Y sets to its respective subjects and renamed column names
7. Checked if I had 30 unique subjects using NROW and unique
8. Used dplyr's group_by() and summarise(across(everything(), list(mean)) on each set
9. Merged the two sets and arranged it by subject
10. Assigned each of the 6 activities to its corresponding activity
11. Changed variable names using gsub 
