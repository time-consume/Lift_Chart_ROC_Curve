Lift\_Chart\_ROC\_Curve
================

## GitHub Documents

R Packages regarding binary lift chart hasn’t function well for me. I’m
writing one lift chart and ROC curve for my own convience

## Lift Chart

Read the example dataset

``` r
dat<-read.csv("example.csv")
head(dat,5)
```

    ##   Observation Propensity Actual.Class
    ## 1           1  0.1143480            0
    ## 2           2  0.7776530            1
    ## 3           3  0.5555499            0
    ## 4           4  0.6940474            1
    ## 5           5  0.3227634            1

sort by the order

``` r
dat<-dat[order(-dat$Propensity),]
for (i in 1:nrow(dat)) {
  dat$accum[i]<-sum(dat$Actual.Class[1:i])  
}
positive<-dat[dat$Actual.Class==1,]
negative<-dat[dat$Actual.Class==0,]
perfect<-c(1:nrow(positive),rep(nrow(positive),nrow(negative)))
worst<-c(rep(0,nrow(negative)),1:nrow(positive))
plt<-data.frame(dat$accum,perfect,worst)
#lift chart
G1<-ggplot(data = plt,aes(y=dat$accum,x=1:1000))+geom_point(shape = ".",colour = 'black')+geom_point(aes(y=plt$perfect,x=1:1000), shape = ".",colour = 'red')+geom_point(aes(y=plt$worst,x=1:1000), shape = ".",colour = 'green')+labs(x="number of observations",y="number of positive occurance")
#need to know the exact loss function
loss<-(perfect-plt$dat.accum)/perfect
mean((perfect-plt$dat.accum)/perfect)
```

    ## [1] 0.1447107

``` r
G1+ggtitle("lost percentage is ",round(mean((perfect-plt$dat.accum)/perfect),3))
```

![](Lift_Chart_ROC_Curve_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

## ROC curve

Write my own ROC function to calculate TPR and FRR for certain cutoff
value

Loop cutoff value to get the whole ROC curve

``` r
#return ROC
ROC<-data.frame(cutoff=0,TPR=0,FRR=0)
#
for (i in seq(0.01,0.99,0.01)) {
  ROC<-rbind(ROC,myfunc(i))
}
ROC<-rbind(ROC,data.frame(cutoff=1,TPR=1,FRR=1))
head(ROC,5)
```

    ##   cutoff       TPR       FRR
    ## 1   0.00 0.0000000 0.0000000
    ## 2   0.01 0.9918699 0.9842520
    ## 3   0.02 0.9878049 0.9665354
    ## 4   0.03 0.9878049 0.9389764
    ## 5   0.04 0.9857724 0.9232283

``` r
G2<-ggplot(data=ROC,aes(x=ROC$FRR,y=ROC$TPR))
G2+geom_line()+ggtitle("AOC is",round(mean(ROC$TPR),3))
```

![](Lift_Chart_ROC_Curve_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
