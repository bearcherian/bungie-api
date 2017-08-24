# The Bungie.Net API

Full Documentation: https://bungie-net.github.io/multi/index.html

Documentation as one single, gigantic page: https://bungie-net.github.io

This section will be added to as time goes by - we're working with it as we go!

# Swagger/OpenAPI Specs

- Grab openapi.json for the OpenAPI 3.0.0 specs
- Grab openapi-2.json for the 2.0.0 specs

# Extension Properties on OpenAPI Specs

- x-mapped-definition = This property has hash identifiers that map to a Manifest Database definition.  This property will reveal which type of Manifest Database definition it points to.  If it's a uint32, it points to a single definition.  If it's a List<uint32>, each value points to a different definition.  If it's a Dictionary<uint32, X>, its' keys point to different definitions.

- x-mobile-manifest-name = If populated, this is the name of the Mobile Manifest table that holds this data as a first-class entity.  The existence of this field implies that this entity is a first-order Destiny Definition aggregate.

- x-enum-values = I really hate that the OpenAPI spec doesn't have a way to tell you both the identifier and the numeric value of an enum, much less documentation for enum values, so I added this extension as a way to return that info.  It'll give you way more than the basically useless base "enum" property.

- x-destiny-component-type-dependency = A new concept in the Destiny 2 API is "Components".  You will see that our Destiny Profile/Character calls have been mostly simplified down to just GetProfile/GetCharacter/GetItem.  This simplification is made possible by Components, which are identifiers you pass into the requests to specify how much data you want back.  An entity with this property will only be returned if you've passed the named Component Identifier into the GetProfile/GetCharacter/GetItem methods.

- x-dictionary-key = OpenAPI didn't have a good way for me to specify that the key of a dictionary was actually meant to be something other than a string (even if always encoded as a string in JSON).  This extension property lets me tell you what it *really* is, so you can strongly type it and convert it if desired.

- x-preview = This boolean indicates that the Operation in question is not yet ready for prime time.  You will have to read the documentation to find out just *how* not ready it is, at least for now.

# What's new in the API?

- ### Component-based Destiny Requests
  - [Destiny2.GetProfile](https://bungie-net.github.io/multi/operation_get_Destiny2-GetProfile.html#operation_get_Destiny2-GetProfile), [Destiny2.GetCharacter](https://bungie-net.github.io/multi/operation_get_Destiny2-GetCharacter.html#operation_get_Destiny2-GetCharacter), and [Destiny2.GetItem](https://bungie-net.github.io/multi/operation_get_Destiny2-GetItem.html#operation_get_Destiny2-GetItem) are now the fundamental building blocks of Destiny data, replacing our large and disparate specialized endpoints.
  - GetCharacter and GetItem are provided for situations where you know the specific character or item you're looking at in your UI, but we fully anticipate that most calls will be made through GetProfile.  At any layer of depth, you can now get any data for the layer below by passing the components you want returned in the ?components= querystring parameter of the method.  (NOTE: I would link to DestinyComponentType here, but I just noticed it's not being output yet.  I will fix that in the morning.  In the meantime, you can infer the components by examining the Response objects from the methods linked above)
  - For example, you can pass the CharacterInventories component to GetProfile, and you will receive the full inventory for all characters (provided that they have granted you permission to access that data, in the same way as in Destiny 1: our privacy policies and settings have not changed since D1, and still apply to D2 data).
  - In this way, my hope is that we can reduce the number of round trips you're currently having to make, while letting you control how much bandwidth you use up.  Ask for only the components that you need!

- ### Milestones
  - Advisors have disappeared, but in their place are ["Milestones"](https://bungie-net.github.io/multi/schema_Destiny-Definitions-Milestones-DestinyMilestoneDefinition.html#schema_Destiny-Definitions-Milestones-DestinyMilestoneDefinition).  Mirroring the game's concept of Milestones, these reflect all of the activities that a player can do in game.  Essentially these become Advisors with matching Definition data, which reduces the payload we have to return in live requests and makes including Milestone information as part of a Profile or Character request more feasible.
  - We will, as we did with Advisors, tack on additional data that we can associate with the Milestone where possible and add to it over time.
  - Milestones will, like Advisors, continue to evolve over time - and, like Advisors, I will probably come to regret how I implemented our view of them.  But for now, I feel hopeful.
  - Like Advisors, there are "Public" Milestones: views of what can be done in the game right now agnostic to an individual player's state.  For this information, you will want to seek out [Destiny2.GetPublicMilestones](https://bungie-net.github.io/multi/operation_get_Destiny2-GetPublicMilestones.html#operation_get_Destiny2-GetPublicMilestones).
  
- ### The Near-Deprecation of Talent Grids

- ### The Ascent of Sockets for Item Customization

- ### (more to be filled in)

# What Endpoints are not ready?

- ### Vendors
  - We discovered breaking changes with how Vendors work under the surface pretty late in our development cycle, and were unable to recover in time to stabilize both it and our other features in time to ship.  Those of you who used Vendors in Destiny 1 remember the great Vendor meltdown of 2016.  The implementation that I built to work around said performance meltdown will no longer work in Destiny 2, which puts me back at the drawing board for finding a way to expose Vendor data in an efficient enough manner to melt neither our servers nor the game's servers.  I regret this deeply - particularly discovering it so late and thus not having time to pivot - but I am using this opportunity to pre-release the proposal for the format in which the Vendor calls will return data for review while I continue to work on making Vendors function again.  It is one of the highest priorities on my plate for post-ship.

- ### Stats (working, but may have bugs)
  - Stats are pretty much in working order, and as you will see their endpoints have not really changed at all.  These *should* be functioning as desired, but I mark them as Pending because they have not been given as thorough of a review for edge cases as the other Destiny features, because BNet itself isn't currently exposing any stats related features.  This is another high-priority task for future improvements of the site, which will also bring about improvements to the API as a result when our attention can be turned back to it as a team.

# The State of the API

The API itself is currently offline in anticipation of the release of Destiny 2.  We have this site as a special sneak preview of the data that will be provided by the API when it comes online.

# FAQ

- Certain APIs are marked as PENDING in the documentation, what does that mean?
-- This may mean that their tentative contracts are being exposed in the documentation for review, or that they're going to be live but in a "Beta" state.  Look at the description of the specific endpoint for details.


