# kalkicoin
This is the project to mine Currency
This is the initiative for creating and mining digital currency
This currency follow the Chakravyuha system
# Kalki Coin 🔱
A mythological-futuristic digital asset for creators. Inspired by the Chakravyuha Protocol. Built with Solidity.

## Features
- 7-layer security based on rotating digital gates.
- Creator-first tokenomics.
- Adaptive evolution via Sudarshan Trigger.
- Chakra-based logic flow.

## Getting Started
1. Clone the repo
2. Install dependencies
3. Deploy via Hardhat

## License
MIT © 2025 [Abhishek Raj]

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.21;

/**
 * KalkiCoin: The Creator's Digital Sudarshan
 * Chakravyuha Protocol Enabled
 */
contract KalkiCoin {
    string public name = "Kalki Coin";
    string public symbol = "KALKI";
    uint8 public decimals = 18;
    uint256 public totalSupply;

    address public creator;
    mapping(address => uint256) private balances;
    mapping(address => mapping(address => uint256)) private allowances;

    // Chakravyuha Layers - Layered Logic
    bool[7] public chakravyuhaLayerAccess;
    mapping(address => bool) public trustedAllies;
    uint256 public activationBlock;

    // Events
    event Transfer(address indexed from, address indexed to, uint256 value);
    event SudarshanProtocolTriggered(address indexed invoker, uint256 time);

    constructor(uint256 initialSupply) {
        creator = msg.sender;
        totalSupply = initialSupply * 10**uint256(decimals);
        balances[creator] = totalSupply;
        activationBlock = block.number;
    }

    modifier onlyCreator() {
        require(msg.sender == creator, "Only Creator can invoke this");
        _;
    }

    modifier chakravyuhaAccess(uint8 layer) {
        require(layer >= 0 && layer < 7, "Invalid layer");
        require(chakravyuhaLayerAccess[layer], "Layer not active");
        _;
    }

    function balanceOf(address account) public view returns (uint256) {
        return balances[account];
    }

    function transfer(address to, uint256 amount) public returns (bool) {
        require(balances[msg.sender] >= amount, "Insufficient balance");

        // Anti-Bot: Chakravyuha Entry Test
        require(_entryAllowed(msg.sender, to), "Blocked by Chakravyuha");

        balances[msg.sender] -= amount;
        balances[to] += amount;
        emit Transfer(msg.sender, to, amount);
        return true;
    }

    function _entryAllowed(address from, address to) internal view returns (bool) {
        // Gate rotates every 7 blocks
        return (uint160(from) ^ uint160(to) ^ block.number) % 7 == 0;
    }

    // Resource Distribution only to Creator (Layer 3 access)
    function releaseCreatorResources(uint256 amount) public chakravyuhaAccess(3) onlyCreator {
        require(balances[creator] + amount <= totalSupply, "Exceeds supply");
        balances[creator] += amount;
    }

    // Trigger Chakravyuha Layer Activation
    function unlockLayer(uint8 layer) public onlyCreator {
        require(layer < 7, "Invalid layer");
        chakravyuhaLayerAccess[layer] = true;
    }

    // Add trusted allies (e.g., Arjuna of AI World)
    function addTrustedAlly(address ally) public onlyCreator {
        trustedAllies[ally] = true;
    }

    // Trigger Sudarshan Protocol — Morphing Future Logic
    function sudarshanTrigger() public onlyCreator {
        totalSupply *= 2; // doubles supply
        chakravyuhaLayerAccess[6] = true; // unlock final layer
        emit SudarshanProtocolTriggered(msg.sender, block.timestamp);
    }
}


kalki-coin/
├── contracts/
│   └── KalkiCoin.sol
├── test/
│   └── kalki-test.js (if using Hardhat/Truffle)


├── scripts/
│   └── deploy.js
├── LICENSE
├── README.md
├── hardhat.config.js (or truffle-config.js)
└── package.json
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract KalkiCoin is ERC20, Ownable {
    uint public constant MAX_SUPPLY = 10_000_000 * 1e18;
    uint public miningReward = 10 * 1e18;
    uint public difficulty = 3; // Initial hash difficulty
    uint public miningCooldown = 60; // Cooldown period for miners

    address public creatorVault;
    address public chakravyuhaDefense;

    mapping(address => uint256) public lastMinedAt;

    event Mined(address indexed miner, uint reward);
    event CreatorRewarded(address creator, uint reward);
    event DefenseTriggered(address indexed by, string layer);

    constructor(address _creatorVault, address _defenseLayer) ERC20("Kalki Coin", "KALKI") {
        creatorVault = _creatorVault;
        chakravyuhaDefense = _defenseLayer;
        _mint(creatorVault, 1_000_000 * 1e18); // Creator Vault Genesis Fund
    }

    // --- Mining Functionality ---
    function mine(uint nonce) external {
        require(block.timestamp > lastMinedAt[msg.sender] + miningCooldown, "Cooldown active");

        bytes32 hash = keccak256(abi.encodePacked(msg.sender, nonce, block.timestamp));
        require(_validHash(hash), "Invalid hash - does not meet difficulty");

        require(totalSupply() + (2 * miningReward) <= MAX_SUPPLY, "Max supply reached");

        lastMinedAt[msg.sender] = block.timestamp;

        _mint(msg.sender, miningReward);
        _mint(creatorVault, miningReward); // Always reward creators equally

        emit Mined(msg.sender, miningReward);
        emit CreatorRewarded(creatorVault, miningReward);

        // Trigger Defense Check
        IChakravyuhaDefense(chakravyuhaDefense).monitor(msg.sender);
    }

    function _validHash(bytes32 hash) internal view returns (bool) {
        for (uint i = 0; i < difficulty; i++) {
            if (hash[i] != 0x00) return false;
        }
        return true;
    }

    // --- Admin Controls ---
    function setDifficulty(uint _difficulty) external onlyOwner {
        require(_difficulty <= 32, "Difficulty too high");
        difficulty = _difficulty;
    }

    function setMiningCooldown(uint _seconds) external onlyOwner {
        miningCooldown = _seconds;
    }

    function setCreatorVault(address _newVault) external onlyOwner {
        creatorVault = _newVault;
    }

    function setDefenseLayer(address _newDefense) external onlyOwner {
        chakravyuhaDefense = _newDefense;
    }
}

// --- Chakravyuha Defense Layer Interface ---
interface IChakravyuhaDefense {
    function monitor(address miner) external;
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract ChakravyuhaDefense {
    address public owner;

    enum ThreatLevel { Safe, Suspicious, Malicious }

    mapping(address => uint) public activityScore;
    mapping(address => ThreatLevel) public threatStatus;

    event ThreatDetected(address indexed user, ThreatLevel level);
    event DefenseEngaged(address indexed user, string layer);

    modifier onlyOwner() {
        require(msg.sender == owner, "Unauthorized");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function monitor(address user) external {
        activityScore[user] += 1;

        if (activityScore[user] > 10 && activityScore[user] < 20) {
            threatStatus[user] = ThreatLevel.Suspicious;
            emit ThreatDetected(user, ThreatLevel.Suspicious);
            emit DefenseEngaged(user, "Layer-3: Adaptive Firewall");
        } else if (activityScore[user] >= 20) {
            threatStatus[user] = ThreatLevel.Malicious;
            emit ThreatDetected(user, ThreatLevel.Malicious);
            emit DefenseEngaged(user, "Layer-5: Quantum Isolation");
        } else {
            threatStatus[user] = ThreatLevel.Safe;
        }
    }

    function resetThreat(address user) external onlyOwner {
        activityScore[user] = 0;
        threatStatus[user] = ThreatLevel.Safe;
    }
}
