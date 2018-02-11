# PD

## Overview

An experiment in implementing a complex interaction system using Blueprint only. The primary goal is providing a framework to rapidly create and iterate on interactions of any type.

Inspiration is the skill system from Phantom Dust. These instructions assume familiarity. Phantom Dust is available for free on the Windows 10 store or XBox One market.

## Terminology

- (Activated) Skill
  - An Actor which has an Activate event. The Activate event is the entry point
  for the skill to process it's logic. Skills are dormant otherwise.

- Skill Consumer
  - Character Actor which owns and activates Skills.

- Skill Point
  - A integer value representing resource cost. A Skill Consumer has two integers
  representing a pool of resources, Current Skill Points and Maximum Skill Points.
  Skills may have an activation cost which deducts from this pool.
  Upon activation a Skill checks if it's owner's Current Skill Points is greater
  than or equal to it's cost. If the Current Skill Points is less than the cost,
  the Skill will not run the Activate event. If it is greater than or equal the
  Skill subtracts the Current Skill Points automatically. Skill Points
  automatically regenerate one per second up to the Maximum Skill Points value.

- Consumed Skill
  - A Skill which was active for it's owner and is no longer active.

- Hand
  - An array of Skills owned by an Actor which are able to trigger their Activate event.

- Deck
  - An array of Skills owned by an Actor not in the Hand.

- Discard
  - Moves the target Skill from the Skill Consumer's Hand into the Discard Pile.

- Discard Pile
  - An array of Consumed Skills.

- NullActivatedSkill
  - A Null Object stand in for a Skill. Has no cost. Implements the Activate event
  which does nothing. Use instead of `null` for a Skill. For instance, to represent
  an empty Hand, populate the Hand with `NullActivatedSkill`s instead of `null`
  objects. This allows for safety in interactions without having to check `IsValid`
  on every step.

- Target
  - An Actor that a Skill Consumer has focus on. A Skill Consumer may have only one Target. Skills receive a reference to the Target upon activation.

- Root
  - Freeze the Skill Consumer in place for the time specified. Will also freeze if the Actor is not grounded.

The Hand, Deck, and Discard definitions are general guidelines. There are no enforcements to most of the stated rules. Complex Skills may act upon them for interesting effects.

## Examples

### Creating a Skill increasing the Maximum Skill Points by three

- Create a new blueprint class with a parent of Activated Skill. Open the new class.
- Click on the Cost variable. Set it to 5.
- Open the Activate event from the event graph
- Right click on the Activate event, select 'Add call to parent'. The parent event
  checks the activation cost and automatically deducts. The parent event will not
  proceed if the activation cost is not met.
- Drag a pin off of the Activate event to the parent. Also connect the Owner pins.
- Drag a pin off of the parent Activate event. Create a sequence node.
- Drag a pin off of the first sequence node. Connect the `Set Maximum Skill Points` function.
- Drag off of the Owner from the Activate Event. Create a `Get Maximum Skill Points` node.
- Drag off of the `Get Maximum Skill Points` node. Create an `Integer + Integer` node. Set the value to 3.
- Connect the `Integer + Integer` node to the `Set Maximum Skill Points` node.
- Connect the Owner of the Event node to the Target of the `Set Maximum Skill Points` node.
- Drag a pin off of the second sequence node. Create a `Discard Skill` node.
- Connect the Target of the `Discard Skill` node to the Owner from the Activate Event.
- Right click on an empty area, create a `Reference to Self` node
- Connect the Skill of the `Discard Skill` node to the `Self` node

## Improvements

- Re-parent `Skill Consumer` as a `Actor Component` rather than a `Character`
- Convert skill types from instances to components. This allows for better composability and interesting interactions.
