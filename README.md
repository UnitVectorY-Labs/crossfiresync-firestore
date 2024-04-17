# crossfiresync-firestore

Example implementation of [crossfiresync](https://github.com/UnitVectorY-Labs/crossfiresync) Firestore publisher

The examples in this repository assume 3 regions are used, but this is just for demonstration purposes.  Any number of regions can be used in practice.

For this example the database names are used:

- **alpha** in `us-central`
- **beta** in `us-east1`
- **gamma** in `us-west1`

The PubSub topic name for this example is `crossfiresync`.

This repository is only for deploying `crossfiresync-firestore` the corresponding deployments of [crossfiresync-pubsub](https://github.com/UnitVectorY-Labs/crossfiresync-pubsub) must also be completed for replication to function.

## Single Region Primary

In this single region example `us-central1` is used as the primary write region where all writes are directed.

```mermaid
flowchart LR

subgraph us-east1
    crossfiresync-pubsub-east[crossfiresync-pubsub]
    beta[(beta)]
end
subgraph us-central1
    crossfiresync-firestore-central[crossfiresync-firestore]
    alpha[(alpha)]
end
subgraph us-west1
    crossfiresync-pubsub-west[crossfiresync-pubsub]
    gamma[(gamma)]
end

crossfiresync-firestore-central --> PubSub>PubSub\ncrossfiresync]

PubSub --> crossfiresync-pubsub-east
PubSub --> crossfiresync-pubsub-west

classDef function fill:#90EE90,stroke:#000000,stroke-width:2px;
class crossfiresync-firestore-central function;
```

The deployment for this example only requires deploying `crossfiresync-firestore` to `us-central1` as that is the region all writes must be directed to.

```
bash ./deploy-single-region-primary.sh us-central1 crossfiresync alpha
```

## Multi Region Primary

In the multi-region example `us-east1`, `us-central1`, and `us-west1` are all able to handle writes.

```mermaid
flowchart LR

subgraph us-east1
    crossfiresync-firestore-east[crossfiresync-firestore]
    crossfiresync-pubsub-east[crossfiresync-pubsub]
    beta[(beta)]
end
subgraph us-central1
    crossfiresync-firestore-central[crossfiresync-firestore]
    crossfiresync-pubsub-central[crossfiresync-pubsub]
    alpha[(alpha)]
end
subgraph us-west1
    crossfiresync-firestore-west[crossfiresync-firestore]
    crossfiresync-pubsub-west[crossfiresync-pubsub]
    gamma[(gamma)]
end
PubSub>PubSub\ncrossfiresync]
crossfiresync-firestore-east --> PubSub
crossfiresync-firestore-central --> PubSub
crossfiresync-firestore-west --> PubSub

PubSub --> crossfiresync-pubsub-east
PubSub --> crossfiresync-pubsub-central
PubSub --> crossfiresync-pubsub-west

classDef function fill:#90EE90,stroke:#000000,stroke-width:2px;
class crossfiresync-firestore-central,crossfiresync-firestore-east,crossfiresync-firestore-west function;
```

The deployment for this example requires deploying `crossfiresync-firestore` to all 3 regions.

```
bash ./deploy-multi-region-primary.sh us-central1 crossfiresync alpha
bash ./deploy-multi-region-primary.sh us-east1 crossfiresync beta
bash ./deploy-multi-region-primary.sh us-west1 crossfiresync gamma
```
