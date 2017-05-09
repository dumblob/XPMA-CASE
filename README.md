# XPMA Modeler

*CASE done right!*

Computer Aided Software Engineering tool implementing the *XPM combined model* and providing additional convenient read-only views of the resulting model (e.g. Entity Relationship Diagram, Use Case Diagram). XPMA CASE produces input for [XPMA](https://github.com/dumblob/XPMA ).

FIXME Screen sizes table
all UIs are touch-friendly and HW-keyboard-input-friendly
mobile ("square" smartphones, ...); NO tiny devices like watches or wearables
tablet (tablets, netbooks, ...)
desktop (notebooks, desktops, ...)
FIXME development strategy: code and architecture must be MVP (with few explicit exceptions like threading in Redis instead of processes) - everything which can be done later manually should be annotated with "FIXME do automatically" and left for the time being with a stub forcing one to do it manually
FIXME use Android auto user-feedback: http://www.acra.ch/

### Functional requirements

#### Minimal Viable Product

- multiplatform (Windows, Mac OS X, Linux)
- support full XPM 2.0
    - supports full CSDDM 2.0
    - supports the BPMN 2.0 subset defined in the XPM 2.0 specification
- when adding new records, default values of all properties will be sane and handy (should consider also surrounding data scheme)
    - the goal is to minimize number of needed changes
- export to a BPMN 2.0 compatible format with extensions
    - make sure BPMN 2.0 implementations will ignore the extensions
- import the BPMN 2.0 model format
- load XPM engine profile (i.e. info about capabilities of the particular XPM engine)
    - CSV? or rather some tree format like XML/JSON/...?
- interactive hints
    - when writing already known IDs (e.g. record names, process names)
- switching between overall views based on tags
    - all-view
    - business view
    - ...
- partial support for live view of the generated system at the selected point with the selected data
    - needed for smooth testing experience
    - [ekam](https://github.com/sandstorm-io/ekam ) might be useful (either directly or just as an inspiration)

#### Next Generation Version

- maybe a different implementation technology
- hints and warnings and error messages based on consistency rules
- supports read-only views
    - read-only, because these models do not support all features of XPM 2.0
        - e.g. adding a new attribute to the ERD view would make it "hidden", because
            - there is no connection to the rest of the model (e.g. not used in any task)
            - it's by default `no_rw`
    - Use Case Diagram
    - ERD
        - click-through on ERD gets one to the record definition and/or references
- support for diff
    - maybe different levels (not just a precise diff)
    - process similarity
        - graph & loop similarity
        - task similarity
    - data similarity
        - structural similarity (ignore all properties - names, constr, etc.)
    - precise diff
