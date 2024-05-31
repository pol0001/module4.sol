// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract PolsToken is ERC20 {
    address public owner;

    constructor(uint256 initialSupply) ERC20("PolsToken", "Pol") {
        owner = msg.sender;
        _mint(msg.sender, initialSupply);
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

        // Redeem logic: here, it can be sending equivalent value in another asset or service
        // This example doesn't include actual redeem logic for simplicity
        // You can implement the actual redeeming mechanism as per your requirements

        // Set a redeem message (optional)
        emit Redeem(msg.sender, amount);
    }

    event Redeem(address indexed account, uint256 amount);
}
