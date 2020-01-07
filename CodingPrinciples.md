# Coding Principles

This file contains a set of coding/development principles I try and stick to whilst developing.  I started work on this list shortly after reading Ray Dalio's 'Principles', and amid discussions within my (then new) team around how we want want to work.  It has so far been very useful to me personally, and made for some very interesting talking points within the team.  I very much hope to keep maintaining this file, adjusting it as I develop as a developer.

Please note that whilst the items listed below should be applicable to pretty much any language/etc they have largely authored with C# in mind, and will make occasional references to C# types/constructs.

### The principles:

- Never use anything for 2 different things
    - This includes using something to both hold data, and to do things with it; an object may wrap a data structure (such as time series), but a data structure (data model) should never do things itself (simple read-only props are permitted)
    - Constructors on data structures should be removed; firstly they are methods, and methods don't belong on data structures, secondly they obsfucate the code and hide what is being assigned to what, and they do not scale as the data structure grows (3 params today, 15 tomorrow)
    - Includes local variables, never mutate a local to allow it to be used for multiple things
    - Null objects never belong in a collection unless it is an array accessed via co-ordinates, null represents the absence of information and adding it to a List is wasteful, confusing and error prone

- Organise local variables and limit their availabilty
    - Group variable declarations within a scope with other similar variables; for example do all data retrieval at the top, and all modifications at the bottom.  This should almost always be prioritized ahead of the need to limit the lifetime of the objects
    - Seek to limit the lifetime/scope of an object; people reading the code wont need to worry about it for as long and it makes it harder to introduce mutations. This is secondary to the need to group similar variables
    - Always dispose any owned objects that are disposable as soon as possible
    - Never dispose objects in a scope that doesn't own them
    - Prefer multiple loops to one if performance is not key and the loop needs to do multiple things, this keeps things seperate and easier to read/change/delete, and also helps prevent variables from being used for different purposes, and keep commits clean and independant
    - If an alias is only used once, then it shouldn't be used at all (unless part of an object deconstruction, where sibling aliases are also referenced), if it is used more than once still heavily consider removing it in order to reduce missdirection

- Limit the number of potential code paths
    - If a method parameter can be null, then it should be optional; if it is optional it should never suprise anyone when it is null
    - Delete any code that isnt used
        - Includes overly defensive code that is never used (e.g. null checks where it is never null)
        - Includes the throwing of explicit exceptions that add very little/no value, where an exception would be thrown anyway
    - Use types to handle type specific code paths, do not code for concrete types within the code consuming them; allows the compiler to do a lot of heavy lifting and helps keep the codebase cohesive, keeping type specific code near or on the type they pertain to, also saves anyone investigating type A from having to read the code for type B when investigating a problem 
    - Only use 'new' member declarations in interfaces/abstract members, can be really confusing on concrete implementations
    - Always use the same default params in the implementation of a function as declared on the interface, can be really confusing otherwise
    - Limit the number of public members available
        - If a public method is only used once, it should probably just be done inline or made private
        - If a public member is only accessed from it's host it should be private/protected
        - If a public member is only accessed from it's project it should be internal
        - If not ever consumer of an interface uses all of its members then the interface should be broken up (can still have layers of implementing interfaces so that IC implements IA and IB)
        - Never hold non-type dependant members on the concrete type; if it only uses public members then it can live elsewhere, probably as an extension - this makes it far easier to maintain a cohesive codebase and segragate public members using interfaces.  Also minimizes the amount of code required to read if working in a class.
        - If a property is optional and it is known at compile-time whether it has a value or not, it should not be available on the object (hiding via an interface is permitted if otherwise impractical)
        - Always seek to minimize the exposure of generics, if an object has members not dependent on object level generics they should be exposed via a generic-free interface extended by an interface containing the generic dependent members

- Continuously document your code
    - Comments should be added where you are forced to do something counter-intuitive
    - Comments should be removed if they document the obvious
    - Why you have made a given change should be handled in the commit message, not the code
    - Commits should do one thing
        - Always seperate refactoring commits from behavoural changes (including bug fixes)
        - Always seperate renamings from other code changes
        - Always seperate indentation changes from other changes
        - Always seperate non-local deletions from other changes; helps an awful lot if/when you need to revert/cherry-pick/move commits around
        - If changing commented code update the comments in the same commit
        - If changing comments but not the corresponding code, do it in a seperate commit to any code changes
    - If something is deliberately and temporarily left undone, immediately add a comment and task to the PR
    - Push continuously; documentation is useless if no one can read it, also backups up your work
    - Never throw the base Exception class, there is always a more specific type you could be using
    - Exceptions should be reserved for coding errors, and not for controlling flow; there is almost never a reason to deliberately throw them
    - Magic numbers should never exist

- Limit the scope of your changes
    - If something desirable is enabled by changes made in a branch/PR do not do them in the branch, add a ticket and then add a comment with a link to the ticket created; this helps guard against scope-creep and limits the risk to the original PR
    - If a refactoring would probably make it easier to do your current task, open a new ticket and a new branch to do it in
    - If a refactoring oportunity is spotted looking at a task and it is not relevant to the task at hand, open a new ticket and do it seperately
    - If you know something works and you want unit tests to guard against future changes, write the unit tests in a new branch with a new ticket

- Know what is important
    - Dont argue to hard over anything that doesnt matter much, if the topic is covered in here argue around the principle and try and refine it further.  If it is not in here and it does matter, add it to this document and refine it.
    - If something cant handle the core changes made and it isnt used by the customer consider deleting it with minimal investigation; for example unit tests that test very little but mock a lot, internal apps which may or may not still be used
    - Most non-I/O code doesn't care about performance, so dont worry about it
    - If non-I/O code cares about performance, benchmark it
    - If working on a bug, try and find out why it happened and then try to make sure it cant happen again (possibly in a new ticket/branch)
    - Recognise when an arguement cannot be resolved by agreement, if it is not important, fold (possibly doing changes in another ticket/branch) and document disagreement, if it is important take it up with someone else and if no agreement reached then, merge anyway.
    - Dont waste time waiting on a PR, if people are being unreasonably slow and costing time and risking merge bugs, merge without approval
    - If you know something works and will not be changed frequently, dont delay delivery for unit tests; the users dont care 
