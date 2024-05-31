// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract PolsToken is ERC20 {
    address public owner;
    ERC20 public degenToken;

    constructor(uint256 initialSupply, address degenTokenAddress) ERC20("PolsToken", "Pol") {
        owner = msg.sender;
        _mint(msg.sender, initialSupply);
        degenToken = ERC20(degenTokenAddress);
    }

    function mint(address to, uint256 amount) public {
        require(msg.sender == owner, "Only the owner can mint tokens");
        _mint(to, amount);
    }

    function transfer(address to, uint256 amount) public virtual override returns (bool) {
        _transfer(_msgSender(), to, amount);
        return true;
    }

    function burn(uint256 amount) public {
        _burn(msg.sender, amount);
    }

    function redeem(uint256 amount) public {
        // Check if the sender has enough tokens to redeem
        require(balanceOf(msg.sender) >= amount, "Insufficient token balance to redeem");

        // Burn the specified amount of tokens from the sender's balance
        _burn(msg.sender, amount);

        // Transfer equivalent DegenTokens to the sender
        require(degenToken.balanceOf(address(this)) >= amount, "Insufficient DegenToken balance in contract");
        degenToken.transfer(msg.sender, amount);

        // Emit redeem event
        emit Redeem(msg.sender, amount);
    }

    event Redeem(address indexed account, uint256 amount);
}
