# CompartmentalModelServer.jl

Demonstration of how to deploy an API server that interacts with a system of
differential equations.

## Purpose

Creating a model is half the battle. Often models must be deployed to production
in order to facilitate consumption by other team members or end users.To
maximize effectiveness, the model should be easily accessible without prior
knowledge of the production environment. One method which may cover several use
cases is to spin up an API server which acts as a sort of _simulation engine_
using the final model. Consumers can query the server to gain answers about a
particular simulation of interest. This package explores how to build this
server using [Oxygen.jl](https://ndortega.github.io/Oxygen.jl/stable/)
to program the API endpoints and
[StructTypes.jl](https://juliadata.github.io/StructTypes.jl/stable/)
to customize serialization/deserialization from Julia objects to JSON payloads.

## Model

Compartmental models are widely used in many areas including epidemiology. The
model used in this demonstration is the one of the simplest compartmental models
which is used to derive many others, [the SIR
model](https://en.wik_models_in_epidemiology).

## Structure

There are 3 main parts:

1. Source (`src/`) where we define:
    - the model itself,
    - methods for serialization/deserialization,
    - helper functions for solving new simulations.
1. Server (`bin/`) where we define:
    - the API server,
    - entry point to run the server is `bin/main.jl`.
1. Tests (`test/`) where we define unit tests.

## FAQ

### Module Initialization

Initialization of the main module occurs immediately after the module is loaded.
The function body of the `__init__` method defines how the module gets
initialized. The benefit of defining `__init__` is that once the module is
loaded, calling its functions such as `simulate` is fast. The down-side is that
loading the module can take a bit longer. Since this package's main use case is
to run as a web server, it is assumed that startup time is not critical. Most
likely the server is a long running job where code loading and startup time are
only incurred once. Therefore, module Initialization is acceptable.

### Server Host

By default, most web serves (including `Oxygen.jl`) run on the normal loop back
address, localhost or equivalently `127.0.0.1`. However, on JuliaHub a web
server host should be set to `0.0.0.0`.
