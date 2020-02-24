
## Integrated Filter Methods {#list-filters}

### Standalone filter methods {#fs-filter-list}

\begin{table}[H]
\centering\begingroup\fontsize{12}{14}\selectfont

\begin{tabular}{l|l|l|l|l|l}
\hline
Name & Task & task\_properties & param\_set & Features & Package\\
\hline
carscore & Regr & character(0) & <environment> & numeric & \textbackslash{}em\{care\}\\
\hline
correlation & Regr & character(0) & <environment> & Integer, Numeric & \textbackslash{}em\{stats\}\\
\hline
cmim & Classif \& Regr & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
information\_gain & Classif \& Regr & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{FSelectorRcpp\}\\
\hline
mrmr & Classif \& Regr & character(0) & <environment> & c("numeric", "factor", "integer", "character", "logical") & \textbackslash{}em\{praznik\}\\
\hline
variance & Classif \& Regr & character(0) & <environment> & Integer, Numeric & \textbackslash{}em\{stats\}\\
\hline
anova & Classif & character(0) & <environment> & Integer, Numeric & \textbackslash{}em\{stats\}\\
\hline
auc & Classif & twoclass & <environment> & Integer, Numeric & \textbackslash{}em\{mlr3measures\}\\
\hline
disr & Classif & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
importance & Classif & character(0) & <environment> & c("logical", "integer", "numeric", "factor", "ordered") & \textbackslash{}em\{rpart\}\\
\hline
jmi & Classif & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
jmim & Classif & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
kruskal\_test & Classif & character(0) & <environment> & Integer, Numeric & \textbackslash{}em\{stats\}\\
\hline
mim & Classif & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
njmim & Classif & character(0) & <environment> & Integer, Numeric, Factor, Ordered & \textbackslash{}em\{praznik\}\\
\hline
performance & Classif & character(0) & <environment> & c("logical", "integer", "numeric", "factor", "ordered") & \textbackslash{}em\{rpart\}\\
\hline
\end{tabular}
\endgroup{}
\end{table}

### Algorithms With Embedded Filter Methods {#fs-filter-embedded-list}


```
## [1] "classif.featureless" "classif.rpart"       "regr.featureless"   
## [4] "regr.rpart"
```
