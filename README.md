# NBA Top Shot Smart Contracts

## Table of Contents

## Introduction

The NBA top shot smart contracts currently implement 
mold casting, moment minting, and user ownership and transfers of the moments.
By following this tutorial, you should be able to get an understanding of
how the topshot smart contracts work.  

Before you read this tutorial, you should be familiar with Flow and the 
Cadence Programming Language.  

 - [Read the Flow Primer](https://www.withflow.org/en/primer)
 - [Complete the Flow Developer Preview to learn the basics of Cadence](https://www.notion.so/flowpreview/Flow-Developer-Preview-6d5d696c8d584398a2a025185945aa5b)


## Contract Overview

All functionality and type definitions are included in the `topshot.cdc` contract.
An expanded version of the topshot contract is `topshot_expanded.cdc`. This contains
all the same functionality but has more utility and getter functions.

The TopShot contract defines five types.

 - `Mold`: A struct type that holds most of the metadata for the moments.
    All molds in Top Shot will be stored and modified in the main contract.
    A `Mold` object contains fields for its ID, the number of moments that can
    be minted from each quality, the number that can still be minted
    of each quality, and a field for all the mold's metadata. 
 - `Moment`: A resource type that is the NFT that represents the Moment
    highlight a user owns. It stores its unique ID, its quality identifier, 
    its place in its quality, 
    and the ID of the mold it references for its metadata.
 - `Collection`: Similar to the `NFTCollection` resource from the NFT
    example, this resource is a repository for a user's moments.  Users can
    withdraw and deposit from this collection and get information about the 
    contained moments.
 - `Admin`: This is a resource type that can be used by admins to cast
    new molds and mint new moments for Topshot. 
    For casting a mold, they can simply call the `castMold` function and
    provide the metadata and quality counts and the mold is created and 
    stored in the TopShot contract.
    For minting a moment, the admin can call the
    `mintMoment` function to mint a new moment that matches a mold that has
    already been created.  They can also call `batchMintMoment` to mint multiple
    moments of the same mold and quality


The contract also defines storage fields that are used in mold casting and 
moment minting.

 - `pub var molds: {Int: Mold}`: `molds` is a dictionary mapping Integer 
    IDs to the `Mold` structs that they belong to.
 - `pub var moldID: Int`: This is the number that is used for IDs in casting
    molds.  Every time a new mold is created, it gets this number as its ID
    and this number is incremented.
 - `pub var totalSupply: Int`: This is the number that is used for IDs in minting
    moments.  Every time a new mold is minted, it gets this number as its ID
    and this number is incremented. It also keeps track of the total number of 
    molds that have been minted

There are also a few functions in `TopShot` that allow anyone to get
data about molds.  These will be covered in the tutorial.

## How to Deploy and Test the TopShot Contract

The first step for using any smart contract is deploying it to the blockchain,
or emulator in our case.  

 1. Start the emulator with the `Run emulator` vscode command.
 2. Open the `topshot.cdc` file.  Feel free to read as much as you want to
    familiarize yourself with the contract
 3. The TopShot smart contract implements the NFT interface in `nft.cdc`, so you need
    to open that first and click the `deploy contract to account` button 
    that appears above the `Tokens` contract. This will deploy the interface definition
    to account 1.
 4. Run the `switch account` command from the vscode comman palette.  Switch to account 2.
 5. In `topshot.cdc`, click the `deploy contract to account` button that appears over the 
    `TopShot` contract declaration.

This deploys the contract code to account 2. It also runs the contracts
`init` function, which initializes the contract storage variables,
stores the `Collection` and `Admin` resources 
in account storage, and stores references to `Collection` and `Admin`.

Lets run a script to read some of the contract data
to make sure it was initialized correctly.

 1. Open the `verify_init.cdc` transaction.
 2. Click the `execute script` button to run the script.
 3. You should see something like:
```
DEBU[2517] "Mold ID"                                    
DEBU[2517] 1                                            
DEBU[2517] "Moment ID"                                  
DEBU[2517] 1
```
This shows that they were initialized correctly.

As you can see, whenever we want to call a function, read a field,
or use a type that is defined in a smart contract, we simply import
that contract from the address it is defined in and then use the imported
contract to access those fields.

### Casting Molds

Now lets create a mold. 
 1. Open the `cast_mold.cdc` transaction file.  
 2. Click the `submit transaction button.

This transaction uses the owners stored `MoldCaster` resource 
to cast two new molds with the `castMold` funtion.  
It also prints some of their data to 
show they were created correctly. Feel free to change some of the 
casting arguments to create different kinds of molds and to ensure that 
the contract rejects molds that don't have metadata or the correct qualities.

The `Mold` metadata field is a mapping of String to String, which means it
is a mapping of the field name, i.e "Player Name" to the value, i.e. "Lebron"
This makes it so any field can be easily accessed by calling the 
`getMoldMetadataField` function or to get all the metadata by calling the 
`getMoldMetadata` function on the contract, providing the ID of the mold.

Information about moment quantity restrictions for molds can be accessed 
by calling the `getNumMomentsLeftInQuality`, `getNumMintedInQuality`, and 
`getQualityTotal` functions.

You can also open the `verify_data.cdc` transaction file to verify that
the quantities are correct.

### Minting Moments 

Now the owner can use the stored `MomentMinter` resource to mint new moments
that reference the molds that have been created.

 1. Open the `mint_moment.cdc` transaction file and submit it.
 2. You should see the lines printed that the moments were minted successfully

You should also see `[1,2]` print, showing that you currently own the moments.

Now we can run a script that shows that we can get the metadata 
directly from a moment even if the data we are getting isn't directly stored in the moment.  
It does this because it keeps a record of the mold that it references.

 1. Open the `verify_moments.cdc` transaction file and execute the script
 2. See that it prints "Lebron" from calling a function of the moment!


### Transferring Moments

Now that you can cast molds and mint moments, you can send them to 
other accounts.  You can see the NFT example in the Flow Developer Preview
to get an idea of how they are transferred between accounts, then try to 
translate that to this context.  

This is just a basic tutorial for now, but it will be getting updated frequently to flesh out the explanations and to add more content.  Let 
Josh Hannan know if you have any questions!













