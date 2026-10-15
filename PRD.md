# Pension Tracker — Product Requirements

## Problem Statement

The Owner holds several Pensions with different Providers: Pot Pensions from past and present jobs, perhaps a personal pension, a final-salary scheme and the State Pension. Each Provider sends its own statement, on its own schedule, in its own format. There is no single place where the Owner can see what all their Pensions are worth today, how much yearly income they have been promised in retirement, how much they have paid in, and how much their pots have grown. Piecing it together means digging out old statements every time, so the Owner rarely has a clear picture.

## Solution

Pension Tracker is a simple Windows desktop app in which the Owner records each Pension they hold and the figures its Provider states: Valuations and Contributions for a Pot Pension, and Income Forecasts for an Income Pension. When the Owner opens the app, they see every open Pension with its latest figure, together with the total value of their pots and the total yearly income they have been promised. Opening a Pension shows its full history and, for a Pot Pension, its Growth. Everything stays on the Owner's own computer, and the app makes no estimates: it shows only the figures the Owner has recorded.

## Requirements

### Pensions

1. The Owner can add a Pension by entering its name, its Provider, whether it is a Pot Pension or an Income Pension, and its Retirement Date.
2. Every Pension is either a Pot Pension or an Income Pension, chosen when it is added; a Pension is never both.
3. The Owner can edit a Pension's name, Provider and Retirement Date.
4. The Owner can mark a Pension as a Closed Pension when they no longer hold it, for example when it has been transferred to another Provider or taken in full.
5. A Closed Pension keeps all its figures and its history stays viewable.
6. A Closed Pension is not counted in any total.
7. The Owner can delete a Pension, with all its figures, after confirming that they want to.

### Figures

8. The Owner can record a Valuation for a Pot Pension: an amount in pounds and the date the Provider states it for.
9. The Owner can record a Contribution for a Pot Pension: one amount in pounds and the date it was paid in, whoever paid it.
10. The Owner can record an Income Forecast for an Income Pension: a yearly amount in pounds and the date the Provider stated it.
11. Valuations and Contributions can be recorded only for a Pot Pension, and Income Forecasts only for an Income Pension.
12. The Owner can edit any Valuation, Contribution or Income Forecast.
13. The Owner can delete any Valuation, Contribution or Income Forecast after confirming that they want to.
14. Every amount is held exactly to the penny, with no rounding drift.

### Main screen

15. When the app opens, it shows every open Pension with its name, its Provider, its kind and its latest figure: the latest Valuation for a Pot Pension, or the latest Income Forecast for an Income Pension, each with the date it applies to.
16. A Pension with no figures yet is shown with no latest figure.
17. Closed Pensions are shown greyed out, below the open Pensions.
18. The main screen shows the total value of the Owner's pots: the sum of the latest Valuation of every open Pot Pension.
19. The main screen shows the total yearly income the Owner has been promised: the sum of the latest Income Forecast of every open Income Pension, labelled with the latest Retirement Date among those Income Pensions, as the date from which that whole total applies.

### One Pension's history

20. Opening a Pension shows its details and its history as a table, newest first.
21. For a Pot Pension, the history lists its Valuations and its Contributions, each with its date and amount.
22. For an Income Pension, the history lists its Income Forecasts, each with the date stated and the yearly amount.
23. For a Pot Pension, the app shows the total of its Contributions, its latest Valuation and its Growth.
24. Growth is the latest Valuation, less the first Valuation, less the Contributions dated after the first Valuation and on or before the latest Valuation.
25. A Pot Pension with fewer than two Valuations shows no Growth.

### How the app behaves

26. Money is shown in pounds, as £12,345.67, and dates as DD/MM/YYYY.
27. A successful save is confirmed by a short message in the app; an error is explained in plain words, with what to do next.
28. Deleting anything asks the Owner to confirm first.
29. Everything the Owner records stays on their own computer: the app makes no network connection of any kind.
30. The Owner's recorded figures are never lost when the app is updated to a new version.

## Out of Scope

- Estimating the future value of a Pot Pension, or any projection of income or value.
- Connecting to a Provider, importing statements, or checking figures against a Provider.
- Splitting a Contribution by who paid it.
- Charts.
- Policy numbers, notes or attachments on a Pension.
- Backing up the Owner's figures; the Owner's own computer backup covers them.
- More than one Owner, logins or passwords.
- Running on anything other than Windows.
