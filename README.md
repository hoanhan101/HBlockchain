# blockchain-db

![build-success](https://img.shields.io/badge/build-success-brightgreen.svg)
![test-passing](https://img.shields.io/badge/test-passing-brightgreen.svg)
![status-stable](https://img.shields.io/badge/status-stable-green.svg)

> Blockchain + MongoDB = BlockchainDB, aka blockchain-db

**blockchain-db** is Blockchain's implementation. It uses MongoDB as a database
management system to store blocks' metadata. Other interesting technical
details such as merkle tree, nonce, difficulty, and transactions are also
implemented and documented in the source code.

![Demo](demo.png)

## Project Status

It is done for the most part. Below are ideas for future implementations.

### Tasks

- [x] Model Bitcoin's blockchain structure 
- [x] Implement Proof of Work, nonce, difficulty
- [x] Enable transactions
- [x] Implement Merkle Tree
- [x] Introduce mining mechanics 
- [x] Introduce block reward
- [x] Remember elapsed time and hash power metrics for later analytics
- [x] Expose RESTful APIs, with simple UI
- [x] Store the full chain in mongodb

### Ideas

- [ ] Dockerize the project
- [ ] Introduce networking with multiple nodes. For now, it only works with one node, which is the local host
- [ ] Introduce Wallet
- [ ] Introduce Attack

## Table of Contents

- [Files and structure](#files-and-structure)
  - [blockchain_db.py](#blockchain_dbpy)
    - [Block structure](#block-structure)
    - [Methods](#methods)
    - [GET methods](#get-methods)
  - [blockchain_db_server.py](#blockchain_db_serverpy)
    - [APIs](#apis)
- [Usage](#usage)
  - [With networking](#with-networking)
  - [Without networking](#without-networking)

## Files and structure

### blockchain_db.py

This file contains the main BlockchainDB logic. I am using `pymongo` to connect with mongodb
database named `blockchain` and the `block` collection. Whenever a new bock is mined, it will
write to the database.  

#### Block structure

```python3
block = {
    "previous_block": <str>,
    'height': <int>,
    'timestamp': <unix time>,
    'transactions': <list>,
    "merkle_root": <str>,
    'number_of_transaction': <int>,
    'nonce': <int>,
    'previous_hash': <str>,
    'block_reward': <int>,
    'difficulty_bits': <int>,
    'difficulty': <int>,
    'elapsed_time': <int>,
    'hash_power': <int>
}
```

#### Methods

Here are the main methods. Details are well documented in doctrings.

- `generate_genesis_block()`
- `generate_next_block(nonce, previous_hash=None)`
- `add_transaction(sender, recipient, amount)`
- `find_merkle_root(transaction_ids)`
- `mine_for_next_block()`
- `calculate_nonce(last_block, number_of_bits)`
- `calculate_block_reward()`
- `calculate_difficulty_bits()`
- `calculate_difficulty()`
- `hash_json_object(json_object)`
- `hash_string_pair(string_1, string_2)`

#### GET methods

- `get_length()`
- `get_last_n_blocks(number)`
- `get_top_blocks(state, number)`
- `get_last_block()`
- `get_genesis_block()`
- `get_block(height)`
- `get_all_blocks()`
- `get_transaction_ids()`

### blockchain_db_server.py

This file uses *Flask* to serve as a web page. 

#### APIs

Endpoint | Description
--- | ---
`/reset` | Drop the database and create a genesis block
`/mine/<int:number>` | Mine a number of blocks over network
`/view/chain` | View the whole blockchain
`/view/last_blocks/<int:number>` | View some number of last mined blocks
`/view/last_block` | View the last mined block 
`/view/genesis_block` | View the genesis block
`/view/block/<int:number>` | View a specific block
`/view/top/<int:number>/<string:state>` | View top numbers of blocks for a given state

## Usage

#### With networking

- In `src`, start `blockchain_db_server.py` and visit `localhost:5000`
- Hit `/reset` endpoint to create a genesis block. This endpoints can also be used to drop the database
and start over whenever you want to.
- Mine some number of blocks at `/mine/<int:number>`
- Use available `/view` endpoints as mentioned above for more details.

#### Without networking

- Use `blockchain_db_test.py` to create an instance of BlockchainDB to mine some blocks.
- Execute `reset()` only once when you start to drop the old database and create a genesis block.
- Comment it out after the second run and try to mine some blocks with the provided testing script.
- Similar to the first option, start `blockchain_db_server.py` in `src` to serve as a web page and view the result on the web
or just print it using the console.  
