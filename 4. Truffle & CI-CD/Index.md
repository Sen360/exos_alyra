// SPDX-License-Identifier: GPL-3.0
pragma solidity 0.8.10;

import "@openzeppelin/contracts/access/Ownable.sol";

/**
 * @title Voting
 * @dev Smart contract de vote
 */
contract Voting is Ownable {
    struct Voter {
        bool isRegistered;
        bool hasVoted;
        uint256 votedProposalId;
    }

    struct Proposal {
        string description;
        uint256 voteCount;
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
    event ProposalRegistered(uint256 proposalId);
    event VotingSessionStarted();
    event VotingSessionEnded();
    event Voted(address voter, uint256 proposalId);
    event VotesTallied();
    event WorkflowStatusChange(
        WorkflowStatus previousStatus,
        WorkflowStatus newStatus
    );

    uint256 public winningProposalId;

    mapping(address => bool) public whitelist;
    mapping(address => Voter) public voters;
    mapping(uint8 => WorkflowStatus) private state;

    WorkflowStatus public votingStatus;
    Proposal[] public proposals;

    /**
     * @dev Vérifie si l'utilisateur est enregistré
     */
    modifier registred() {
        require(
            whitelist[msg.sender] == true,
            "Pas enregistre sur la liste blanche"
        );
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
        votingStatus = WorkflowStatus(uint256(votingStatus) + 1);
        emit WorkflowStatusChange(
            WorkflowStatus(uint256(votingStatus) - 1),
            WorkflowStatus(uint256(votingStatus))
        );
        if (votingStatus == WorkflowStatus(uint256(1))) {
            startProposalsRegistration();
        }
        if (votingStatus == WorkflowStatus(uint256(2))) {
            closeProposalsRegistration();
        }
        if (votingStatus == WorkflowStatus(uint256(3))) {
            startVotingSession();
        }
        if (votingStatus == WorkflowStatus(uint256(4))) {
            closeVotingSession();
        }
        if (votingStatus == WorkflowStatus(uint256(5))) {
            getWinningProposal();
        }
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
        require(
            votingStatus == WorkflowStatus.ProposalsRegistrationStarted,
            "impossible de faire des propositions"
        );
        proposals.push(Proposal(description, 0));
        uint256 id = proposals.length - 1;
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
    function addVote(uint256 _proposalId) external registred {
        require(
            votingStatus == WorkflowStatus.VotingSessionStarted,
            "impossible de voter"
        );
        require(voters[msg.sender].hasVoted == false, "Deja vote");
        proposals[_proposalId].voteCount++;
        voters[msg.sender] = Voter(true, true, _proposalId);
        emit Voted(msg.sender, _proposalId);
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
    function getWinningProposal()
        private
        onlyOwner
        returns (uint256 _proposalId)
    {
        uint256 winnerVoteCount = 0;
        uint256 challenger = 0;
        for (uint256 i = 0; i < proposals.length; i++) {
            if (proposals[i].voteCount > winnerVoteCount) {
                winnerVoteCount = proposals[i].voteCount;
                _proposalId = i;
            } else if (proposals[i].voteCount == winnerVoteCount) {
                challenger = i;
            }
        }
        winningProposalId = _proposalId;
        if (winnerVoteCount == proposals[challenger].voteCount) {
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
    function getWinningInfo()
        public
        view
        returns (string memory description, uint256 voteCount)
    {
        require(winningProposalId != 0, "Aucune proposition gagnante");
        return (
            proposals[winningProposalId].description,
            proposals[winningProposalId].voteCount
        );
    }

    /**
     * @dev Récupère le nombre de propositions
     * @return uint Nombre de propositions
     */
    function getProposalCount() public view returns (uint256) {
        return proposals.length;
    }
}

/* eslint-disable no-undef */
const Voting = artifacts.require('Voting')
const { assert } = require('chai');


contract("Voting", (accounts) => {
     const owner = accounts[0];
     const voter = accounts[1];
     let VoterToken;

    beforeEach(async () => {
        VoterToken = await Voting.new({ from: owner });
    });


    it('vérfie si utilisateur enregistré', async () => {
        let whitelist
        whitelist = await VoterToken.whitelist(voter)
        assert.equal(whitelist, false);
        await VoterToken.addWhiteList(voter);
        whitelist = await VoterToken.whitelist(voter)
        assert.equal(whitelist, true);
    });


    it('nextstep', async () => {
        let votingStatusOld, votingStatusNew
        votingStatusOld = await VoterToken.votingStatus({ from: owner });
        await VoterToken.nextStep({from: owner});
        votingStatusNew = await VoterToken.votingStatus({from: owner});
        assert.equal(votingStatusOld + 1, votingStatusNew);
    });

    it('nextstep not owner', async () => {
        let votingStatusOld, votingStatusNew
        votingStatusOld = await VoterToken.votingStatus();
        await VoterToken.nextStep({from: voter});
        votingStatusNew = await VoterToken.votingStatus();
        assert(votingStatusOld + 1, votingStatusNew);
    });

    it('Start Proposal registration', async () => {
        
    });

    it('addProposal', async () => {
        
    });

    it('startVotingSession', async () => {
       
    });


    it('addVote', async () => {
        let voters, voteStarted
        voteStarted = await VoterToken.addVote({from: voter});
        assert.equal(voteStarted, false);
        voters = await VoterToken.addVote({from: voter});
        assert.equal(voters, false);
        voters = await VoterToken.addVote({from: voter});
        assert.equal(voters, true);
    });

    it('closingSession', async () => {
       
    });



