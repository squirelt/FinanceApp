# FinanceApp

FinanceApp is a planned Windows desktop application for managing personal income,
expenses, budgets, and account balances.

The planned stack is C# / .NET 8 with WPF (MVVM) and a local encrypted SQLite
database. This repository currently contains specifications and agent
configuration; application code has not been created yet.

## Specifications

- [Detailed specification (German)](FSP_FinanceApp.md)
- [Original requirements (German)](FinanceApp_Spezifikation.md)

## Working on this project

Read [AGENTS.md](AGENTS.md) for the engineering-skills configuration.
Issues are tracked in [GitHub Issues](https://github.com/squirelt/FinanceApp/issues).

## Private financial data

The local Excel workbook `Finanzkontrolle_Vorlage.xlsx` contains financial values
and is intentionally excluded from Git. Local databases, backups, secrets, and
build output are also ignored. Do not commit personal financial data or
credentials.
