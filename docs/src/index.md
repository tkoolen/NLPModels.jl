# NLPModels.jl documentation

This package provides general guidelines to represent optimization problems in
Julia and a standardized API to evaluate the functions and their derivatives.
The main objective is to be able to rely on that [API](api) when designing
optimization solvers in Julia.

## Introduction

The general form of the optimization problem is
```math
\begin{align*}
\min \quad & f(x) \\
& c_i(x) = 0, \quad i \in E, \\
& c_{L_i} \leq c_i(x) \leq c_{U_i}, \quad i \in I, \\
& \ell \leq x \leq u,
\end{align*}
```
where ``f:\mathbb{R}^n\rightarrow\mathbb{R}``,
``c:\mathbb{R}^n\rightarrow\mathbb{R}^m``,
``E\cup I = \{1,2,\dots,m\}``, ``E\cap I = \emptyset``,
and
``c_{L_i}, c_{U_i}, \ell_j, u_j \in \mathbb{R}\cup\{\pm\infty\}``
for ``i = 1,\dots,m`` and ``j = 1,\dots,n``.

For computational reasons, we write
```math
\begin{align*}
\min \quad & f(x) \\
& c_L \leq c(x) \leq c_U \\
& \ell \leq x \leq u,
\end{align*}
```
defining ``c_{L_i} = c_{U_i}`` for all ``i \in E``.
The Lagrangian of this problem is defined as
```math
L(x,\lambda,z^L,z^U;\sigma) = \sigma f(x) + c(x)^T\lambda  + \sum_{i=1}^n z_i^L(x_i-l_i) + \sum_{i=1}^nz_i^U(u_i-x_i),
```
where ``\sigma`` is a scaling parameter included for computational reasons.
Notice that, for the Hessian, the variables ``z^L`` and ``z^U`` are not used.

Optimization problems are represented by an instance/subtype of `AbstractNLPModel`.
Such instances are composed of

- an instance of [`NLPModelMeta`](#attributes), which provides information about the problem,
  including the number of variables, constraints, bounds on the variables, etc.
- other data specific to the provenance of the problem.

## Install

Install NLPModels.jl with the following commands.
```julia
Pkg.add("NLPModels")
```
If you want the `ADNLPModel` or the `MathProgNLPModel`, you also need the
```julia
Pkg.add("ForwardDiff")
Pkg.add("MathProgBase")
```
respectively. In addition, if you want to create a `MathProgNLPModel` from a
`JuMP` model, you'll need
```julia
Pkg.add("JuMP")
```

## Usage

See the [Models](models), or the [Tutorial](tutorial), or the [API](api).

## Internal Interfaces

 - [`ADNLPModel`](@ref): Uses
   [`ForwardDiff`](http://github.com/JuliaDiff/ForwardDiff.jl) to compute the
   derivatives. It has a very simple interface, though it isn't very efficient
   for larger problems.
 - [`MathProgNLPModel`](@ref): Uses a `MathProgModel`, derived from a
   [`AbstractMathProgModel`](https://github.com/JuliaOpt/MathProgBase.jl) model.
   For instance, [`JuMP.jl`](https://github.com/JuliaOpt/JuMP.jl) models can be
   used.
 - [`SimpleNLPModel`](@ref): Only uses user defined functions.
 - [`SlackModel`](@ref): Creates an equality constrained problem with bounds
    on the variables using an existing NLPModel.

## External Interfaces

 - `AmplModel`: Defined in
   [`AmplNLReader.jl`](https://github.com/JuliaSmoothOptimizers/AmplNLReader.jl)
   for problems modeled using [AMPL](http://www.ampl.com)
 - `CUTEstModel`: Defined in
   [`CUTEst.jl`](https://github.com/JuliaSmoothOptimizers/CUTEst.jl) for
   problems from [CUTEst](https://ccpforge.cse.rl.ac.uk/gf/project/cutest/wiki).

If you want your interface here, open a PR.

## Attributes

`NLPModelMeta` objects have the following attributes:

Attribute   | Type               | Notes
------------|--------------------|------------------------------------
`nvar`      | `Int             ` | number of variables
`x0  `      | `Array{Float64,1}` | initial guess
`lvar`      | `Array{Float64,1}` | vector of lower bounds
`uvar`      | `Array{Float64,1}` | vector of upper bounds
`ifix`      | `Array{Int64,1}`   | indices of fixed variables
`ilow`      | `Array{Int64,1}`   | indices of variables with lower bound only
`iupp`      | `Array{Int64,1}`   | indices of variables with upper bound only
`irng`      | `Array{Int64,1}`   | indices of variables with lower and upper bound (range)
`ifree`     | `Array{Int64,1}`   | indices of free variables
`iinf`      | `Array{Int64,1}`   | indices of visibly infeasible bounds
`ncon`      | `Int             ` | total number of general constraints
`nlin `     | `Int             ` | number of linear constraints
`nnln`      | `Int             ` | number of nonlinear general constraints
`nnet`      | `Int             ` | number of nonlinear network constraints
`y0  `      | `Array{Float64,1}` | initial Lagrange multipliers
`lcon`      | `Array{Float64,1}` | vector of constraint lower bounds
`ucon`      | `Array{Float64,1}` | vector of constraint upper bounds
`lin `      | `Range1{Int64}   ` | indices of linear constraints
`nln`       | `Range1{Int64}   ` | indices of nonlinear constraints (not network)
`nnet`      | `Range1{Int64}   ` | indices of nonlinear network constraints
`jfix`      | `Array{Int64,1}`   | indices of equality constraints
`jlow`      | `Array{Int64,1}`   | indices of constraints of the form c(x) ≥ cl
`jupp`      | `Array{Int64,1}`   | indices of constraints of the form c(x) ≤ cu
`jrng`      | `Array{Int64,1}`   | indices of constraints of the form cl ≤ c(x) ≤ cu
`jfree`     | `Array{Int64,1}`   | indices of "free" constraints (there shouldn't be any)
`jinf`      | `Array{Int64,1}`   | indices of the visibly infeasible constraints
`nnzj`      | `Int             ` | number of nonzeros in the sparse Jacobian
`nnzh`      | `Int             ` | number of nonzeros in the sparse Hessian
`minimize`  | `Bool            ` | true if `optimize == minimize`
`islp`      | `Bool            ` | true if the problem is a linear program
`name`      | `String`           | problem name

## License

This content is released under the [MIT](http://opensource.org/licenses/MIT) License.
[![](http://upload.wikimedia.org/wikipedia/commons/8/83/License_icon-mit.png) ](http://opensource.org/licenses/MIT)

## Contents

```@contents
```
