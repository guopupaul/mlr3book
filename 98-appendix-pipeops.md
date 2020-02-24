
## Integrated PipeOps {#list-pipeops}

\begin{table}[H]
\centering\begingroup\fontsize{12}{14}\selectfont

\begin{tabular}{l|l|l|l|l|l|l|l}
\hline
key & packages & input.num & output.num & input.train & input.predict & output.train & output.predict\\
\hline
boxcox & bestNormalize & 1 & 1 & Task & Task & Task & Task\\
\hline
branch &  & 1 & -- & Any & Any & Any & Any\\
\hline
chunk &  & 1 & -- & Task & Task & Task & Task\\
\hline
classbalancing &  & 1 & 1 & TaskClassif & TaskClassif & TaskClassif & TaskClassif\\
\hline
classifavg & stats & -- & 1 & -- & PredictionClassif & -- & PredictionClassif\\
\hline
classweights &  & 1 & 1 & TaskClassif & TaskClassif & TaskClassif & TaskClassif\\
\hline
colapply &  & 1 & 1 & Task & Task & Task & Task\\
\hline
collapsefactors &  & 1 & 1 & Task & Task & Task & Task\\
\hline
copy &  & 1 & -- & Any & Any & Any & Any\\
\hline
crankcompose & distr6 & 1 & 1 & -- & PredictionSurv & -- & PredictionSurv\\
\hline
distrcompose & distr6 & 2 & 1 & -- & PredictionSurv & -- & PredictionSurv\\
\hline
encode & stats & 1 & 1 & Task & Task & Task & Task\\
\hline
encodeimpact &  & 1 & 1 & Task & Task & Task & Task\\
\hline
encodelmer & lme4 | nloptr & 1 & 1 & Task & Task & Task & Task\\
\hline
featureunion &  & -- & 1 & Task & Task & Task & Task\\
\hline
filter &  & 1 & 1 & Task & Task & Task & Task\\
\hline
fixfactors &  & 1 & 1 & Task & Task & Task & Task\\
\hline
histbin & graphics & 1 & 1 & Task & Task & Task & Task\\
\hline
ica & fastICA & 1 & 1 & Task & Task & Task & Task\\
\hline
imputehist & graphics & 1 & 1 & Task & Task & Task & Task\\
\hline
imputemean &  & 1 & 1 & Task & Task & Task & Task\\
\hline
imputemedian & stats & 1 & 1 & Task & Task & Task & Task\\
\hline
imputenewlvl &  & 1 & 1 & Task & Task & Task & Task\\
\hline
imputesample &  & 1 & 1 & Task & Task & Task & Task\\
\hline
kernelpca & kernlab & 1 & 1 & Task & Task & Task & Task\\
\hline
learner &  & 1 & 1 & TaskClassif & TaskClassif & -- & PredictionClassif\\
\hline
learner\_cv &  & 1 & 1 & TaskClassif & TaskClassif & TaskClassif & TaskClassif\\
\hline
missind &  & 1 & 1 & Task & Task & Task & Task\\
\hline
modelmatrix & stats & 1 & 1 & Task & Task & Task & Task\\
\hline
mutate &  & 1 & 1 & Task & Task & Task & Task\\
\hline
nop &  & 1 & 1 & Any & Any & Any & Any\\
\hline
pca &  & 1 & 1 & Task & Task & Task & Task\\
\hline
quantilebin & stats & 1 & 1 & Task & Task & Task & Task\\
\hline
regravg &  & -- & 1 & -- & PredictionRegr & -- & PredictionRegr\\
\hline
removeconstants &  & 1 & 1 & Task & Task & Task & Task\\
\hline
scale &  & 1 & 1 & Task & Task & Task & Task\\
\hline
scalemaxabs &  & 1 & 1 & Task & Task & Task & Task\\
\hline
scalerange &  & 1 & 1 & Task & Task & Task & Task\\
\hline
select &  & 1 & 1 & Task & Task & Task & Task\\
\hline
smote & smotefamily & 1 & 1 & Task & Task & Task & Task\\
\hline
spatialsign &  & 1 & 1 & Task & Task & Task & Task\\
\hline
subsample &  & 1 & 1 & Task & Task & Task & Task\\
\hline
unbranch &  & -- & 1 & Any & Any & Any & Any\\
\hline
yeojohnson & bestNormalize & 1 & 1 & Task & Task & Task & Task\\
\hline
\end{tabular}
\endgroup{}
\end{table}
