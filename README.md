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

# Trap Logic Summary

Note: address public constant tokenAddress = 0x54a53Ccdf04ad09CE673973E818c50917a892b3f - you need to change the token address you want to track 
      address public constant watchAddress = 0xe64dE8a50813307119B7e083B20D894C9cB1dbAB - you need to change the wallet address you want to track
      thresholdPercent = 5   -  you can change percentage
      

<pre> // SPDX-License-Identifier: MIT
pragma solidity ^0.8.29;

interface ITrap {
    function collect() external returns (bytes memory);
    function shouldRespond(bytes[] calldata data) external pure returns (bool, bytes memory);
}

interface IERC20 {
    function balanceOf(address account) external view returns (uint256);
}

contract TokenBalanceAnomalyTrap is ITrap {
    address public constant tokenAddress = 0x54a53Ccdf04ad09CE673973E818c50917a892b3f; //  change
    address public constant watchAddress = 0xe64dE8a50813307119B7e083B20D894C9cB1dbAB; //  change
    uint256 public constant thresholdPercent = 5;

    function collect() external view override returns (bytes memory) {
    uint256 tokenBalance = IERC20(tokenAddress).balanceOf(watchAddress);
    return abi.encode(tokenBalance);




}
    function shouldRespond(bytes[] calldata data) external pure override returns (bool, bytes memory) {
        if (data.length < 2) return (false, "Insufficient data");

        uint256 current = abi.decode(data[0], (uint256));
        uint256 previous = abi.decode(data[1], (uint256));

        if (previous == 0 && current > 0) {
            return (true, "Initial non-zero balance detected");
        }

        uint256 diff = current > previous ? current - previous : previous - current;
        uint256 percent = previous > 0 ? (diff * 100) / previous : 0;

        if (percent >= thresholdPercent) {
            return (true, "Balance anomaly detected");
        }

        return (false, "");
    }
} </pre>


# Response Contract: LogAlertReceiver.sol

<pre> // SPDX-License-Identifier: MIT
pragma solidity ^0.8.29;

contract LogAlertReceiver {
    event Alert(string message);

    function logAnomaly(string calldata message) external {
        emit Alert(message);
    }
} </pre>


