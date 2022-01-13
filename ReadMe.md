# UnitfulMoles

[![Build Status](https://travis-ci.org/rafaqz/UnitfulMoles.jl.svg?branch=master)](https://travis-ci.org/rafaqz/UnitfulMoles.jl)
[![codecov.io](http://codecov.io/github/cesaraustralia/Dispersal.jl/coverage.svg?branch=master)](http://codecov.io/github/rafaqz/UnitfulMoles.jl?branch=master)

This package provides a set of predefined conventional elemental mol units (like `molC` for moles of carbon) and a standardised method for defining custom mol units for the Julia language.
It essentially extends the [Unitful.jl](https://github.com/PainterQubits/Unitful.jl) package.

## Conventional mol units

Units are available as `u"molXX"` for most of the elements of the periodic table (just replace `XX` with the element's symbol).

```julia
julia> using UnitfulMoles

julia> 3u"mmolFe" / 10u"molC"
0.3 mmolFe molC⁻¹
```

You can also directly load the iron mmol and carbon mol units via

```julia
julia> using UnitfulMoles: mmolFe, molC

julia> 3mmolFe / 10molC
0.3 mmolFe molC⁻¹
```

## Molecular weight conversions

Molar weights are provided for free, so you can convert from moles to grams effortlessly with Unitful's `uconvert` function (or the `|>` symbol):

```julia
julia> using UnitfulMoles

julia> uconvert(u"g", 5u"mmolFe")
0.279225 g

julia> 200u"molC" |> u"kg"
2.4021999999999997 kg
```

> **Note**:<br>
    Atomic weights taken from *Atomic weights of the elements 2013 (IUPAC Technical  Report)* [doi:10.1515/pac-2015-0305](http://doi.org/10.1515/pac-2015-0305). Weights in g are provided with 5 digit precision, using "Conventional" weights where a range is provided instead of a specific value.

## Compounds

And you can create custom compounds with the `@compound` macro:

```julia
julia> using UnitfulMoles, Unitful

julia> @compound H2O
molH₂O

julia> 10molH₂O |> u"g" # Molecular weight is calculated automatically!
180.15 g

julia> @compound CH₄ # subscripts work as well (and look nicer, too!)
molCH₄

julia> 1molCH₄ |> u"g"
16.043 g
```

## Custom mol units

For custom mol units, the main command is the `@mol` macro:

```julia
julia> using UnitfulMoles, Unitful

julia> @mol A
molA
```

This allows for stoichiometric ratios to be expressed more naturally:

```julia
julia> @mol B
molB

julia> 0.5molA/molB
0.5 molA molB⁻¹
```

## Custom molar weights

You can also assign a molar weight to the unit
to allow conversion between mol and g:

```julia
julia> @mol Foo 14.0067
molFoo

julia> uconvert(u"g", 5molFoo)
70.0335 g
```

## Compose and convert on the fly

You can use these macros in assignments:

```julia
julia> using UnitfulMoles, Unitful

julia> x = (100@compound CO2) / 25u"L"
4.0 molCO₂ L⁻¹

julia> x |> u"g/L"
176.036 g L⁻¹
```

## C-mol and others

The `@xmol` macro creates fractional moles scaled to one mole of an element in a
compound. The best example is the C-mole, which measure the amount of a compound
relative to one mole of C:

```julia
julia> using UnitfulMoles

julia> @xmol C C₈H₁₀N₄O₂
C-molC₈H₁₀N₄O₂

julia> uconvert(molC₈H₁₀N₄O₂, 1CmolC₈H₁₀N₄O₂)
0.125 molC₈H₁₀N₄O₂

julia> uconvert(CmolC₈H₁₀N₄O₂, 1molC₈H₁₀N₄O₂)
8.0 C-molC₈H₁₀N₄O₂

julia> uconvert(u"g", 1CmolC₈H₁₀N₄O₂)
24.27425 gg
```

## Use in a package

To use UnitfulMoles.jl in a package you will need to register your package with Unitful.jl,
by adding this code in the main package module.

```julia
const localunits = Unitful.basefactors
function __init__()
    merge!(Unitful.basefactors, localunits)
    Unitful.register(UnitfulMoles)
end
```

Without this you will likely have errors with using e.g. a `@compound` you define in the package.
