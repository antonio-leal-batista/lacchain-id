# Documentation of the smart contract for credential registration

This document aims to provide documentation for smart contract [CredentialRegistry](https://github.com/lacchain/lacchain-id/blob/master/contracts/CredentialRegistry.sol).

## What is this smart contract for?

This smart contract will serve as a public database to store information self-attested by citizens. 

## Components

### ICredentialRegistry 

It is an interface which defines following struct:

**CredentialMetadata**
```
struct CredentialMetadata {
        bytes32 issuerDid;
        bytes32 subjectDid;
        string purpose;
        uint iat;
        uint exp;
        bool status;
    }
```
This struct will save metadata related the verifiable credential.

* issuerDid: is an unique issuer identifier.
* subjectDid: is an unique citizen identifier generated by each app. It must not be associated to personal data. For Example: 0x93FA3E4624676F2E9AA143911118B4547087E9B6E0B6076F2E1027D7A2DA2B0A
* iat: the timestamp in milliseconds when the credential is issued. For example: 123456
* exp: the timestamp in milliseconds when the credential expires. For example: 124000
* status: The state of the credential. true for valid and false for invalid. 

### AccessControl

This contract manages the roles. This contract is an implementation from [AccessControl Openzeppelin](https://docs.openzeppelin.com/contracts/3.x/api/access#AccessControl).

### CredentialRegistry

This contract implements the ICredentialRegistry interface and inherits AccessControl methods.

**Register a credential**

`function register(bytes32 credentialHash, bytes32 issuerDid, bytes32 subjectDid, string memory purpose, uint exp) override onlyIssuer external returns(bool)`

This function register a new credential and metadata from a issuer address. 

* credentialHash: this parameter is the hash of some fields in the verifiable credentials that are common to all the apps. 

**Revoke covid credential**

`function revoke(bytes32 credentialHash) onlyIssuer override external returns(bool)`

This function revoke a credential previously registered.

* credentialHash: this parameter is the hash of the verifiable credential that will be revoked.

the requirements for the credential to be revoked are that: 

* only the same address that registered the credential can revoke it (the credential exists) and that the credential is in a valid state.

**Verify a credential**

`function verify(bytes32 credentialHash, address issuer) override external view returns(bool isValid)`

This function verify if a credential is valid.

* credentialHash: this parameter is the hash of the verifiable credential that will be verified.
* issuer: address of a issuer who generated the credential.

## Deploy and Upgrade Smart Contract

We are using [Openzeppelin Cli](https://docs.openzeppelin.com/cli/2.8/) to deploy the upgradable smart contract.

First, you need to install openzeppelin cli dependencies. 

```$ npm install --save-dev @openzeppelin/cli```

Second, you need to modify the networks.js file to set the node and private key to deploy the smart contract. Next execute the command to deploy. 

```$ npx openzeppelin deploy```

If you need to upgrade the smart contract then execute the following command.

`npx openzeppelin upgrade`