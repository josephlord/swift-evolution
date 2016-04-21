# NS_ENUMs should be considered Open

* Proposal: [SE-NNNN](https://github.com/josephlord/swift-evolution/blob/master/proposals/0000-consider_ns_enums_open.md)
* Author(s): [Joseph Lord](https://github.com/josephlord)
* Status: **[Awaiting review](#rationale)**
* Review manager: TBD

## Introduction

Enums imported from C/Objective C could (as a result of library
versioning or bugs) actually have raw values outside of the defined
values. A default case should be required on switch statements involving
them and the current undefined behaviour should be removed.

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

In cases where an enum value is outside of the expected range and there is no
default in a switch the behaviour is currently undefined (it seems to take the
first case in practice) potentially leading to runtime crashes. Undefined
behaviour and crashes at runtime due to library changes outside the developers
control are highly undesirable and Swift should be helping us to make safe
code to prevent them. At the moment if you are handling all cases it is not
even possible to add a default case.

I encountered the issue when testing on iOS 8 and hitting [an issue with CoreData](http://stackoverflow.com/a/32978387/1476206)
where it was passing a zero value the NSFetchedResultsChangeType to my code.
That thread clearly shows that the issue is affecting people and while it is
clearly a bug in the unsafe code of Core Data that is something which Swift
should be defending against. There is a simple test example showing the issue
in [SR-1258](https://bugs.swift.org/browse/SR-1258)

## Proposed solution

Redefine NS_ENUMs from being a closed type to an open one. This would then
require a default case on all switch statements over the type. With the default
case explicit situation must be handled by the developer. They can choose to
raise errors call assertionFailure or preconditionFailure as the wish or handle
the case safely.

## Detailed design

 - Help required here 

## Impact on existing code

This will break all switch statements over an enum imported from Objective-C
or C where there is no default case. There should not be any automatic
migration because it is appropriate for the developer to decide on the course
of action in each case.

## Alternatives considered

1) No change

This leaves the potential for undefined behaviour in all build modes if cases
of bugs in code outside of Swift OR in the event that new cases are added to
enums in future library versions.

2) Debug assertion

This would have the benefit that developers might discover errors more quickly
but could still leave risks where libraries (including OS frameworks) are
updated and introduce bugs after application release or in untested
configurations.

-------------------------------------------------------------------------------


