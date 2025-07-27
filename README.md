# Token-balance-anomaly-Trap
Trap for tracking balance of various tokens (Drosera trap sergiant/captain)

# Objective

Create a functional and deployable Drosera trap that:

Monitors balances of various tokens on a specific wallet

Uses the standard collect() / shouldRespond() interface

Triggers a response when balance deviation exceeds a given threshold (e.g., 5%)

Integrates with a separate alert contract to handle responses.

# Problem

Any unexpected change in the balance on the wallet may indicate either theft or some kind of error.

Solution: Monitor token balance of a wallet across blocks. Trigger a response if there's a significant deviation in either direction.
