# Bitcoin BlockChain Certification Protocol

Bitcoin Blockchain Certification Protocol (BCP) is the technology powering bitcoin certification of equitybits. This protocol requires a middleman, which by no means have any rights of changing history (unless he carries out a 51% attack), removing the need of trusting the middleman.

This protocol is similiar to timestamping services, except that it operates with private keys and publicly shared (via the blockchain) public keys and signatures. Hence, if it is cannot be agreed that certain document is signed some point in time, and the signatures are lost, it is possible to run a (computationally feasible) brute force attempt to validate if a user signed a document.

The beauty of the protocol is despite having the need of a 3rd party node to broadcast a certification signature, the verification process can be carried out by any node in the blockchain using this protocol.

This protocol opens up possibility to various events:

* A company wants to sign share certificate with an investor.
* A landlord wants to sign a contract with a tenant online.
* A educational institution wants to approve transcripts / degrees

In all the above cases, the possibility of forging certificates cannot be ignored. Having irreversible history in the block chain eradicates the problem of fake documents.  

## Key Terms

**Node** refers to a node in the bitcoin network which can acts as signature broadcasters

**User** refers to a party who wants certificates and hence signatures broadcasted in the blockchain.

**Certificate** refers to a real document that is to be digital signed by two users. Both users should agree on the certificate before signing it.

**Signature** refers to the digital signature of the certificate.

## Cryptography

The underlying cryptography behind certification is the <a href="http://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm">ECDSA</a> and the curve secp256k1, similiar to those employed by bitcoin.

## Specification

A node will have to initiallly broadcast an initialization transaction, announcing to other certification nodes that the node will be starting to certify transactions. One of the outputs (vout = 0, specifically) will be . The concept of labelling output is inspired by colored coins.

To register a user to a node, the user should provide the node with a 33-byte compressed public key. The node will broadcast a transaction to the user's bitcoin address, which contains certain op_codes and the 33-byte compressed public key. Then, the user should publish in a financial statement / shareholder report that he will be using this address for certification.

Signing a certificate should be done offline by the user himself. Then, the user should provide a copy of the document and the signature, which are two coordinates in the finite field. The node will broadcast two transactions into the bitcoin blockchain, one with the x-coordinates and another with the y-coordinates of the signature. Note that after this point, verifying the signature of a certificate and trivially be done by any party within the P2P network.

## Spreading of Node

One of the limitations of this method is horizontal scaling. An easy way out is to create various certification nodes. This manner will however require an additional protocol to recognize such scenarios.

To solve this issue, a certification right can be divided to as many portion as possible within a transaction, with the first output being the administrator output. Hence, every bitcoin wallet which receives the certification rights output will have the right to certify new certificates. The wallet that owns the administrator output, will have the additional rights to spawn new children, terminate existing children or terminate the service as a whole.

## Rationale

By utilizing bitcoin addresses, only the certifying-node will have to be certificate aware. The user(s) will not need specified wallets to certify real-world documents. In the case the user does not have Bitcoin addresses, they can use the service with dummy addresses. However, we wish to state that such act is ** HIGHLY NOT RECOMMENDED ** as it will reduce the amount of Bitcoins in the world.

In the case a transaction broadcaster becomes non-trustworthy, he may populate an address with all possible digital signatures. However, with almost `1.39e+77` keys (accounting the possibility of the birthday attack) to brute force (only to give a 1/2 likelihood of a two way collision), it is more worthwhile for him to be a honest broadcaster a collect a (very small) broadcast fees from individuals.

## Transactions Format

The first transaction input (except for node initialization) must be a certification right input, otherwise, such transaction is considered invalid BCC transaction.

The transaction outputs are as follows :

1. Certification Rights - An output corresponding to a transaction right
2. Marker Output - An OP_RETURN output that contains specification of the transaction
3. Affected address output - A transaction of arbirtary value to signify the affected address. there is only 1 at a time.
4. Further outputs are irrelevant and considered to be regular bitcoin outputs.

The marker output (40 bytes) is in the following format:

* **namespace** - BCC 3 bytes
* **OP_CODE** - the op_code corresponding to the operation - 1 byte
* **length of message** - the length of the instruction / key / signature - 1 byte
* **message** - the signature / key / hash - 0-35 bytes

## Operations

In the current version (0x01), there are the following op_codes

<table>
  <thead>
    <th>OP Code</th>
    <th>Description</th>
  </thead>
  <tbody>
    <tr>
      <td>0x00</td>
      <td>The creation of a new certification Right</td>
    </tr>
    <tr>
      <td>0x01</td>
      <td>Binding an address to a 33 bit-compressed public key for the specific certification right</td>
    </tr>
    <tr>
      <td>0x02</td>
      <td>
        The **r** / **x-coordinate** of signatures
      </td>
    </tr>
    <tr>
      <td>0x03</td>
      <td>
        The **s** / **y-coordinate** of signatures
      </td>
    </tr>
    <tr>
      <td>0xE0</td>
      <td>Do nothing (outputs can be used to manage child-spawning)</td>
    </tr>
    <tr>
      <td>0xF0</td>
      <td>Destroying a child node</td>
    </tr>
    <tr>
      <td>0xFF</td>
      <td>Termination of service as a whole</td>
    </tr>
  </tbody>
</table>

## Powered Projects

### **<a target="_blank" href="https://www.equitybits.cc/">equityBits</a>**

The protocol is powering , a service should allows investors and companies to digitally sign and verify share certificate whilist provide certificate management services.

## Read more

This document is a mere introduction to the protocol. To learn more, check out the white paper <a target="_blank" href="https://s3-eu-west-1.amazonaws.com/equitybits/white_paper.pdf">here</a>
