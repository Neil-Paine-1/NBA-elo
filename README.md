# NBA-elo
Archive of NBA Elo data (updated from FiveThirtyEight version)

https://fivethirtyeight.com/features/how-we-calculate-nba-elo-ratings/

## How Estimated RAPTOR works

Players are ranked at [my Substack](https://neilpaine.substack.com/p/nba-estimated-raptor-player-ratings) using Estimated RAPTOR, a simplified version of the [RAPTOR ratings](https://projects.fivethirtyeight.com/nba-player-ratings/) formerly found at FiveThirtyEight.com. Players are rated on both offense and defense, based on an estimate of how much they contributed to their team's performance on each side of the ball. The ratings represent the number of points per 100 possessions a player added to (or subtracted from) his team relative to a league-average player.

To derive the ratings, linear regressions were used to assign weights to each boxscore action (per 100 team possessions) based on what predicts full RAPTOR ratings. There are also weights applied to a player's on-court effect on his team's performance on either offense or defense, with the plus/minus component carrying more weight on the defensive side (because the boxscore is less effective at measuring defensive performance than it is on offense). The weights from the regression are as follows:

|  Category   | Offensive Weight | Defensive Weight |
|-------------|------------------|------------------|
| (Intercept) |         -3.88704 |        -3.079144 |
| MPG         |         0.026112 |         0.033637 |
| `PTS/100`   |         0.662784 |        -0.081412 |
| `TSA/100`   |         -0.51622 |         0.025422 |
| `AST/100`   |         0.430454 |        -0.025109 |
| `TOV/100`   |        -0.893465 |        -0.055809 |
| `ORB/100`   |         0.303023 |        -0.099034 |
| `DRB/100`   |        -0.085637 |         0.191569 |
| `STL/100`   |         0.418092 |         1.150891 |
| `BLK/100`   |        -0.230734 |         0.611107 |
| `PF/100`    |        -0.108369 |         0.010649 |
| OnCourt     |         0.018381 |         0.089391 |
| `On-Off`    |         0.032054 |         0.021717 |

There is also a version for pre-1997 seasons that lack the play-by-play data needed for plus/minus calculations:

|  Category   | Offensive Weight | Defensive Weight |
|-------------|------------------|------------------|
| (Intercept) |        -4.716102 |        -4.045701 |
| MPG         |         0.045769 |         0.026576 |
| `PTS/100`   |         0.695515 |          0.03021 |
| `TSA/100`   |        -0.593825 |         -0.03421 |
| `AST/100`   |         0.447521 |         0.012358 |
| `TOV/100`   |        -0.754827 |        -0.285614 |
| `ORB/100`   |         0.497774 |        -0.068515 |
| `DRB/100`   |        -0.112898 |         0.238577 |
| `STL/100`   |          0.42115 |         1.371489 |
| `BLK/100`   |        -0.186495 |         0.609386 |
| `PF/100`    |        -0.098824 |         0.004516 |

These raw ratings are then adjusted for position by making sure the following leaguewide targets are met for each position's minute-weighted average:

| POSITION | OFFENSE | DEFENSE |
|----------|---------|---------|
| PG       |     0.3 |    -0.3 |
| SG       |     0.2 |    -0.2 |
| SF       |       0 |       0 |
| PF       |    -0.2 |     0.2 |
| C        |    -0.5 |     0.5 |

And finally, the ratings are adjusted for team by ensuring that 4.5 times the minute-weighted average rating for each team's players is equal to the team's offensive or defensive rating relative to the league's average. So if a team's offensive rating was +1.5 relative to the league average, but 4.5 times the minute-weighted average of its players' offensive RAPTOR ratings was +1.0, then each player would receive an adjustment of (0.5 / 4.5) or +0.11 on offense to make the team match the sum of its parts.

Among players with at least 1,000 minutes in a season, the resulting ratings had correlations of 0.913 on offense, 0.784 on defense and 0.890 overall against the full-fledged version of RAPTOR (including player tracking inputs) from 2014-2023.

For **Wins Above Replacement** (WAR), a player's Net Rating Contribution is first calculated:

> Net Rating Contribution = (Minutes / (Team Total Minutes / 4.5)) * Total RAPTOR

Then Net Rating Contribution is converted to WAR per 82 team games via the following formula:

> WAR per 82 = (Minutes / (Team Total Minutes)) * 27.769 + 2.519 * Net Rating Contribution)

And WAR per 82 can be converted to actual WAR via:

> WAR = Team Schedule * (WAR per 82 / 82)
