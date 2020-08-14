# Celo DID Method Specification

## Summary
[summary]: #summary

Decentralized identifiers (DIDs) are a new type of identifiers that enables verifiable, self-sovereign digital identity. This Celo DID method specification describes a new DID method, that is, Celo DID and defines how Celo blockchain stores Celo DIDs and their corresponding DID documents, and how to do CRUD operations on Celo DID documents.

This specification conforms to the requirements specified in the [DIDs specification](https://www.w3.org/TR/did-core/) currently published by the W3C Credentials Community Group.


## Celo DID Method Name
[did-method-name]: #did-method-name

The namestring that shall identify this DID method is: `celo`.

A DID that uses this method **MUST** begin with the following prefix: `did:celo`. Per this DID specification, this string **MUST** be in lowercase.

The remainder of the DID, after the prefix, is its namespace-specific identifier specified below.


### Namespace Specific Identifier (NSI)
[namespace-specific-identifier]: #namespace-specific-identifier

The namespace specific identifier is defined by the following ABNF:
```
celo-did   = "did:celo:" celo-specific-idstring
celo-specific-idstring = 40*40HEXDIG
```

### Example
[example]: #example

A valid Celo DID might be:
```
did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De
```

## CRUD Operations
[crud-operations]: #crud-operations

The following section outlines the DID operations for the `did:celo` method.

Celo DIDs reside on the Celo blockchain, and are managed via the Celo DID management smart contract.

For the sake of convenience, we refer to the Celo DID management smart contract as 'the registry'.

### Create (Register)
[create]: #create

The Celo DID creation is implicit and it does not reqiure any interaction with the registry.

A subject who has a Celo address need not invoke any method and will automatically own a Celo DID that concatenating "did:celo:" and the Celo address without a `0x` prefix.

For instance, Alice, who has a Celo address `0x1f4B9d871fed2dEcb2670A80237F7253DB5766De`, will automatically become the subject of `did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De`.


### Read (Resolve)
[read]: #read

Celo DID's associated DID document can be looked up by invoking the `getDocument` method of the registry.

To ensure the smart contract invocation result is trustworthy, the client could query a certain number of nodes and then compare the return values or deploy its own node.

The interface method for resolving a Celo DID document is defined as follows:

```
get_document()
```

Besides this full-fledged resolver, the Celo blockchain provides other simple resolvers, such as fetching the `authentication` property.

#### Celo DID Document Example
[did-document-example]: #did-document-example

```json
{
  "@context": ["https://www.w3.org/ns/did/v1"],
  "id": "did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De",
  "publicKey": [
	{
	  "id": "did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De#keys-1",
	  "type": "EcdsaSecp256k1VerificationKey2019",
	  "controller": "did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De",
	  "publicKeyHex": "0xfbf38de9fb40edcdab412094d24fa39a314f3d3f52f5860e2509c32522eda30161fe70dfc9f90434d64bd976ede4f112d4f2d8e34d28fe48281663219d2ddac6"
	}
  ],
  "authentication": [
	"did:celo:1f4B9d871fed2dEcb2670A80237F7253DB5766De#keys-1"
  ]
}
```

### Update (Replace)
[update]: #update

To update a Celo DID document, the corresponding Celo DID subject just need to invoke relevant functions.

For instance, the Celo DID subject can invoke the `addController` method to add a delegate which has the authorization to insert a new verification method into the `authentication` property of the delegated Celo DID.

The interface method for adding a delegate Celo DID is defined as follows:

```
add_controller(delegate: String)
```

The `delegate` parameter specifies the to-be-added controller.

Similarly, the interface method for removing a delegate Celo DID is defined as follows:

```
remove_controller(delegate: String)
```

The `delegate` parameter specifies the to-be-removed controller.

Here we do not provide the full list of supported update methods and will provide specific documentation which lists all of the related APIs.

### Delete (Revoke)
[delete]: #delete

To delete (or deactivate) a Celo DID, it suffices to remove all the verification methods from its associated DID document and set a flag in the registry to indicate the DID is deactivated. In this case, there is no authentication method that can be used to authenticate the holder's identity.

The interface method for deactivating a Celo DID document is defined as follows:

```
deactivate_did(did: String)
```

The `did` parameter specifies the to-be-deactivated Celo DID.

More importantly, the deletion of a Celo DID implies this DID cannot be registered or reactivated again.

## Security and Privacy Considerations
[security-and-privacy-considerations]: #security-and-privacy-considerations

There are several securities and privacy considerations that implementers would want to take into consideration when implementing this specification.

The current Celo DID implementation does not allow a Celo address to have multiple Celo DIDs, and if the Celo DID is deactivated, the corresponding Celo address cannot access the deactivated Celo DID. Hence, it loses all capability to perform operations on that Celo DID.

Since the delegates specified in the `controller` property can change the value of `authentication`, they have the same privileges as the DID subject.

Celo DID documents should be limited to verification methods and service endpoints, and should not store any personal information.

## Reference Implementations
[reference-implementations]: #reference-implementations

The reference implementation is available here: https://github.com/ontology-tech/DID-solidity/tree/celo-did

## References
[references]: #references

[1]. Celo, https://celo.org/

[2]. W3C Decentralized Identifiers (DIDs) v1.0, https://w3c.github.io/did-core/
