

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/title_dark.png">
  <img alt="title image light / dark." src="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/title_light.png">
</picture>

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/title_light.png">
  <img alt="title image light / dark." src="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/title_dark.png">
</picture>


  <!-- <source media="(prefers-color-scheme: light)" srcset="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/title_light.png"> -->

<br></br>
# Sway bare-bones Development Environment.

In this guide we will setup a simple no fills Sway development environment, we will setup the whole thing from scratch: from installing Rust and Sways language server for VScode to the fuel tool chain. We will initialize a skeleton forc project that has a Sway main file and initialize a test harness for integration testing. A description of the conventions of basic fuel project development is given from square one and is targeted at developers getting to know the Sway landscape within Fuel.

Additionally as a simple example we will borrow [Cami's full-stack fuel example](https://github.com/camiinthisthang/fullstack-fuel), adding in the counter contract code and an integration test. This section is short as the objective of this guide is to get acquainted with the forc toolchain and Sway program development. I encourage you to read the full-stack fuel example as it gives excellent detail into the contract code line-by-line. Leave comments over at [fuel forum](https://forum.fuel.network/).

<br></br>

# The TL/DR to TL/Suld've-R:

As a new developer or even a seasoned one when coming to a new ecosystem and getting setup, its sometimes easy to get tripped up on something that doesn't install correctly, or another dependency being out of date which usually adds to frustration and is a massive time sink. The team at Fuel have spent a lot of effort making the developer experience low friction to migration, rich in tooling and well documented.

Even with the above said, the in Fuel ecosystem, its tooling and code are under rapid development, as a beginner you may come across some code examples that are a little outdated or guides that need updating from version changes. If you're finding all the links to docs, guides and how to's a little overwhelming lets start with the bare minimum background information to form a foundation to understanding the Fuel ecosystem and get you up and running quickly and into a reasonably bare bones Sway development environment. This guide is based off `forc` version `0.31.3` and is an aggregation of the important parts from a handful of setup guides in order to save you some time getting setup.




<br></br>

# Prerequisites:


## Rust:
We need Rust installed. Its not strictly necessary for the fuel toolchain to be installed but it is for the integration testing that we will be going over. Install Rust and verify the version. Im working on Ubuntu for which the set of commands to install Rust are below. Your system may be different, but follow the guide [here](https://www.rust-lang.org/tools/install) if you need. 

Ubuntu:
```
sudo apt update && sudo apt upgrade
sudo apt install curl build-essential gcc make -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Then configure your current shell with:
```
source "$HOME/.cargo/env"
```

Check the installed version:
```
$ rustup --version
rustup 1.25.1 (bb60b1e89 2022-07-12)
info: This is the version for the rustup toolchain manager, not the rustc compiler.
info: The currently active `rustc` version is `rustc 1.65.0 (897e37553 2022-11-02)`
```

So in this example at the time of writing we have rustup version 1.25.1 and the rust compiler version 1.65.0.



## Get the Sway Visual Studio Code Extension:

I'm going to assume any sane developer, presumably like yourself is going to use VScode to write the majority of their code. If you're a glass eater and want to use `vim` then I applaud you. For the rest of us normies lets just install Visual Studio code, you can get it [here](https://code.visualstudio.com/download). Once installed add the Sway extension to VScode to make life easier:

<!-- ![sway_extension_dark](docs/imgs/sway_vscode_dark.png#gh-dark-mode-only)![sway_extension_light](docs/imgs/sway_vscode_light#gh-light-mode-only) -->
<!-- ![sway_extension_light](https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/sway_vscode_light.png#gh-dark-mode-only)![sway_extension_dark](https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/sway_vscode_dark.png#gh-light-mode-only) -->

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/sway_vscode_dark.png">
  <img alt="light dark vscode." src="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/sway_vscode_light.png">
</picture>


  <!-- <source media="(prefers-color-scheme: light)" srcset="https://github.com/CatspersCoffee/Sway_Developer_Environment_Basics/blob/main/docs/imgs/sway_vscode_light.png"> -->

<br></br>

# A Little Background to the Fuel Toolchain.

To develop in Sway on the Fuel blockchain you want to have the Fuel toolchain installed. `fuelup` is the official package manager for the Fuel toolchain. The take away here is what you get with fuelup is `forc` (the `f`uel `orc`hestrator), `fuel-core` as well as a bunch of other tools.

`forc` provides a variety of tools and commands for developers working with the Fuel ecosystem, from a simple way to scaffold a new project to formatting, running scripts, deploying contracts, testing contracts, and more. `forc` is to Fuel, what `cargo` is to Rust or what `pip` is to Python. `fuel-core` lets you spin up an local Fuel node to run tests against during your Sway development.









<br></br>

# Setting Up the Development Environment

Installing the Fuel toolchain can be done through the `fuelup-init` script below. Copy and paste this into your terminal and run:

```console
curl --proto '=https' --tlsv1.2 -sSf https://fuellabs.github.io/fuelup/fuelup-init.sh | sh
```

This will install `forc`, `forc-deploy`, `forc-doc`, `forc-explore`, `forc-fmt`, `forc-lsp`, `forc-run`, `forc-wallet`  `fuel-core`, `fuel-indexer` and  `fuelup` in `~/.fuelup/bin`. The script will ask for permission to add `~/.fuelup/bin` to your `PATH`. Enter y and the installation will run:

```
Would you like fuelup-init to modify your PATH variable for you? (N/y)
```

After the install you should now have a couple new hidden directories in home `.fuelup` & `.forc`, and at the time of writing this the output message from the install was:

```
Installed:
- forc 0.31.3
- forc-explore 0.28.1
- forc-wallet 0.1.2
- fuel-core 0.15.0
- fuel-indexer 0.1.10
```

Its a good idea at this point to check that everything installed correctly. Source your `.bashrc` file in your current terminal window, or log out and log back in. What you should have in your `~/.fuelup/bin` directory is the below:
```
$ cd ~/.fuelup/bin
$ ls
forc  forc-deploy  forc-doc  forc-explore  forc-fmt  forc-lsp  forc-run  forc-wallet  fuel-core  fuel-indexer  fuelup
```

Heres a print out of my terminal when I check the version of each of the binaries (above) from my home directory:
```
$ fuelup --version
fuelup 0.14.0

$ forc --version
forc 0.31.3

$ forc-wallet --version
forc-wallet 0.1.2

$ forc-explore --version
forc-explore 0.28.1

$ fuel-core --version
fuel-core 0.15.0

...
```



<br></br>

# A bare-bones Project:

Before we build our first project lets describe how a simple development cycle works for a Sway program. The whole project uses a few different parts from the toolchain we installed earlier to compile, test and deploy etc, these include: forc, Fuel-core, Sway and Rust. 

What we have when we initialize a new forc project is a Sway main file `main.sw` in the `/src` directory, where you write the code for your Sway program (a smart contract, script, predicate or library - some info on the difference [here](https://fuellabs.github.io/sway/v0.31.3/sway-program-types/index.html)). There will also be a `Forc.toml` in the root directory which defines the project metadata like name, dependencies and other attributes of the project (Similar to a `Cargo.toml` in Rust). Sway looks very similar syntactically to Rust, however we need to be aware of a clear differentiation when we go on to testing a Sway program, don't let the build environments for `forc` and `cargo` confuse you. Once we have written some program code you may want to test. At the time of writing this Unit Testing is in development but we can write Integration tests using Fuels Sway tests template via Rusts Cargo generate crate (using the Fuel Rust-SDK). The best way to see all this in action is to do it, so lets generate a forc project and integration test skeleton project:

Move to your workspace in your development environment. To generate a new project we use the Fuel Orchestrator (`forc`) with the `"new"` command: `forc new <name_of_project>` where `<name_of_project>` is what you want to call your new forc project:

```console
forc new project-1
```
Ok great, forc should have given you some output about how to compile & test, some links to docs, community and bug reporting, and you should now have a new directory `/project-1` with the file structure:

```
.
└── project-1
    ├── Forc.toml
    └── src
        └── main.sw
```

Great! we have the bare bones of a new forc project with a skeleton Sway main file. lets build it using the `forc build ` command from the project root and see what changes in the project directory:


```
$ forc build
  Creating a new `Forc.lock` file. (Cause: lock file did not exist)
    Adding core
    Adding std git+https://github.com/fuellabs/sway?tag=v0.31.3#12ad8423811d566972dd75fbb954cdb95afde8d8
   Created new lock file at /home/catsper/fuel/workspace/project-1/Forc.lock
  Compiled library "core".
  Compiled library "std".
  Compiled contract "project-1".
  Bytecode size is 60 bytes.
```

Notice below now we have a `/out` directory, this is where the `abi.json`, the `.bin` and `storage_slots.json` file are output. We are going to reference these soon when we do some testing.
```
.
├── Forc.lock
├── Forc.toml
├── out
│   └── debug
│       ├── project-1-abi.json
│       ├── project-1.bin
│       └── project-1-storage_slots.json
└── src
    └── main.sw
```

Right now we have a program that is a skeleton, it can be built but it doesn't do anything. Before we switch our attention from the project structure and go ahead and add some contract code to the Sway main file (which we will do in a little bit below). I want to run through the setup of the test harness so we have a complete `program + testing` project. That way you have a real "bare-bones" project which you can test against a local fuel-core node.


# Setting Up the Test Harness

We're going to add a Rust integration test harness using a cargo generate template. To do this we need to make sure we have the cargo generate command installed. Its great we already have Rust installed because now we're going to use it. We need the developer tool cargo-generate which leverages the sway test template. In a terminal window run:

```console
cargo install cargo-generate
```

Once Cargo is done installing we can generate a test hardness for integration tests. In the terminal in your project root run:

```console
$ cargo generate --init fuellabs/sway templates/sway-test-rs --name project-1 --force
⚠️   Favorite `fuellabs/sway` not found in config, using it as a git repository: https://github.com/fuellabs/sway.git
🔧   Destination: /home/catsper/fuel/workspace/project-1 ...
🔧   project-name: project-1 ...
🔧   Generating template ...
[1/3]   Done: Cargo.toml
[2/3]   Done: tests/harness.rs
[3/3]   Done: tests
🔧   Moving generated files into: `/home/catsper/fuel/workspace/project-1`...
✨   Done! New project created /home/catsper/fuel/workspace/project-1
```

If you have changed the project name to something other than `project-1`, then modify the `--name project-1` part of the above.


Awesome! Now we have the project directory looking like the below, with the test `harness.rs` in the `/tests` sub directory:

```
.
├── Cargo.toml
├── Forc.lock
├── Forc.toml
├── out
│   └── debug
│       ├── project-1-abi.json
│       ├── project-1.bin
│       └── project-1-storage_slots.json
├── src
│   └── main.sw
└── tests
    └── harness.rs
```

Again so far the test harness is a just a skeleton and doesn't do much except give us a simple starting point for a test with a contract instance. You can run the test(s) using the `cargo test` command.

```console
cargo test
```

With the output:
```
running 1 test
test can_get_contract_id ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.21s
```


Lets take account of what we've done so far:

1. We've installed the requisite software (Rust, VScode).
2. installed the `forc` toolchain.
3. Initialized a skeleton forc project with Sway main file.
4. Built the sway forc project.
5. Installed the cargo test cargo-generate tool.
6. Generated a Swat Rust test harness.
7. Built and Run the skeleton integration test.







<br></br>

# Counter Contract Example:

If you haven't already checkout [Cami's full-stack fuel example](https://github.com/camiinthisthang/fullstack-fuel) with the counter contract take a look. This is what we will be basing the contract code off below.

In the `main.sw` file we have from out initialized forc project above, replace whats in `main.sw` with the counter contract code below: 

```Sway
contract;

storage {
    counter: u64 = 0,
}

abi Counter {

    #[storage(write)]
    fn initialize_counter(value: u64) -> u64;

    #[storage(read, write)]                         
    fn increment_counter(amount: u64) -> u64;       

    #[storage(read)]                                
    fn count() -> u64;                              
}

impl Counter for Contract {

    #[storage(write)]
    fn initialize_counter(value: u64) -> u64 {
        storage.counter = value;
        value
    }

    #[storage(read, write)]
    fn increment_counter(amount: u64) -> u64 {
        let incremented = storage.counter + amount;
        storage.counter = incremented;
        incremented
    }

    #[storage(read)]
    fn count() -> u64 {
      return storage.counter;
    }

}
```

build the contract with `forc build`

```
$ forc build
  Compiled library "core".
  Compiled library "std".
  Compiled contract "project-1".
  Bytecode size is 300 bytes.
```

Now copy and paste the integration tests, in the `/tests/harness.rs` file, copy and past the below:

```Rust
use fuels::{prelude::*, tx::ContractId};

// Load abi from json
abigen!(MyContract, "out/debug/project-1-abi.json");

async fn get_contract_instance() -> (MyContract, ContractId) {
    // Launch a local network and deploy the contract
    let mut wallets = launch_custom_provider_and_get_wallets(
        WalletsConfig::new(
            Some(1),             /* Single wallet */
            Some(1),             /* Single coin (UTXO) */
            Some(1_000_000_000), /* Amount per coin */
        ),
        None,
        None,
    )
    .await;
    let wallet = wallets.pop().unwrap();

    let id = Contract::deploy(
        "./out/debug/project-1.bin",
        &wallet,
        TxParameters::default(),
        StorageConfiguration::with_storage_path(Some(
            "./out/debug/project-1-storage_slots.json".to_string(),
        )),
    )
    .await
    .unwrap();

    let instance = MyContract::new(id.clone(), wallet);

    (instance, id.into())
}



#[tokio::test]
async fn can_get_contract_id() {
    let (_contract_instance, _id) = get_contract_instance().await;
}

#[tokio::test]
async fn can_inint_counter() {
    let (contract_instance, _id) = get_contract_instance().await;
    // Now you have an instance of your contract you can use to test each function

    let result = contract_instance
        .methods()
        .initialize_counter(42)
        .call()
        .await
        .unwrap();

    assert_eq!(42, result.value);

}

#[tokio::test]
async fn can_inc_counter() {
    let (contract_instance, _id) = get_contract_instance().await;

    // if the above test fails can_inint_counter(), then this test will also fail
    let result = contract_instance
        .methods()
        .initialize_counter(42)
        .call()
        .await
        .unwrap();

    assert_eq!(42, result.value);

    // Call `increment_counter()` method in our deployed contract.
    let result = contract_instance
        .methods()
        .increment_counter(10)
        .call()
        .await
        .unwrap();

    assert_eq!(52, result.value);

}
```


```
$ cargo test
   Compiling project-1 v0.1.0 (/home/catsper/fuel/workspace/project-1)
    Finished test [unoptimized + debuginfo] target(s) in 24.19s
     Running tests/harness.rs (target/debug/deps/integration_tests-b18d1acdbb52c03e)

running 3 tests
test can_get_contract_id ... ok
test can_inint_counter ... ok
test can_inc_counter ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.39s
```

Congratulations, you've just compiled and tested your first Sway contract.



<br></br>

# Fuel-core Instance

Just as a last note to wrap this guide up. If you want to start a local fuel core instance use `fuel-core run --db-type in-memory`.

```
$ fuel-core run --db-type in-memory
...(omitted)...   
2022-12-13T03:24:39.632980Z  INFO fuel_core::cli::run: 213: Fuel Core version v0.15.0
2022-12-13T03:24:39.637122Z  INFO new_node: fuel_core::service::graph_api: 111: Binding GraphQL provider to 127.0.0.1:4000
```








<br></br>

## Whats my system:

`Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-53-generic x86_64)`
