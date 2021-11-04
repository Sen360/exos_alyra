Solidity

Execice 1:

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
}


Exercice 2:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
}


Exercice 3: 

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  function addPerson(string _name, uint _age) public {
    Person memomory person = Person(_name, _age);
  }
}

Exercice 4:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  Person[] public people;
}

Exercice 5:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  Person[] public people;
  
  function add(string _name, uint _age) public {
    Person memory person = Person(_name, _uint);
    people.push(person);
  } 

  function remove() public {
      people.pop();
  }
}

Exercice 6: 

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
  event Authorized(address _address);
}

Exercice - type de visibilité de fonctions

pragma solidity 0.6.0;

contract HelloWorld {
  string myString = "Hello World";
  
  function hello() public view returns (string memory) {
    return myString;
  }
}

Exercice - Whitelist

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
  event Authorized(address _address);
  
  function authorize(address _address) public {
    whitelist[_address] = true;
    emit Authorized(_address);
  }
}

Exercice variable spéciale
pragma solidity 0.8.9;

contract Time {
  function getTime() public view returns(uint) {
    return block.timestamp;
  }
}

Exercice msg.sender

pragma solidity 0.8.9;

contract Choice {
  mapping(address => uint) choices;
  function add(uint _myuint) public {
    choices[msg.sender] = _myuint;
  }
}

Excercice banque décentralisée

pragma solidity 0.8.9;

contract Bank {
    mapping(address => uint) private _balances;
    
    function deposit(uint _amount) public {
        require(msg.sender != address(0), "Can not deposir ether to the address 0");
        _balances[msg.sender] += _balances[msg.sender];
    } 
    
    function transfer(address _recipient, uint _amount) public {
        require(_recipient != address(0), "Can not transfer ether to the address 0");
        require(_balances[msg.sender] >= _amount, "Not enough ethers to transfer");
        _balances[msg.sender] -= _balances[msg.sender];
        _balances[_recipient] += _balances[_recipient];
    }
    
    function balanceOf(address _address) public view returns(uint) {
        return _balances[msg.sender];
    }
}

Exercice Système d'administration

pragma solidity 0.8.9;

contract Admin {
    mapping(address => bool) _whitelist;
    mapping(address => bool) _blacklist;
    event Whitelisted(address _address);
    event Blacklisted(address _address);
    
    function whitelist(address _address) public onlyOwner {
        require(!_whitelist[_address], "The address is already whitelisted");
        require(!_blacklist[_address], "The address is already blacklisted");
        _whitelist[_address] = true;
        emit Whitelisted(_address);
    }
    
    function blacklist(address _address) public onlyOwner {
        require(!_whitelist[_address], "The address is already whitelisted");
        require(!_blacklist[_address], "The address is already blacklisted");
        _whitelist[_address] = false;
        emit Blacklisted(_address);
    }
    
    function isWhitelisted(address _address) public view onlyOwner returns(bool) {
        return _whitelist[_address];
    }
    
     function isBlacklisted(address _address) public view onlyOwner returns(bool) {
        return _blacklist[_address];
    }
}

Système de Vote

// SPDX-License-Identifier: GPL-3.0

pragma solidity 0.8.9;
import 'openzeppelin-solidity/contracts/access/Ownable.sol';
/**
 * @title Voting
 * @dev Smart contract de vote
 */
contract Voting is Ownable {

    struct Voter {
        bool isRegistered;
        bool hasVoted;
        uint votedProposalId;
    }

    struct Proposal {
        string description;
        uint voteCount;
    }

    enum WorkflowStatus {
        RegisteringVoters,
        ProposalsRegistrationStarted,
        ProposalsRegistrationEnded,
        VotingSessionStarted,
        VotingSessionEnded,
        VotesTallied
    }

    event VoterRegistered(address voterAddress);
    event ProposalsRegistrationStarted();
    event ProposalsRegistrationEnded();
    event ProposalRegistered(uint proposalId);
    event VotingSessionStarted();
    event VotingSessionEnded();
    event Voted (address voter, uint proposalId);
    event VotesTallied();
    event WorkflowStatusChange(WorkflowStatus previousStatus, WorkflowStatus newStatus);

    uint public winningProposalId;

    mapping(address => bool) public whitelist;
    mapping(address => Voter) public voters;
    mapping(uint8 => WorkflowStatus) private state;

    WorkflowStatus public votingStatus;
    Proposal[] public proposals;

    /**
     * @dev Vérifie si l'utilisateur est enregistré
     */
    modifier registred {
        require(whitelist[msg.sender] == true, "Non enregistre sur la liste blanche");
        _;
    }

    /**
     * @dev Ajouter un électeur à la liste blanche
     * @param voterAddress Adresse du votant
     */
    function addWhiteList(address voterAddress) external onlyOwner {
        require(!whitelist[voterAddress], "Deja enregistre");
        whitelist[voterAddress] = true;
        voters[voterAddress] = Voter(true, false, 0);
        emit VoterRegistered(voterAddress);
    }
    /**
     * @dev passe à l'étape suivante du processus de propositions/vote
     */
    function nextStep() external onlyOwner {
        votingStatus = WorkflowStatus(uint(votingStatus)+1);
        emit WorkflowStatusChange(WorkflowStatus(uint(votingStatus)-1), WorkflowStatus(uint(votingStatus)));
        if (votingStatus == WorkflowStatus(uint(1))) { startProposalsRegistration();}
        if (votingStatus == WorkflowStatus(uint(2))) { closeProposalsRegistration();}
        if (votingStatus == WorkflowStatus(uint(3))) { startVotingSession();}
        if (votingStatus == WorkflowStatus(uint(4))) { closeVotingSession();}
        if (votingStatus == WorkflowStatus(uint(5))) { getWinningProposal();}
    }
    /**
     * @dev Démarrer la session d'enregistrement des propositions
     */
    function startProposalsRegistration() private onlyOwner {
        addProposal("Abstentation");
        emit ProposalsRegistrationStarted();

    }

    /**
     * @dev Ajouter une proposition
     * @param description Description de la proposition
     */
    function addProposal(string memory description) public registred {
        require(votingStatus == WorkflowStatus.ProposalsRegistrationStarted, "impossible de faire des propositions");
        proposals.push(Proposal(description,0));
        uint id = proposals.length-1;
        emit ProposalRegistered(id);
    }
    /**
     * @dev Mettre fin à la session d'enregistrement des propositions
     */
    function closeProposalsRegistration() private onlyOwner {
        emit ProposalsRegistrationEnded();
    }

    /**
     * @dev Démarrer la session de vote
     */
    function startVotingSession() private onlyOwner {
        emit VotingSessionStarted();

    }

    /**
     * @dev Voter pour une proposition
     * @param _proposalId ID de la proposition
     */
    function addVote(uint _proposalId) external registred {
        require(votingStatus == WorkflowStatus.VotingSessionStarted, "impossible de voter");
        require(voters[msg.sender].hasVoted == false, "Deja vote");
        proposals[_proposalId].voteCount++;
        voters[msg.sender] = Voter(true, true, _proposalId);
        emit Voted (msg.sender, _proposalId);
    }

    /**
     * @dev Mettre fin à la session de vote
     */
    function closeVotingSession() private onlyOwner {
        emit VotingSessionEnded();
    }

    /**
     * @dev Comptabilise les votes pour récupérer la proposition gagnante
     * @return _proposalId ID de la proposition gagnante
     */
    function getWinningProposal() private onlyOwner returns (uint _proposalId) {
        uint winnerVoteCount = 0;
        uint challenger = 0;
        for (uint i = 0; i < proposals.length; i++) {
            if (proposals[i].voteCount > winnerVoteCount) {
                winnerVoteCount = proposals[i].voteCount;
                _proposalId = i;
            } else if (proposals[i].voteCount == winnerVoteCount) {
                challenger = i;
            }
        }
        winningProposalId = _proposalId;
        if(winnerVoteCount == proposals[challenger].voteCount) {
             winningProposalId = 0;
        }
        emit VotesTallied();
        return winningProposalId;
    }
    

    /**
     * @dev Récupère les informations concernant la proposition gagnante
     * @return description Description de la proposition
     * @return voteCount Nombre de vote 
     */
    function getWinningInfo() public view returns (string memory description, uint voteCount) {
        require(winningProposalId != 0, "Aucune proposition gagnante");
        return (proposals[winningProposalId].description, proposals[winningProposalId].voteCount);
    }
    

    /**
     * @dev Récupère le nombre de propositions
     * @return uint Nombre de propositions
    */
    function getProposalCount() public view returns(uint) {
        return proposals.length;
    }
}
