# lung// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LungHealthTracker {

    struct LungHealth {
        uint256 lungCapacity;  // Measured in milliliters (mL)
        string condition;      // Example: "Healthy", "Asthma", "COPD"
        uint256 lastUpdated;   // Timestamp of the last update
    }

    mapping(address => LungHealth) public lungHealthData;

    event LungHealthRecorded(address indexed user, uint256 lungCapacity, string condition);
    event LungHealthUpdated(address indexed user, uint256 newLungCapacity, string newCondition);

    // Record initial lung health data
    function recordLungHealth(uint256 _lungCapacity, string memory _condition) public {
        require(_lungCapacity > 0, "Lung capacity must be positive");

        LungHealth storage lungHealth = lungHealthData[msg.sender];
        require(lungHealth.lungCapacity == 0, "Lung health data already recorded. Use update function.");

        lungHealth.lungCapacity = _lungCapacity;
        lungHealth.condition = _condition;
        lungHealth.lastUpdated = block.timestamp;

        emit LungHealthRecorded(msg.sender, _lungCapacity, _condition);
    }

    // Update lung health data
    function updateLungHealth(uint256 _newLungCapacity, string memory _newCondition) public {
        require(_newLungCapacity > 0, "Lung capacity must be positive");
        
        LungHealth storage lungHealth = lungHealthData[msg.sender];
        require(lungHealth.lungCapacity > 0, "You must record initial data before updating");

        lungHealth.lungCapacity = _newLungCapacity;
        lungHealth.condition = _newCondition;
        lungHealth.lastUpdated = block.timestamp;

        emit LungHealthUpdated(msg.sender, _newLungCapacity, _newCondition);
    }

    // View lung health data
    function getLungHealth() public view returns (uint256 lungCapacity, string memory condition, uint256 lastUpdated) {
        LungHealth memory lungHealth = lungHealthData[msg.sender];
        require(lungHealth.lungCapacity > 0, "No lung health data recorded");

        return (lungHealth.lungCapacity, lungHealth.condition, lungHealth.lastUpdated);
    }
}
