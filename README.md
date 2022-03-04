# leader-election

[![License](https://img.shields.io/badge/license-GPL-blueg)](https://www.gnu.org/licenses/agpl-3.0.en.html)

This library is used by Chainpoint Core to deterministically elect a peer from a list of peers, given an identical source of entropy for all peers. This method of leader election does not require excessive peer-to-peer coordination or an odd number of peers. The election does not even need to take place at the same time for all peers, so long as all peers have the same peer list and entropy- this ensures similar results in case of highly asynchronous operation or network latency.

## Install

This package requires Go modules.

`go get github.com/chainpoint/leader-election`

## Usage

 1. Ensure all peers on your network receive identical sources of entropy during your desired election period.
This can be a blockhash, a [drand](https://github.com/drand/drand) signature, or 
some other data common to all peers. 
 2. Ensure all peers have an identical list of active peers during your desired election period. Many p2p libraries maintain this for you. 
 3. Run the code below on all peers during your desired election period. 

```
package main

import (
    "encoding/binary"
    "errors"
    "math/rand"
    "reflect"
    "sync"
    "fmt"
    leaderelection "github.com/chainpoint/leader-election"
)

func main() {
    entropy := "ajdlfjad;lfkjad;kfj"
    peerList := []string{"peer", "peer1", "peer2", "peer3"}
    desiredNumLeaders := 1
    myPeerNameIs := "peer"

    // Return array consisting of desiredNumLeaders number of peers from peerList, given an entropy string
    result := leaderelection.ElectLeaders(peerList, desiredNumLeaders, entropy).([]string) 
    fmt.Println(result)
    if len(result) > 0 and myPeerNameIs == result[0] {
        fmt.Println("I am leader!")
    }
}

```
