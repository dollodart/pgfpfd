# Description

This LaTeX package defines nodes and streams for drawing chemical engineering
PFDs using the Tikz/PGF LaTeX package. The Tikz frontend for PGF provides the
scaling, shifting, and positioning (alignment) operations needed. While the
Tikz pic could achieve many of the same functions, it isn't as low-level and
therefore extendable, and in particular it cannot be positioned by its anchors.
One could define a pic as a box and place it inside a node which could be
positioned at least by the node's anchors.

Positioning (positioning-plus) and edge routing (paths.ortho) can be made
easier to specify by using the Tikz libraries at
https://github.com/Qrrbrbirlbel/pgf. The same functionality can be achieved in
a more verbose way by the general method of defining coordinates in terms of
other coordinates using `\path let` and positioning relative to them or drawing
between them.

Though intended for graphs with simple node shapes, the tutorial in Section 5
of the Tikz/PGF manual is helpful for those wanting to create their own
flowsheets.

# Planned Features

## Stream crossing

I have never seen a PFD that is non-planar, but they often have streams cross
anyways because of the fixed positions of ports on the unit operations.
A jump-over for stream crossing can be implemented just by using a background color
of white for one of the streams, so long as that flow is drawn later or on a
higher layer than the streams it crosses. Stream crossing using on intersection
library to make some change to the path (like a semicircle at the crossing) can
also be done.

## Improved Node Definitions

- Outer separation, for variable linewidths on unit operations, should be
  supported.  For edges of the nodes which are perpendicular to the x- and
  y-directions, this is just adding an increment equal to the outer sep.
  Otherwise some computations for adjusting anchor position are needed. At least
  for polygons, it's possible to define a general algorithm for the separation
  needed for a line width by extending the coordinate along a perpendicular
  bisector to the edges it is on a fixed width, though the two calculated
  adjustments will generally be mismatched. This is done at
  https://github.com/dollodart/dev-cs for conformal layers with matching to the
  larger coordinate. That matching, or smoothing of discontinuities between
  paths, is anyways done at a low level in PGF.
- Improve anchor border, which is currently using just the bounding shape.
  This would rarely be used because there are well-defined ports for these unit
  operations. All node shapes are simple polygons, even if they have non-linear
  parts like arcs. As a result, the general algorithm of evaluating the angle
  incoming (relative to the center of the node) to determine what segment (or
  arc) to calculate an intersection with can be used. In fact, the anchor border
  would just be defined as the intersection with the segment that is farther out
  if the shape were not simple.
  
## Automatic orthogonal edge routing

There is an expansive field of literature on this. At the global level a LuaTeX
solution can be made which is optimal, doing things like avoiding edge crossing
and bundling parallel edges. That would effectively be implementing what is
already in the C++ libraries of the Dunnart program
(https://github.com/mjwybrow/dunnart). However, simpler pairwise optimal
solutions can be implemented in PGF (that is, TeX). There is already the Tikz
library paths.ortho at https://github.com/Qrrbrbirlbel/pgf. It is
semi-automatic because the directions have to be specified, but can be extended
to infer the directions the relative positions of the ports to each other and
their respective nodes' anchor borders or bounding boxes. Currently it is
limited to NSEW on square (or circle or similarly simple geometry) nodes but
can be extended to go between arbitrary ports. The value of Tikz/PGF is precise
control of the appearance, except maybe for automatic orthogonal graph creation
on large numbers of (generally simple) nodes, which is not the use case for
PFDs. But some users may still like to simply specify what ports should be
connected at least for a subset of the streams.
