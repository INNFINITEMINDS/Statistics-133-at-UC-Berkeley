hw02-aummul\_manasawala
================

### Loading Packages

``` r
library(knitr)
library(dplyr)
library(ggplot2)
```

5 Summary Tables
================

5.1) Total Shots by Player
--------------------------

``` r
total_shots_by_players <- shots_data %>% 
  select(name, shot_made_flag) %>%
  group_by(name) %>%
  summarise(total = sum(shot_made_flag=="made shot"))

total_shots_by_players
```

    ## # A tibble: 5 x 2
    ##   name           total
    ##   <fct>          <int>
    ## 1 Andre Iguodala   192
    ## 2 Graymond Green   245
    ## 3 Kevin Durant     495
    ## 4 Klay Thompson    575
    ## 5 Stephen Curry    584

5.2) Effective Shooting Percentage
----------------------------------

``` r
effective_shooting_perc <- shots_data %>%
  select(name, shot_made_flag) %>%
  group_by(name) %>%
  summarise(total_shots_attempted = sum(shot_made_flag == "made shot" | shot_made_flag=="missed shot")) %>%
  mutate(total_shots_made = total_shots_by_players$total) %>%
  mutate(perc_made = total_shots_made/total_shots_attempted)

effective_shooting_perc
```

    ## # A tibble: 5 x 4
    ##   name           total_shots_attempted total_shots_made perc_made
    ##   <fct>                          <int>            <int>     <dbl>
    ## 1 Andre Iguodala                   371              192     0.518
    ## 2 Graymond Green                   578              245     0.424
    ## 3 Kevin Durant                     915              495     0.541
    ## 4 Klay Thompson                   1220              575     0.471
    ## 5 Stephen Curry                   1250              584     0.467

``` r
total_2_pts_shots_made <- shots_data %>%
  select(name, shot_type, shot_made_flag) %>%
  filter(shot_type == "2PT Field Goal")%>%
  filter(shot_made_flag == "made shot") %>%
  group_by(name) %>%
  summarise(total_shots_made = sum(shot_made_flag == "made shot"))
  

two_pt_effective_shooting_perc <- 
  shots_data %>%
  select(name, shot_made_flag, shot_type) %>%
  filter(shot_type == "2PT Field Goal") %>%
  group_by(name) %>%
  summarise(total_2pt_shots_attempted = sum(shot_made_flag == "made shot" | shot_made_flag=="missed shot")) %>%
  mutate(two_pts_made = total_2_pts_shots_made$total_shots_made) %>%
  mutate(perc_made = two_pts_made/total_2pt_shots_attempted)

two_pt_effective_shooting_perc
```

    ## # A tibble: 5 x 4
    ##   name           total_2pt_shots_attempted two_pts_made perc_made
    ##   <fct>                              <int>        <int>     <dbl>
    ## 1 Andre Iguodala                       210          134     0.638
    ## 2 Graymond Green                       346          171     0.494
    ## 3 Kevin Durant                         643          390     0.607
    ## 4 Klay Thompson                        640          329     0.514
    ## 5 Stephen Curry                        563          304     0.540

``` r
total_3_pts_shots_made <- shots_data %>%
  select(name, shot_type, shot_made_flag) %>%
  filter(shot_type == "3PT Field Goal")%>%
  filter(shot_made_flag == "made shot") %>%
  group_by(name) %>%
  summarise(total_shots_made_three = sum(shot_made_flag == "made shot"))
  

three_pt_effective_shooting_perc <- 
  shots_data %>%
  select(name, shot_made_flag, shot_type) %>%
  filter(shot_type == "3PT Field Goal") %>%
  group_by(name) %>%
  summarise(total_3pt_shots_attempted = sum(shot_made_flag == "made shot" | shot_made_flag=="missed shot")) %>%
  mutate(three_pts_made = total_3_pts_shots_made$total_shots_made_three) %>%
  mutate(perc_made = three_pts_made/total_3pt_shots_attempted)

three_pt_effective_shooting_perc
```

    ## # A tibble: 5 x 4
    ##   name           total_3pt_shots_attempted three_pts_made perc_made
    ##   <fct>                              <int>          <int>     <dbl>
    ## 1 Andre Iguodala                       161             58     0.360
    ## 2 Graymond Green                       232             74     0.319
    ## 3 Kevin Durant                         272            105     0.386
    ## 4 Klay Thompson                        580            246     0.424
    ## 5 Stephen Curry                        687            280     0.408

6. Shooting Distance
====================

6.1) dplyr table
----------------

``` r
shots_attempted_by_dist <- shots_data %>%
  select(shot_distance, shot_made_flag) %>%
  group_by(shot_distance) %>%
  summarise(by_dist_total_shots_attempt = sum(shot_made_flag != "null"))

shots_made_by_dist <- shots_data %>%
  select(shot_distance, shot_made_flag) %>%
  group_by(shot_distance)%>%
  summarise(by_dist_total_shots_made = sum(shot_made_flag == "made shot"))

distance_vs_prop <- shots_data %>%
  select(shot_distance, x) %>%
  group_by(shot_distance) %>%
  summarise(dummy_col <- sum(x)) %>%
  mutate(made_shot_prop = shots_made_by_dist$by_dist_total_shots_made/shots_attempted_by_dist$by_dist_total_shots_attempt) %>%
  select(shot_distance, made_shot_prop)

distance_vs_prop
```

    ## # A tibble: 56 x 2
    ##    shot_distance made_shot_prop
    ##            <int>          <dbl>
    ##  1             0          0.841
    ##  2             1          0.668
    ##  3             2          0.534
    ##  4             3          0.373
    ##  5             4          0.411
    ##  6             5          0.286
    ##  7             6          0.396
    ##  8             7          0.395
    ##  9             8          0.463
    ## 10             9          0.321
    ## # ... with 46 more rows

6.2) ggplot
-----------

``` r
ggplot(data = distance_vs_prop, aes(x = shot_distance, y = made_shot_prop) ) +
  geom_point() +
  labs(x = "Distance of the shot", y = "Proportion of shots made") +
  theme_bw()
```

![](../images/proportions%20of%20shot%20vs%20distance-1.png)

##### What do you observe?

We see that as the distace increases from 0 to 5, there is a sharp decline in the proportion of shots made from around 0.84 to as low as 0.29. After distace 5 thereis a lot of noise in the data but if we see beyond the turbulence there is decrease in proportions of shot made with the increasing distance but the slope is ver lesscompared to the slope observed when distace increased from 0 to 5.

##### Can you confirm that shorter the distance, more efective the shots?

Yes, we can safely generalise that shorter the distance, more effective the shots if e ignore the outliers and the noise.

##### Can you guesstimate a distance threshhold beyond which the chance of making a successful shot is basically null?

The guess I would make looking at the data is that approximately 34-35 is the distance thresh hold beyond which the chance of making a successful shot is basically null.

##### What distances tend to have percentage 50% or more?

Distaces 0 to 2 have percentage greaer than 50%.

7. Total number of shots by minute of occurence
===============================================

Curry's plot

``` r
curry_shots_by_minute <- shots_data %>%
  select(minute, name) %>%
  filter(name== "Stephen Curry") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(curry_shots = sum(name == "Stephen Curry") ) %>%
  mutate(name = "Stephen Curry")




ggplot(data = curry_shots_by_minute, aes(x = minute, y = curry_shots)) +
  geom_point(color = "steelblue2")+
  theme_minimal()+
  labs(y = "total number of shots")+
  geom_path(color = "steelblue1")+
  annotate("rect", xmin=c(0, 24), xmax=c(12, 36), ymin=0,  ymax=Inf, fill="grey 70", alpha=0.2)+
  geom_vline(xintercept = 48, size = 0.2)+
  scale_x_continuous(breaks = seq(from = 0, to = 48, by = 12))+
  ggtitle("Stephen Curry")
```

![](../images/Stephen%20Curry%20shots%20vs%20time-1.png) Dataframe for al plot:

``` r
curry_shots_by_minute <- shots_data %>%
  select(minute, name) %>%
  filter(name== "Stephen Curry") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(shots = sum(name == "Stephen Curry") ) %>%
  mutate(name = "Stephen Curry")


green_shots_by_minute <- shots_data %>%
  select(minute, name) %>%
  filter(name == "Graymond Green") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(shots = sum(name == "Graymond Green") ) %>%
  mutate(name= "Graymond Green")


thompson_shots_by_minute <- shots_data %>%
  select(minute, name) %>%
  filter(name== "Klay Thompson") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(shots = sum(name == "Klay Thompson") ) %>%
  mutate(name = "Klay Thompson")

durant_shots_by_minute <- shots_data %>%
  select(minute, name) %>%
  filter(name== "Kevin Durant") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(shots = sum(name == "Kevin Durant") ) %>%
  mutate(name = "Kevin Durant")

iguodala_dummy <- shots_data %>%
  select(minute, name) %>%
  filter(name== "Andre Iguodala") %>%
  arrange(minute) %>%
  group_by(minute) %>%
  summarise(shots1 = sum(name == "Andre Iguodala") ) 


shots <- c(rep(0, 48))
for (i in 1:48){
  filter_dummy <- filter(iguodala_dummy, minute==i)
     if (nrow(filter_dummy)!=0) {shots[i]=filter_dummy$shots1} else {shots[i]=0}
}
name = c(rep("Andre Iguodala", 48))
igudala_shots_by_minute <- data.frame(minute = c(1:48), shots, name)



all <- rbind( igudala_shots_by_minute, green_shots_by_minute, durant_shots_by_minute, thompson_shots_by_minute, curry_shots_by_minute)
```

Plot for all the five players:

``` r
ggplot(data = all, aes(x = minute, y = shots)) +
  geom_point(color = "steelblue2")+
  theme_minimal()+
  labs(y = "total number of shots")+
  geom_path(color = "steelblue1")+
  annotate("rect", xmin=c(0, 24), xmax=c(12, 36), ymin=0,  ymax=Inf, fill="grey 70", alpha=0.2)+
  geom_vline(xintercept = 48, size = 0.2)+
  scale_x_continuous(limits = c(1, 48), breaks = c(1, 12, 24, 36, 48))+
  ggtitle("Total number of shots(by minute occurence")+
  facet_wrap(~name)
```

    ## Warning: Removed 5 rows containing missing values (geom_rect).

![](../images/all%20players%20shot%20vs%20time-1.png)
