# Pushing Side Effects to the Edges

## Notes on Scott Wlaschin NDC: Functional Core, Imperative Shell

### Links
- https://www.youtube.com/watch?v=P1vES9AgfC4&t=434s
- https://fsharpforfunandprofit.com/ioedges

### Notes
- Why care?
  - I/O is not part of the domain
  - I/O is non-deterministic
  - I/O might fail
- No I/O (not possible)
  - keep I/O away from domain logic -> possible (as good as it can get)
- Models
  - Traditional model: `API -> Service -> Domain -> DB (I/O)` (and back)
  - Vertical slices: code that change together belong together 
  - Workflow: vertical slices stretched out
    - DB (I/O) at the center
  - Better: domain at the center, outside I/O
- Good and bad design
  - Good: `input -> logic -> output`
  - Bad:
    - `magic`
    - `input -> magic`
    - `magic -> output`
- Exampe of `magic`:
  - Compare numbers: interleaved I/O and decisions
    - Solution: separate the decisions from I/O
      - Represent the decision, e.g. enum
      - Pure function + I/O shell
      - Contrast:
        - easy to test
        - I/O shell replaceable (e.g. from console to DB), decisions remains the same
          - OO approach: abstract the I/O, e.g. build a hierarchy of reader/writer -> complicated
          - FP approach:
            - compose (i.e. change the shell)
            - business logic unchanged, no interface needed
            - no mock necessary
- Example: I/O heavy, e.g. update user profile (read DB check if unchanged done, if changed update DB, if email changed send verification email, etc.)
  - Solution: again model decisions
  - code documented: because everything is explicit
- Validation
  - belongs at the edges: *after* input shell but *before* the pure domain
- ORM doesn't belong here
- I/O in the middle of the workflow?
  - keep pure and I/O separated!
- Domain code smells
  - async
  - catching exceptions
  - throwing exceptions
- Perspective on "dependency"
  - Dependency is something that introduces non-determinism.
  - Everything else is not "dependency". e.g. you can have it globally, no need to inject, etc.
- Managing dependencies
  - Dependency rejection: don't have any, keep dependency away from pure domain (Mark Seeman)
  - Dependency retention: don't worry (good in many cases, e.g. scripts, ETL, data-heavy processes)
  - Dependency injection: pass in all dependencies to the class _as a whole_
    - Problem of interface creep
  - Dependency parameterization: pass _only_ the dependencies needed for a particular function/method
  - Dependency interpretation: replace calls to dependencies with "instructions" that are interpreted later
    - Separate interpreter processes the "program"
    - The interpreter does all of the I/Os   
- Summary
  - Avoid non-pure code in core domain
  - instead:
    1. Push I/O to the edges (dependency rejection)
    2. Use dependency parameterization
    3. Use interpreter pattern (complex)
   
      
