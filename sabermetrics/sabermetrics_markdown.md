Study on Sabermetrics and Linear Regression
================
Juan Lamilla
09/04/2021

## Setup

``` r
knitr::opts_chunk$set(echo = TRUE)
library(Lahman)
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.6     v dplyr   1.0.4
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(dslabs)
team_stats <- Teams %>% filter(yearID %in% 1961:2001)
```

## Introduction

This is an R Markdown document used to organize and display the code I
have written for my case study in sabermetrics in my class for linear
regression. More specifically, this document highlights relations
between baseball players and their contributions to the team, and how we
can use linear regression to calculate their effectiveness.

``` r
team_stats %>%
  mutate(HR_per_game = HR / G, R_per_game = R / G) %>%
  ggplot(aes(HR_per_game, R_per_game)) + 
  geom_point(alpha = 0.5) + 
  ggtitle("Scatterplot of the Relationship between HRs and Runs") +
  xlab("Home Runs Per Game") + ylab("Runs Per Game")
```

![](sabermetrics_markdown_files/figure-gfm/HR/R-1.png)<!-- -->

``` r
team_stats %>%
  mutate(BB_per_game = BB / G, R_per_game = R / G) %>%
  ggplot(aes(BB_per_game, R_per_game)) + 
  geom_point(alpha = 0.5) + 
  ggtitle("Scatterplot of the Relationship between Bases on Balls and Runs") +
  xlab("Bases on Balls Per Game") + ylab("Runs Per Game")
```

![](sabermetrics_markdown_files/figure-gfm/BB/R-1.png)<!-- -->

``` r
# Calculate correlation coefficient between runs per game and at bats per game
team_stats %>% 
  mutate(AB_per_game = AB / G, R_per_game = R / G) %>% 
  summarize(r = cor(AB_per_game, R_per_game)) %>% pull(r)
```

    ## [1] 0.6580976

We can see a relatively strong direct correlation between the number of
runs and the number of at bats per game. This is also evident by the
linearity of the graph. This makes perfect sense, as a team would
obviously get more runs the more changes (at bats) they receive.

``` r
team_stats %>%
  mutate(win_rate = W / G, E_per_game = E / G) %>%
  ggplot(aes(win_rate, E_per_game)) + 
  geom_point(alpha = 0.5) + 
  ggtitle("Scatterplot of the relationship between Win Rate and Number of Fielding Errors") +
  xlab("Wins Per Game") + ylab("Fielding Errors Per Game")
```

![](sabermetrics_markdown_files/figure-gfm/WR/FE-1.png)<!-- -->

``` r
# Calculate correlation coefficient between win rate and errors per game
team_stats %>% 
  mutate(win_rate = W / G, E_per_game = E / G) %>% 
  summarize(r = cor(win_rate, E_per_game)) %>% pull(r)
```

    ## [1] -0.3396947

We can see a weaker inverse correlation between the win rate and the
number of fielding errors per game.

``` r
team_stats %>%
  mutate(X3B_per_game = X3B / G, X2B_per_game = X2B / G) %>%
  ggplot(aes(X3B_per_game, X2B_per_game)) + 
  geom_point(alpha = 0.5) + 
  ggtitle("Scatterplot of the relationship between Triples and Doubles") +
  xlab("Wins Per Game") + ylab("Fielding Errors Per Game")
```

![](sabermetrics_markdown_files/figure-gfm/X3B/X2B-1.png)<!-- -->

``` r
# Calculate correlation coefficient between triples and doubles
team_stats %>% 
  mutate(X3B_per_game = X3B / G, X2B_per_game = X2B / G) %>% 
  summarize(r = cor(X3B_per_game, X2B_per_game)) %>% pull(r)
```

    ## [1] -0.01157404

We can see a very very weak inverse correlation between the number of
triples and the number of doubles per game. Since this correlation
coefficient is so close to 0, we may as well say there is no correlation
in this relationship. This is also evident by the amount of scatter in
the plot.
