# ReadVTK.jl

[![Docs-stable](https://img.shields.io/badge/docs-stable-blue.svg)](https://trixi-framework.github.io/ReadVTK.jl/stable)
[![Docs-dev](https://img.shields.io/badge/docs-dev-blue.svg)](https://trixi-framework.github.io/ReadVTK.jl/dev)
[![Build Status](https://github.com/trixi-framework/ReadVTK.jl/workflows/CI/badge.svg)](https://github.com/trixi-framework/ReadVTK.jl/actions?query=workflow%3ACI)
[![Coveralls](https://coveralls.io/repos/github/trixi-framework/ReadVTK.jl/badge.svg?branch=main)](https://coveralls.io/github/trixi-framework/ReadVTK.jl?branch=main)
[![License: MIT](https://img.shields.io/badge/License-MIT-success.svg)](https://opensource.org/licenses/MIT)
<!-- [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5221552.svg)](https://doi.org/10.5281/zenodo.5221552) -->

With **ReadVTK.jl** you can read in data from VTK XML files in Julia. It aims to complement
the excellent package [WriteVTK.jl](https://github.com/jipolanco/WriteVTK.jl).
ReadVTK is part of the [Trixi Framework](https://trixi-framework.github.io).

*Note: ReadVTK was mainly motivated by wanting to write proper tests for
[Trixi2Vtk.jl](https://github.com/trixi-framework/Trixi2Vtk.jl).
A lot of useful features are still missing (see [below](#what-does-not-work)),
and community contributions to improve this package are welcome!*

## Usage

First, load the package with
```julia
using ReadVTK
```
Open a VTK file by creating a `VTKFile` object and passing the filename to the
constructor:
```julia
vtk = VTKFile(get_example_file("celldata_appended_binary_compressed.vtu"))
```
To retrieve information about the cell data, use
```julia
cell_data = get_cell_data(vtk)
```
The return object of type `VTKCellData` allows access to the individual
`VTKDataArray`s using a dictionary-like syntax:
```julia
element_ids = cell_data["element_ids"]
```
Finally, the actual data can be obtained by executing
```julia
data = get_data(element_ids)
```

Full example including REPL output:
```julia
julia> using ReadVTK

julia> vtk = VTKFile(get_example_file("celldata_appended_binary_compressed.vtu"))
VTKFile("celldata_appended_binary_compressed.vtu", <XMLDocument>, "UnstructuredGrid", "1.0.0", "LittleEndian", "vtkZLibDataCompressor", <appended_data>, 4434, 3085)

julia> cell_data = get_cell_data(vtk)
VTKData()

julia> element_ids = cell_data["element_ids"]
VTKDataArray("element_ids")

julia> data = get_data(element_ids)
3085-element reinterpret(Int64, ::Vector{UInt8}):
    1
    2
    3
    ⋮
 3083
 3084
 3085
```

Further example VTK files can be found in the
[`ReadVTK_examples` repository](https://github.com/trixi-framework/ReadVTK_examples).

### What works
* Reading in VTK XML files of type `UnstructuredGrid` or `ImageData`
* Extracting cell or point data
* Extracting point coordinates
* Extracting information about cell types
* Only for `ImageData` files: get origin, spacing, and extent information
* Reading `PolyData` files containing vortices, lines, and/or polygons

### What does not work
* Reading VTK files not stored in the VTK XML format
* Reading VTK files of other type than `UnstructuredGrid`, `ImageData`, or `PolyData`
* Multiblock files, PVD files
* Different byte orders in file and host system
* Probably reading from VTK files that were *not* created by [WriteVTK.jl](https://github.com/jipolanco/WriteVTK.jl) will fail, specifically since
  * compressed data is assumed to be stored as a single block
  * appended data is assumed to be stored as `raw`
  * `header_type` is hardcoded to `UInt64`
* Extracting primitives from `PolyData` files other than vortices, lines, and/or polygons
* Likely anything else that is not specifically mentioned under *What works*

## Development
Helpful resources for working with (i.e., reading and writing) VTK XML files:
* VTK file format documentation (incomplete!) as a [PDF](http://vtk.org/VTK/img/file-formats.pdf)
* VTK XML formats [wiki article](https://vtk.org/Wiki/VTK_XML_Formats)
* [Blog post](https://mathema.tician.de/what-they-dont-tell-you-about-vtk-xml-binary-formats/)
  on encoding binary data
* Mailing list [message](https://public.kitware.com/pipermail/paraview/2005-April/001391.html)
  on encoding binary data

## Authors
ReadVTK is maintained by the
[Trixi authors](https://github.com/trixi-framework/Trixi.jl/blob/main/AUTHORS.md).
Its principal developers are
[Michael Schlottke-Lakemper](https://www.hlrs.de/people/michael-schlottke-lakemper)
(University of Stuttgart, Germany) and [Hendrik Ranocha](https://ranocha.de)
(University of Hamburg, Germany).

Further contributions to ReadVTK have been made by the following people:
* [Jorge Pérez Zerpa](https://www.fing.edu.uy/~jorgepz/)
(Universidad de la República, Uruguay)
* [Ondřej Kincl](https://www2.karlin.mff.cuni.cz/~kincl/)
(Charles University, Czech Republic)

## License and contributing
ReadVTK is licensed under the MIT license (see [LICENSE.md](LICENSE.md)).
Since ReadVTK is an open-source project, we are very happy to accept contributions
from the community. Please refer to [CONTRIBUTING.md](CONTRIBUTING.md) for more details. To get in
touch with the developers, join us on Trixi's
[Slack workspace](https://join.slack.com/t/trixi-framework/shared_invite/zt-sgkc6ppw-6OXJqZAD5SPjBYqLd8MU~g)
or
[create an issue](https://github.com/trixi-framework/ReadVTK.jl/issues/new).

## Acknowledgments
This package would not exist without the nice work of 
[Juan Ignacio Polanco](https://github.com/jipolanco) and his cleanly written and well-documented package
[WriteVTK.jl](https://github.com/jipolanco/WriteVTK.jl). 
