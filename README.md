BezierCurve
==========

Overview
--------
This repository contains a small, self-contained HTML + JavaScript demo for drawing an n-degree Bezier curve on an HTML5 canvas. The user can add, remove, and edit Bezier control points either by:
- clicking the canvas to add a point at the mouse position,
- dragging existing points directly on the canvas,
- typing numeric X/Y values in input fields.

The program redraws the curve and the control polygon immediately after each change.

Files
-----
- Bezier_curve.html
  Single-page app containing the canvas, UI controls, and all JavaScript logic.
- README.md
  Minimal repository README.

How to run
----------
1. Download or clone the repository.
2. Open "Bezier_curve.html" in any modern web browser (no build step, no server required).

What is drawn
-------------
The visualization consists of:
- Control points (red circles): the points P0..Pn that define the curve.
- Control polygon (light gray polyline): straight segments connecting P0->P1->...->Pn.
- Bezier curve (blue polyline): a sampled approximation of the true Bezier curve.

Mathematical approach
---------------------
Given n+1 control points P0, P1, ..., Pn (each Pi = (xi, yi)), the Bezier curve B(t) for t in [0, 1] is computed using the Bernstein polynomial form:

B(t) = sum_{i=0..n} [ C(n,i) * (1 - t)^(n - i) * t^i * Pi ]

Where:
- C(n,i) is the binomial coefficient ("n choose i")
- the scalar term  C(n,i) * (1 - t)^(n - i) * t^i  is the Bernstein basis polynomial weight
- Pi is a 2D vector control point

In code, x(t) and y(t) are computed separately using the same weights:
x(t) = sum_{i=0..n} weight_i(t) * xi
y(t) = sum_{i=0..n} weight_i(t) * yi

Sampling / approximation
------------------------
The curve is rendered by sampling t in uniform steps (default step = 0.001) and connecting the resulting points with straight line segments. This produces a polyline approximation of the curve.

Binomial coefficient optimization
---------------------------------
To avoid recomputing binomial coefficients inside the t-loop, the program:
1) precomputes C(n,i) for i=0..n once per redraw, and
2) reuses those coefficients while iterating over t.

The function getBinomialCoefficient(n, k) computes "n choose k" iteratively, using the symmetry:
C(n,k) = C(n,n-k)
to reduce the number of multiplication/division steps.

User interaction summary
------------------------
- "Add Bezier point" button: adds a new control point with default coordinates (0,0).
- Click on canvas:
  - if close to an existing point (within a click tolerance), start dragging it
  - otherwise add a new point at the clicked coordinates
- Dragging:
  - updates the selected point coordinates as the mouse moves
  - coordinates are clamped to the canvas boundaries

Notes / limitations
-------------------
- The rendered curve is a sampled polyline (not an exact analytic curve drawing).
- Very small step sizes increase smoothness but cost more CPU per redraw.
- The current implementation uses Math.pow for Bernstein weights; for large n, numerical issues may appear (common for high-degree Bezier curves).
