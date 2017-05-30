# Open SDL refactoring

* Proposal: [SDL-NNNN](NNNN-open_sdl_refactoring.md)
* Author: [Alexander Kutsan](https://github.com/LuxoftAKutsan), [Alexander Galiuzov](https://github.com/AGaliuzov)
* Status: **Awaiting review**
* Impacted Platforms: [Core]

## Introduction

This proposal is to provide refactoring of existing code base, due to the continuous growing of SDL code volume and complexity.
The main architectural idea is to define in SDL basic logical components that are responsible each for its own piece of logic and redesign them to be maximally isolated and flexible.

## Motivation

The important objective is to make interfaces between such components maximally clear and intuitive to simplify further project development and maintenance. All these goals could be represented in next fundamental design principles that we should follow:
- **Efficiency** (SDL is embedded product so code performance should be the first and foremost priority)
- **Flexibility** (possibility to extend the functionality of any component without an affect on other components should be supported)
- **Extensibility** (possibility to add / remove / replace any component with another one with the same interface without an affect on other components and the whole system should be supported)
- **Testability** (during interfaces designing and development necessity of each component complete coverage by unit tests should be considered)

## Proposed solution

The proposed solution is to designed _SDL architecture_ to keep in focus following components and communications between them:
- Device (physical mobile device connected to SDL using one of supported transports)
- Connection (each device could have multiple connections through different transports*)
- Session (each connection could contain multiple sessions. In terms of HMI session is equal to application*)
- Service (each session could contain multiple services. For example audio, video, RPC services, etc.*)


## Detailed design
There is a set of components that should take a central place in our future architecture: Device, Connection, Session and Service. Besides, we need two additional components that would be responsible for receiving data from mobile transport (MobileAdapter) and sending data to HMI (HmiAdapter). Cooperation between all these new components could be approximately described with next scheme:   
![scheme_of_arch](../../nnnn-open_sdl_refactoring/assets/scheme_of_new_architecture.png)   
(arrows mean message transferring between components)

As an example, we can take a look to the scheme illustrates existing dependencies between _SDL_ components (illustration is very rough and given with only purpose to illustrate differences between two architectural approaches)   

![differences_between_two_architectural_approaches](../../nnnn-open_sdl_refactoring/assets/differences_between_arch_approaches.png)

## Impact on existing code

Potentially, we can face with the situation when one of the components will require an access to another one component with the same type (for example Connection will require an access to all Sessions to guarantee session id uniqueness). Earlier this logic could be done completely by “manager” as far as it has an access to all subcomponents, but now for such access granting we’ll need kind of superstructure that able to supervise same-type components state. All other cases when component requires an access to another component (especially to component of another type) are exceptional and should not be implemented.

## Alternatives considered

An considered alternative considered is to leave it as is.
