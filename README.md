DAAAT: Dependencies-to-AMR Alignment Annotation Tool
====================================================

Nathan Schneider  
2016-06-05

This tool, currently in the prototyping phase, is intended to support manual annotation in a web browser of alignments between a syntactic dependency parse and an Abstract Meaning Representation (AMR) annotation.

Currently, the tool is implemented in HTML/Javascript/jQuery.

Input
-----

The AMR annotation is in *tree* form, rather than the graph that it represents, because it is believed that human AMR annotators tend to follow aspects of the syntactic structure in constructing the annotation that would be lost by normalizing graph-equivalent annotations. Such aspects include the order of a node's non-core semantic dependents and the choice of where to put the primary, instance-labeled occurrence of a reentrant variable. (TODO: when does AMR editor normalize order?)

The dependency parse consists of the words of a sentence and directed arcs (edges) between pairs of words. Each relation has an associated label (type); we refer to the labeled arc as a *relation*. The parse is typically a rooted tree, but that is not a requirement: some words may have multiple parents, as in the enhanced/non-basic versions of Stanford and Universal Dependencies. (At present, we do not distinguish basic and non-basic relations.)

Alignment formalism
-------------------

A simple formalism is currently assumed for alignment: each alignment consists of a pair of *units* between the dependency parse and the AMR.

  - The *units* in a dependency parse are its **words** and **relations**. A word can be aligned independently of the relations it participates in.
  - The *units* of an AMR are:
    * **Primary nodes**, consisting of a variable, slash, and concept of which the variable is an instance. E.g., `b / boy` is one primary node, and `e / eat-01` is another.
    * **Reentrancies**, in which a variable whose concept is defined elsewhere is reused, not followed by a slash. E.g., in `(e / eat-01 :ARG0 (b / boy) :ARG1 (c / cake :poss b :quant 3))` ("the boy ate his 3 cakes"), the bare `b` following the `:poss` is a reentrancy.
    * **Constants**, including strings within names, as well as numeric values. `3` is a constant.
    * **Relations**, which connect a primary node to a primary node, a reentrancy, or a constant, via a role label. In the above example, the `:ARG0`, the `:poss`, and the `:quant` are the labels for the three relations.
      * (TODO: We may want to collapse special constants that are relation-specific. Currently, this is done for constants `+` and `-`: thus, `:polarity -` is treated as one unit.)

In addition to the above, we introduce **pseudo-units** of both the dependency parse and the AMR. This is a trick to allow explanatory labels for null alignments. The pseudo-units are listed below the respective structures and begin with `!`: `!art` to mark non-alignment of articles, `!amr-error` to flag a unit of the dependency parse that cannot be aligned due to an error in the AMR, etc.

Only (pseudo-)units as defined above may be aligned (they cannot be subdivided, and other material, such as parentheses indicating the structure, is not included).

Currently, each alignment must include exactly one dependency unit and one AMR unit. However, it may be useful in the future to think of alignments between sets of units, or sets of alignments that are tied together.

The current formalism allows **many-to-many** alignments: there are no restrictions on how many or which units of the AMR that a dependency unit aligns to, or vice versa.

The current formalism does not assign labels to alignments, nor are duplicate alignments (between the same pair of units) allowed.

User interface
--------------

The elements of the UI are: the sentence at top; the dependency parse, displayed in (LISP-like) Pennman notation at left; and the AMR annotation, displayed in Pennman notation at right as output by the AMR Editor.

Hovering over a word in the sentence color-codes the corresponding word in the parse, and vice versa. Hovering over a relation in the dependency parse color-codes the head word in red and the modifier in blue in both the parse and the sentence.

Alignments are created by first activating one or more units in the dependency parse, by clicking so they have a green background; then clicking one or more units in the AMR, so they have a yellow background. (Clicking a unit toggles its status. Once an AMR unit has been clicked, clicking on an inactive dependency parse unit deactivates the other units in order to begin a new alignment.) The same procedure is used to remove alignments.

Existing alignments are printed at the bottom of the screen and can be visualized by hovering over their respective units. Black text is used to indicate that a unit is unaligned (units involved in at least one alignment are gray by default).

Implementation status
---------------------

Currently, only the user interface is implemented, and only for a hardcoded example. The following steps remain before this can be a full-fledged annotation tool:

  - [ ] Fine-tuning the existing UI (colors, click interactions)
  - [ ] Possibly adding graphical views of the parse (such as with brat) and AMR
  - [ ] Adding keyboard commands
  - [ ] Considering extensions to the alignment formalism, such as:
    * set-to-set alignments: activating multiple dependency parse and/or AMR units at once would no longer be equivalent to multiple pairwise alignments
    * labeled alignments: assign a string label or color code to each alignment
    * multipart alignments: where multiple pairwise (or set-to-set) alignments can be bundled together as one "rule"
  - [ ] Consider more sophisticated unit-finding heuristics
  - [ ] Revising the available pseudo-units
  - [ ] Adding additional UI elements, such as a free text comment field
  - [ ] Implementing a backend for loading (parse,AMR) pairs and saving the alignments
  - [ ] Implementing extraction of unit IDs from the raw parse and AMR structures (not sure if this should be in the frontend or backend)
  - [ ] Implementing a way to navigate between sentences in a dataset
  - [ ] Considering support for loading automatic alignments for manual revision, and/or heuristics for easy alignments (such as `"the" to !art`)
  - [ ] Possibly constraining the use of pseudo-units (e.g., no real unit can be aligned to multiple pseudo-unit; pseudo-units cannot be aligned to one another)
  - [ ] Possibly requiring all units to be aligned (to a unit or pseudo-unit) for the sentence to be completed
  - [ ] Finalizing the format of the printed alignments

### Long-term possibilities

  - [ ] Ability to edit sentence segmentation, tokenization, dependency parse, and/or AMR
  - [ ] Integration within the AMR Editor
