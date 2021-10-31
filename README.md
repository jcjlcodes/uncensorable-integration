# Integration guide for [Uncensorable](https://uncensorable.art)

## What?

Uncensorable is digital bulletin board stored on the Polygon blockchain. Our client is available at https://uncensorable.art.

Data stored on the blockchain cannot be deleted or modified. It is immutable, and therefore uncensorable.

Every statement becomes an ERC-721 NFT, fully stored on chain. These can be read and displayed by anyone (for example, but not limited to, this webpage.)

See for example our [OpenSea collection](https://opensea.io/collection/uncensorable). Learn more at https://uncensorable.art/about

## Interacting with the Uncensorable contract

### Contract addresses

Verified contracts with full source code at Polygonscan:

- Polygon Mainnet [0x0fB864362ACEDEe1AbC15470D732F1C5397978cc](https://polygonscan.com/address/0x0fB864362ACEDEe1AbC15470D732F1C5397978cc)

### How is NFT data stored?

There are two methods for modifying NFT state: `post(bytes calldata payload)` and `boost(uint256 id)`. They emit the events 

```
Post(uint256 indexed id, address indexed from, uint256 power)

Power(uint256 indexed id, address indexed from, uint256 power)
```

The `payload` is a compressed binary representation of the post data, described in the next section. To access the payload for a post:

1. Find the transaction for the post by finding the corresponding `Post` event (using `eth_getLogs` with the appropriate filters).
2. Get and ABI decode the transaction input data using `eth_getTransactionByHash`.

### Binary payload format

The post payload is a DEFLATE (zlib) compressed [protobuf](https://developers.google.com/protocol-buffers) with the format [proto/post.proto](proto/post.proto):

```protobuf
message Post {
    string message = 1;
    
    message Image {
        bytes bytes = 1;
        string content_type = 2;
    }
    Image image = 2;
}
```
