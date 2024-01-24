Markov-Decision-Processes-MDPs
================
Joshua Edefo
2024-01-24

Brief description of the Markov Decision Processes (MDPs) Treatment
planining for those who are sufferring from depression

MDP means when an agent interacts with an environment, it takes actions
that affects states of the environment. there is a limited feedback
which is the reward signal that tells how well agent is perfoemorming.
the goal is to improve behaviour given only this limitef=d feedback

Applicationin the heakthcare sysstem, the agent could be the health
professional, environment the patients, states means the various disease
stages, reward may be the benefit

So lets’ narrow it down to people suffering from depression

States are 1) depressed, 2) borderline and 3)depression free

Model will be that the patient will transit in the order of depressed -
borderline - depression free with a probability of relapse into any
state

Actions are the different plans that affect the disease states, these
are 1)antidepressant, 2) psychotherapy and 3) combination of
antidepressant and psychotherapy

Rewards will based on quality of life(qol) each action will give, this
will be 1) low qol 2) medium qol 3) high qol

Load library

``` r
library(MDPtoolbox)
```

    ## Warning: package 'MDPtoolbox' was built under R version 4.3.2

    ## Warning: package 'linprog' was built under R version 4.3.2

    ## Warning: package 'lpSolve' was built under R version 4.3.2

Lets begin

``` r
# Consider first action which is using antidepressant

# name of states

state_names<-c("depressed", "borderline", "depression_free")

# probability matrix as action after states

Antidepressant<-matrix(c(0.1, 0.3, 0.6, 
                         0.3, 0.1, 0.6, 
                         0.1, 0.1, 0.8),
            nrow = 3, ncol =3, byrow=TRUE, 
            dimnames = list(from = state_names, to = state_names ))



# scecond action psychotherapy
state_names<-c("depressed", "borderline", "depression_free")

# probability matrix as action after states

state_names<-c("depressed", "borderline", "depression_free")

Psychotherapy<-matrix(c(0.2, 0.3, 0.5, 
                        0.4, 0.2, 0.4, 
                        0.2, 0.2, 0.6),
                       nrow = 3, ncol =3, byrow=TRUE, 
                       dimnames = list(from = state_names, to = state_names ))


# Third action which is combination of antisepressant and psychotherapy
state_names<-c("depressed", "borderline", "depression_free")

Antide_and_Psycho<-matrix(c(0.05, 0.3, 0.65, 
                          0.3, 0.1, 0.6, 
                          0.06, 0.1, 0.84),
                      nrow = 3, ncol =3, byrow=TRUE, 
                      dimnames = list(from = state_names, to = state_names ))


# Aggregate previous matrices to create transition  probabilities into list Aggregate_mat
Aggregate_mat <-list(Antidepressant = Antidepressant, 
                 Psychotherapy = Psychotherapy,
                 Antide_and_Psycho = Antide_and_Psycho)


# create matrix with rewards

rewards_names<-c("low quality", "medium quality", "high quality")

Reward_mat<-matrix(c(1,  1,  1, 
                     5,  5,  5, 
                     10, 10, 10),
                      nrow = 3, ncol =3, byrow=TRUE, 
                      dimnames = list(from = rewards_names, to = rewards_names ))
# this mean if enter from depressed state you will have low quality of life which reward is 1
# enter from borderline line you  will have a reward of 5 which is medium quality of life
# enter from depresion free state you will get a reward of 10 which is high qol

# Check if this provides a well- define MDP, empty string meand ok

mdp_check(Aggregate_mat,Reward_mat)
```

    ## [1] ""

Run policy iteration with discount 0.7

Discount factor in MDP is a paramer that determines how much the agent
values the future rewards, its ranges from 0 to 1, a low values means
care more about current while a high value suggests the agent care much
about long-term consequences

``` r
Treament_plan <- mdp_policy_iteration(P = Aggregate_mat,
                                      R = Reward_mat,
                                      discount = 0.7)
# Treatment policy
# Display optimal policy

Treament_plan $policy
```

    ## [1] 3 1 3

``` r
#[1] 3 1 3

names(Aggregate_mat)[Treament_plan $policy]
```

    ## [1] "Antide_and_Psycho" "Antidepressant"    "Antide_and_Psycho"

``` r
#[1] "Antide_and_Psycho" "Antidepressant"    "Antide_and_Psycho"

# if we begin the treatment of patient at depressed state the optimal therapy 
# is combination of antidepressant and psychotherapy but if we begin the treatment at borderline state 
# the optimal treatment is antidepressant and if we begin at depression free the the optimal therapy is 
# combination therapy

# We can also display the value  function
Treament_plan$V
```

    ## [1] 20.53629 23.74226 30.39923

``` r
## [1] 20.53629 23.74226 30.39923
# it shows us the value of following this policy as we move from state to state
```

Session information

``` r
sessionInfo()
```

    ## R version 4.3.1 (2023-06-16 ucrt)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 11 x64 (build 22631)
    ## 
    ## Matrix products: default
    ## 
    ## 
    ## locale:
    ## [1] LC_COLLATE=English_United Kingdom.utf8 
    ## [2] LC_CTYPE=English_United Kingdom.utf8   
    ## [3] LC_MONETARY=English_United Kingdom.utf8
    ## [4] LC_NUMERIC=C                           
    ## [5] LC_TIME=English_United Kingdom.utf8    
    ## 
    ## time zone: Europe/London
    ## tzcode source: internal
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ## [1] MDPtoolbox_4.0.3 linprog_0.9-4    lpSolve_5.6.20   Matrix_1.6-1.1  
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] digest_0.6.33     fastmap_1.1.1     xfun_0.40         lattice_0.21-8   
    ##  [5] knitr_1.44        htmltools_0.5.6   rmarkdown_2.25    cli_3.6.1        
    ##  [9] grid_4.3.1        compiler_4.3.1    rstudioapi_0.15.0 tools_4.3.1      
    ## [13] evaluate_0.21     yaml_2.3.7        rlang_1.1.1
