// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

contract CrowdFunding {

    address public immutable owner;
    uint256 public currentBalance;
    uint256 public totalRaised;
    uint256 public immutable goal;
    uint256 public constant MINIMUMDONATION = 1 ether;

    uint256 public immutable deadline;
    uint256 public completedAt;
    
    mapping(address => uint256) public donors;
   

    event DonationEvent(address indexed contributor, uint indexed amount);


    constructor(uint256 _duration, uint256 _goal) {
        owner = msg.sender;
        deadline = block.timestamp + _duration;
        goal = _goal;
    }

    function donate() public payable {
        require(block.timestamp < deadline, "CrowdFunding: The time has expired");
        require(msg.value >= MINIMUMDONATION, "CrowdFunding: Not enough");
        require(currentBalance < goal, "CrowdFunding: goal already reached");
        currentBalance += msg.value;
        totalRaised += msg.value;
        donors[msg.sender] += msg.value;
        emit DonationEvent(msg.sender, msg.value);

        if(currentBalance >= goal){
            completedAt = block.timestamp;
        }
    }

    function withdraw() public onlyOwner(){
        require(block.timestamp > deadline, "CrowdFunding: not expired");
        require(currentBalance >= goal, "CrowdFunding: goal not reached");
        payable(owner).transfer(currentBalance);
        currentBalance = 0;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "CrowdFunding: only owner");
        _;
    }

    




}
