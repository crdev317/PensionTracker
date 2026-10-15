# Pension Tracker

The language of Pension Tracker, a personal app in which one person records the UK pensions they hold and the figures their providers state for them.

## Language

### People and pensions

**Owner**:
The one person who uses Pension Tracker and holds every Pension it records.
_Avoid_: User, member, customer

**Pension**:
One pension arrangement the Owner holds with one Provider, either a Pot Pension or an Income Pension, not the Provider itself.
_Avoid_: Pot, plan, scheme, policy, account

**Pot Pension**:
A Pension that builds a pot of money whose value rises and falls, such as a workplace, personal or SIPP pension, unlike an Income Pension, which promises a set income.
_Avoid_: Defined contribution, DC, money purchase

**Income Pension**:
A Pension that promises a yearly income from its Retirement Date, such as a final-salary pension or the State Pension, unlike a Pot Pension, which has a value.
_Avoid_: Defined benefit, DB, final salary

**Provider**:
The organisation that runs a Pension, including the government for the State Pension.
_Avoid_: Insurer, administrator, company

**Retirement Date**:
The date from which one Pension can be taken or starts paying, which each Pension has for itself, not the date the Owner plans to retire.
_Avoid_: Retirement age, pension age

**Closed Pension**:
A Pension the Owner no longer holds, such as one transferred to another Provider or taken in full, kept with all its figures but not counted in any total.
_Avoid_: Archived, inactive, deleted

### Figures

**Valuation**:
The value of a Pot Pension on a given date, as its Provider states it.
_Avoid_: Balance, fund value, worth

**Contribution**:
One amount paid into a Pot Pension on a given date, whoever paid it, not a change in its value.
_Avoid_: Payment, deposit, top-up

**Growth**:
How much a Pot Pension's value has changed since its first Valuation, leaving out the Contributions paid in since then, not its whole value.
_Avoid_: Return, profit, gain

**Income Forecast**:
The yearly income an Income Pension promises from its Retirement Date, as its Provider states it on a given date.
_Avoid_: Projection, entitlement, benefit

## Relationships

- The **Owner** holds one or more **Pensions**
- A **Pension** is run by exactly one **Provider** and has exactly one **Retirement Date**
- A **Pension** is either a **Pot Pension** or an **Income Pension**, never both
- A **Pot Pension** has zero or more **Valuations** and zero or more **Contributions**
- An **Income Pension** has zero or more **Income Forecasts**
- A **Pension** is either open or a **Closed Pension**; a **Closed Pension** keeps its figures but counts in no total
- A **Pot Pension**'s **Growth** comes from its first and latest **Valuations** and the **Contributions** between them

## Example dialogue

> **Dev:** "The Owner's workplace statement shows £40,000, and £3,000 was paid in this year. Is that one **Valuation**?"
> **Domain expert:** "It's one **Valuation** of £40,000 on the statement date. The £3,000 is recorded as **Contributions**, never added to the **Valuation**."
> **Dev:** "Does the State Pension get a **Valuation** too?"
> **Domain expert:** "No. The State Pension is an **Income Pension**, so it gets an **Income Forecast**: the yearly income it promises from its own **Retirement Date**."
> **Dev:** "Should we estimate what a **Pot Pension** will be worth at its **Retirement Date**?"
> **Domain expert:** "No. Pension Tracker holds only the figures the Owner records; it estimates nothing."

## Flagged ambiguities

- "pot" was used both for any **Pension** and for the money in a **Pot Pension**. Resolved: **Pension** is the arrangement, and "pot" appears only inside the term **Pot Pension**.
- "retirement date" could mean the date the Owner plans to retire or the date one **Pension** can be taken. Resolved: **Retirement Date** is always one **Pension**'s own date; the Owner's plan is not a term here.
- "projection" could mean a **Provider**'s promised income or an estimate of a pot's future value. Resolved: the promise is an **Income Forecast**, and Pension Tracker makes no estimates.
