// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CarbonToken {
    string public name;
    string public symbol;
    uint256 public totalSupply;

    mapping(address => uint256) public balanceOf;

    event Transfer(address indexed from, address indexed to, uint256 value);

    constructor(string memory _name, string memory _symbol, uint256 _totalSupply) {
        name = _name;
        symbol = _symbol;
        totalSupply = _totalSupply;
        balanceOf[msg.sender] = _totalSupply;
    }

    function transfer(address _to, uint256 _value) external returns (bool) {
        require(balanceOf[msg.sender] >= _value, "Insufficient balance");
        require(_to != address(0), "Invalid recipient address");

        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;

        emit Transfer(msg.sender, _to, _value);

        return true;
    }
}

contract CarbonDApp {
    CarbonToken public carbonToken;
    address public owner;
    bool public paused;

    mapping(address => uint256) public carbonOffsets;

    event OffsetCarbon(address indexed from, uint256 value);
    event RedeemCarbon(address indexed from, uint256 value);

    modifier onlyOwner() {
        require(msg.sender == owner, "Only the contract owner can perform this action");
        _;
    }

    modifier whenNotPaused() {
        require(!paused, "Contract is paused");
        _;
    }

    constructor(address _carbonTokenAddress) {
        carbonToken = CarbonToken(_carbonTokenAddress);
        owner = msg.sender; // Set the contract owner
        paused = false; // Initialize the contract as not paused
    }

    function offsetCarbon(uint256 _value) external whenNotPaused {
        require(_value > 0, "Invalid offset value");
        require(carbonToken.balanceOf(msg.sender) >= _value, "Insufficient token balance");

        carbonToken.transferFrom(msg.sender, address(this), _value);
        carbonOffsets[msg.sender] += _value;

        emit OffsetCarbon(msg.sender, _value);
    }

    function redeemCarbon(uint256 _value) external whenNotPaused {
        require(_value > 0, "Invalid redeem value");
        require(carbonOffsets[msg.sender] >= _value, "Insufficient carbon offsets");

        carbonOffsets[msg.sender] -= _value;
        carbonToken.transfer(msg.sender, _value);

        emit RedeemCarbon(msg.sender, _value);
    }

    function pauseContract() external onlyOwner {
        paused = true; // Pause the contract
    }

    function unpauseContract() external onlyOwner {
        paused = false; // Unpause the contract
    }

    // Function to transfer ownership of the contract to a new address
    function transferOwnership(address newOwner) external onlyOwner {
        require(newOwner != address(0), "Invalid new owner address");

        owner = newOwner;
    }

    // Function to upgrade the CarbonToken contract
    function upgradeCarbonToken(address newCarbonTokenAddress) external onlyOwner {
        require(newCarbonTokenAddress != address(0), "Invalid new CarbonToken address");

        carbonToken = CarbonToken(newCarbonTokenAddress);
    }
}
