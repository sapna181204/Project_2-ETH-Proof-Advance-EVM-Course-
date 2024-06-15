# Project_2-ETH-Proof-Advance-EVM-Course-
# ETH Proof: Advance EVM Course PROJECT 2
# CODE
/*
ETH PROOF: Advanced EVM Course
1. Create separate Solidity contracts for both insurance types.
2. Have clearly defined policies (a minimum of two different types) for each insurance type.
3. Follow the factory contract model where for each user, a separate insurance contract is deployed.
4. Users should be able to pay the premium and claim the insurance with the required checks.
*/








// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;                                                          //license under which contract is released 

contract SimpleInsurance {
    address public owner;                                                        //address of owner
    uint public premium;                                                         //premium amount
    uint public coverageAmount;                                                  //coverage amount
    bool public isClaimed;                                                       //isclaimed
    uint public policyEndTime;                                                   //time when policy expires

    event PremiumPaid(address owner, uint amount);                               //event emitted when premium is paid
    event InsuranceClaimed(address owner, uint amount);                          //event emitted when insurance is claimed

    constructor(address _owner) payable {
        require(msg.value == 1 ether, "Send 1 ether to create policy");          //require 1 ether to create policy
        owner = _owner;                                                          //set owner of policy
        premium = 0.1 ether;                                                     //set premium amount
        coverageAmount = 1 ether;                                                //set coverage amount
        policyEndTime = block.timestamp + 2 seconds;                             //set policy end time to 10 minutes from now
        isClaimed = false;                                                       //initialize the isClaimed to be false 
    }

    function payPremium() external payable {
        require(msg.sender == owner, "Only owner can pay premium");              //only owner can pay the premium
        require(msg.value == premium, "Incorrect premium amount");               //sent amount must match the premium amount
        emit PremiumPaid(owner, msg.value);                                      //emit premiumpaid event
    }

    function claimInsurance() external {
        require(msg.sender == owner, "Only owner can claim");                    //only owner can claim insurance
        require(!isClaimed, "Already claimed");                                  //insurance can only be claimed once
        require(block.timestamp > policyEndTime, "Policy not expired yet");      //policy must have expired

        isClaimed = true;                                                        //isClaimed flag to true
        payable(owner).transfer(address(this).balance);                          //transfer contract balance to owner
        emit InsuranceClaimed(owner, address(this).balance);                     //emit insuranceclaimed event
    }
}

contract SimpleInsuranceFactory {
    mapping(address => SimpleInsurance) public userPolicies;                    //mapping to store user policies

    function createPolicy() external payable {
        require(address(userPolicies[msg.sender]) == address(0), 
        "Policy already exists"); //ensure user doesn't have an existing policy
        SimpleInsurance newPolicy = new SimpleInsurance{value: msg.value}
        (msg.sender);                            //create new SimpleInsurance contract and pass the msg.value and msg.sender
        userPolicies[msg.sender] = newPolicy;                     //store the new policy in the userPolicies mapping
    }

    function getMyPolicy() external view returns (SimpleInsurance) {
        return userPolicies[msg.sender];                          //return user's policy from the userpolicies mapping
    }
}

# EXPLANATION (README.md file) 

This Solidity code consists of two contracts: SimpleInsurance and SimpleInsuranceFactory.

The SimpleInsurance contract handles basic insurance policies, where users pay a 0.1 ether premium and can claim a 1 ether coverage amount after the policy expires. It restricts actions like premium payment and insurance claims to the policy owner, emitting events for these actions.

The SimpleInsuranceFactory contract serves as a factory for creating SimpleInsurance contracts per user. It manages user policies via mappings, enabling users to create, access, and manage their individual insurance contracts efficiently.


