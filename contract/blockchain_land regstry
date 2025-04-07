// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LandRegistry {

    address public admin;

    constructor() {
        admin = msg.sender;
    }

    struct Land {
        uint256 id;
        string location;
        uint256 area; // in square feet
        address currentOwner;
        bool isRegistered;
    }

    uint256 public landCounter;
    mapping(uint256 => Land) public lands;
    mapping(address => uint256[]) public ownerToLands;

    modifier onlyAdmin() {
        require(msg.sender == admin, "Only admin can perform this action");
        _;
    }

    modifier onlyOwner(uint256 landId) {
        require(lands[landId].currentOwner == msg.sender, "You are not the owner of this land");
        _;
    }

    event LandRegistered(uint256 indexed landId, address indexed owner);
    event LandTransferred(uint256 indexed landId, address indexed from, address indexed to);

    function registerLand(string memory _location, uint256 _area, address _owner) public onlyAdmin {
        landCounter++;
        lands[landCounter] = Land(landCounter, _location, _area, _owner, true);
        ownerToLands[_owner].push(landCounter);
        emit LandRegistered(landCounter, _owner);
    }

    function transferLand(uint256 _landId, address _to) public onlyOwner(_landId) {
        require(lands[_landId].isRegistered, "Land not registered");

        address previousOwner = lands[_landId].currentOwner;
        lands[_landId].currentOwner = _to;
        ownerToLands[_to].push(_landId);

        // Remove land from previous owner's list
        uint256[] storage ownerLands = ownerToLands[previousOwner];
        for (uint i = 0; i < ownerLands.length; i++) {
            if (ownerLands[i] == _landId) {
                ownerLands[i] = ownerLands[ownerLands.length - 1];
                ownerLands.pop();
                break;
            }
        }

        emit LandTransferred(_landId, previousOwner, _to);
    }

    function getLandsByOwner(address _owner) public view returns (uint256[] memory) {
        return ownerToLands[_owner];
    }

    function getLandDetails(uint256 _landId) public view returns (
        uint256 id,
        string memory location,
        uint256 area,
        address currentOwner,
        bool isRegistered
    ) {
        Land memory land = lands[_landId];
        return (land.id, land.location, land.area, land.currentOwner, land.isRegistered);
    }
}
