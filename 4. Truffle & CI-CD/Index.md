/* eslint-disable no-undef */
const Voting = artifacts.require('Voting')
const { assert } = require('chai');

contract("Voting", ([owner, voter]) => {
    let VoterToken;

    beforeEach(async () => {
        VoterToken = await Voting.new({ from: owner });
    });

    it('modifier registered', async () => {
        let whitelist
        whitelist = await VoterToken.whitelist(voter)
        assert.equal(!whitelist, true);
    });

    it('addWhitelist', async () => {
        let whitelist, voterRegistered, voterAddress
        whitelist = await VoterToken.whitelist(voter)
        assert.equal(whitelist, false);
        voterRegistered = await VoterToken.VoterRegistered(voterAddress);
        assert.equal(voterRegistered, voterRegistered);
    });


    it('nextstep', async () => {
        let WorkflowStatusChange, WorkflowStatus;
        WorkflowStatusChange = await VoterToken.WorkflowStatusChange(WorkflowStatus);
        assert.equal(WorkflowStatusChange, WorkflowStatusChange);
    });


    it('Start Proposal registration', async () => {
        let ProposalsRegistrationStarted;
        ProposalsRegistrationStarted = await VoterToken.ProposalsRegistrationStarted(owner)
        assert.equal(ProposalsRegistrationStarted, ProposalsRegistrationStarted);
    });

    it('addProposal', async () => {
        let votingStatus, ProposalsRegistrationStarted, ProposalRegistered, id
        assert.equal(votingStatus, ProposalsRegistrationStarted);
        ProposalsRegistrationStarted = await VoterToken.ProposalRegistered(id);
        assert.equal(ProposalRegistered, ProposalRegistered);
    });

    it('startVotingSession', async () => {
        let VotingSessionStarted;
        VotingSessionStarted = await VoterToken.VotingSessionStarted(owner);
        assert.equal(VotingSessionStarted, VotingSessionStarted);
    });


    it('addVote', async () => {
        let votingStatus, VotingSessionStarted, voters, Voted, _proposalId;
        assert.equal(votingStatus, VotingSessionStarted);
        voters = await VoterToken.voters(voter);
        assert.equal(voters, voters);
        Voted = await VoterToken.Voted(voter, _proposalId);
        assert.equal(Voted, Voted);
    });

    it('closingSession', async () => {
        let VotingSessionEnded;
        VotingSessionEnded = await VoterToken.VotingSessionEnded(owner);
        assert.equal(VotingSessionEnded, VotingSessionEnded);
    });

    it('getWinningProposal', async () => {
        let VotesTallied;
        VotesTallied = await VoterToken.VotesTallied();
        assert.equal(VotesTallied, VotesTallied);
    });

    it('getWinningInfo', async () => {
        let winningProposalId;
        winningProposalId = await VoterToken.winningProposalId();
        assert.equal(winningProposalId, winningProposalId);
    });
})
