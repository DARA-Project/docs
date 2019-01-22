## Introduction

Dara is a hybrid model checker which verifies properties of distributed systems written in Golang. The Dara pipeline is implemented in three distinct git repositories.

+ [godist](https://github.com/DARA-Project/GoDist) : Contains a custom version of Go v1.10, which provides the necessary interposition to schedule Go threads and capture system calls.
+ [dara](https://github.com/DARA-Project/dara) : Contains the model inference, model refinement, the abstract model checker, and trace filtering pieces of Dara.
+ [godist-scheduler](https://github.com/DARA-Project/GoDist-Scheduler) : Contains the concrete state explorer and the replay engine.

System-specific repositories (for systems we check with Dara):
+ [BTCD-applications](https://github.com/DARA-Project/BTCD-Applications) : Contains information for verifying [BTCD](https://github.com/btcsuite/btcd), a full node bitcoin implementation in Golang.
+ [Dara-ETCD](https://github.com/DARA-Project/Dara-Etcd) : Contains information for verifying [ETCD](https://github.com/etcd-io/etcd), a distributed key-value store that uses the Raft consensus algorithm.

## How to install

To be able to run Dara, you must have all the following things installed.
For doing Record/Replay, you only need to have godist and godist-scheduler installed.
It is suggested that you install them in the following order.

### Cloning Repos

Go packaging relies heavily on the GOPATH environment variable. We recommend that
all your repos should be located under the GOPATH root.
So, you should clone all repos as follows (this assumes that you have the GOPATH environment variable set) : 

```
   > cd $GOPATH/src/github.com
   > mkdir DARA-Project
   > cd DARA-Project
   > git clone https://github.com/DARA-Project/GoDist.git
   > git clone https://github.com/DARA-Project/dara.git
   > git clone https://github.com/DARA-Project/GoDist-Scheduler.git
```

### Installing GoDist

Installing GoDist (its binary name is dgo) requires that you have some version of Go (>=1.4) on your computer.

Below steps assume that you have Go 1.10 and its sources are installed on your machine at "/usr/lib/go-1.10".
To install GoDist, execute the following commands:

```
   > cd $GOPATH/src/github.com/DARA-Project/GoDist/src
   > export GOROOT_BOOTSTRAP="/usr/lib/go-1.10"
   > ./make.bash
   > sudo ln -s $GOPATH/src/github.com/DARA-Project/GoDist/bin/go /usr/bin/dgo
```

### Installing dara

Installing the Model Inference Unit (dara) requires you to have docker. Run the following commands for installation:

```
   > cd $GOPATH/src/github.com/DARA-Project/dara
   > sudo docker build -t dara/1.0 -f ./docker/Dockerfile .
```

### Installing GoDist-Scheduler

Once you have the above installed, you are ready to install the global scheduler:

```
   > cd $GOPATH/src/github.com/DARA-Project/GoDist-Scheduler
   > dgo install
```

## How to use

### Record + Replay usage

#### Environment setup

Recording and replaying requires shared memory for IPC and for the environment to be set up for communication between the global scheduler and the local Go runtimes. This is automated by a run.sh script in the GoDist-Scheduler repo: [example run.sh](https://github.com/DARA-Project/GoDist-Scheduler/blob/master/examples/SimpleFileRead/run.sh)

The same run.sh script is also used to record and replay exectuions. 

The ```Program``` variable in the run script is the name of the go file that contains the main function for running the system.

#### Record

To record the execution of a program, run the following command in the same directory as the run script and the program:

```
   > sudo -E ./run.sh -record=true
```

#### Replay

To replay the execution of a program, run the following command in the same directory as the run script and the program,

```
   > sudo -E ./run.sh -replay=true
```
