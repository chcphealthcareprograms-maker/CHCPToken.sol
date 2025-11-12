// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

/*
    CHCPToken — An ERC-20 compliant token with clear explanations.
    This contract follows the ERC-20 standard so it’s compatible with
    wallets like Trust Wallet, MetaMask, and exchanges that support ERC-20 tokens.
*/

contract CHCPToken {
    // --- Basic Token Information ---
    string public name = "CHCP Wallet Token";      // Full token name
    string public symbol = "CHCP";                 // Token ticker symbol
    uint8 public decimals = 18;                    // Number of decimal places
    uint256 public totalSupply;                    // Total amount of tokens in existence

    // --- Mappings (Core Storage) ---
    mapping(address => uint256) public balanceOf;  // Tracks each address’s balance
    mapping(address => mapping(address => uint256)) public allowance; 
    // allowance[owner][spender] -> amount authorized for spender to spend

    // --- Events (to notify off-chain apps like explorers & wallets) ---
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    /*
        The constructor runs once — when the contract is first deployed.
        We give the deployer (msg.sender) all tokens initially.
        _initialSupply should be passed in *whole units* (e.g. 1000000 for 1 million tokens).
    */
    constructor(uint256 _initialSupply) {
        totalSupply = _initialSupply * (10 ** uint256(decimals));  // scale total supply by decimals
        balanceOf[msg.sender] = totalSupply;                       // assign all tokens to deployer
        emit Transfer(address(0), msg.sender, totalSupply);        // emit event showing mint
    }

    /*
        Transfer tokens from the sender (msg.sender) to another address (_to).
        Requirements:
          - sender must have enough tokens
    */
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Invalid recipient");
        require(balanceOf[msg.sender] >= _value, "Insufficient balance");

        balanceOf[msg.sender] -= _value;  // subtract from sender
        balanceOf[_to] += _value;         // add to recipient
        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    /*
        Approve another address (_spender) to spend a certain number of tokens
        on your behalf.
    */
    function approve(address _spender, uint256 _value) public returns (bool success) {
        require(_spender != address(0), "Invalid spender");
        allowance[msg.sender][_spender] = _value;  // set allowance
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    /*
        Transfer tokens from one address to another, using an allowance.
        This allows smart contracts or other accounts to move tokens you’ve approved.
    */
    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Invalid recipient");
        require(balanceOf[_from] >= _value, "Insufficient balance");
        require(allowance[_from][msg.sender] >= _value, "Allowance exceeded");

        balanceOf[_from] -= _value;                     // subtract from sender
        balanceOf[_to] += _value;                       // add to recipient
        allowance[_from][msg.sender] -= _value;         // reduce spender allowance
        emit Transfer(_from, _to, _value);
        return true;
    }

    /*
        (Optional Utility)
        Mint new tokens to a specified address.
        Only the contract deployer can call this function.
    */
    function mint(address _to, uint256 _amount) public {
        require(msg.sender == deployer, "Only deployer can mint");
        totalSupply += _amount;
        balanceOf[_to] += _amount;
        emit Transfer(address(0), _to, _amount);
    }

    /*
        (Optional Utility)
        Burn tokens (permanently destroy them) from the caller’s account.
    */
    function burn(uint256 _amount) public {
        require(balanceOf[msg.sender] >= _amount, "Insufficient balance");
        balanceOf[msg.sender] -= _amount;
        totalSupply -= _amount;
        emit Transfer(msg.sender, address(0), _amount);
    }

    // --- Owner Variable for Minting Control ---
    address public deployer = msg.sender;
}added initial CHCPToken.sol
