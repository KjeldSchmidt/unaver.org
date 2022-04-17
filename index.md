Unary Versioning 11..
==============================

Summary
-------

There are two simple rules to Unary Versioning.

First, all numbers are given in unary, also called Base-1. 

Second, given a version number MAJOR.MINOR.PATCH, increment the:

1. MAJOR version when you make incompatible API changes,
1. MINOR version when you add functionality in a backwards compatible
   manner, and
1. PATCH version when you make backwards compatible bug fixes.

Additional labels for pre-release and build metadata are available as extensions
to the MAJOR.MINOR.PATCH format.

Introduction
------------

In the world of software management there exists a dreaded place called
"number parsing hell." The bigger your system grows and the more packages you
integrate into your software, the more likely you are to find yourself, one
day, in this pit of despair.

In large distributed systems with heterogeneous technologies, there is a large
variety of tools and language capabilities commonly used. Managing package and
software release versions took a large step forwards with the introduction of
Semantic Versioning. However, the Semantic Versioning approach has a fatal 
flaw: the representation of numeric values as human-readable strings.
A human-readable number must be converted into a machine-readable number
before a dependency management can hope to correctly resolve the dependencies
for a project. Many mainstream development languages have number-parsing 
capabilities, but some do not. Even those that do will frequently require 
complicated manual setup and preparation.

As a solution to this problem, we propose a simple extension of the rules of
semantic versioning, which minimizes the required capabilities of any software
environment that needs to handle version numbers intelligently and with minimal
effort. As a first step, follow the versioning recommendations set out by 
Semantic Versioning. However, when encoding version numbers, there is one extra
rule to follow:

When writing down your version numbers, always use Base-1 instead of Base-10.
For example, if you conceive of a version as being 1.2.3, write it down as
1.11.111. This has two benefits:

1. Ease of parsing. Only a `split` and a `len` function are required to assess
the version number. No complicated `parseInt` is needed.
1. This scheme is strictly semver-compliant. Systems that expect numbers in
Base-10 will still correctly identify the semantic meaning of version upgrades
which are given in Base-1.

We call this system "Unary Versioning." As outlined above, this scheme is 
strictly easier and equally as expressive as semantic versioning. Due to the
widespread adoption of Semantic Versioning and the undeniable, objective
improvement that Unary Versioning brings, we expect this scheme to become the
dominant software versioning scheme in the Open Source community quickly.

Migration from SemVer to UnaVer
------------------------------------------
At this point, you are obviously convinced of the benefits of Unary Versioning,
and find yourself saddened by the fact that your projects are already using
Semantic Versioning. 

Fear not, for migration is very easy. Quite simply, the next time you increment
your software version, simply apply the Unary Version strategy to all numbers
which have changed, making sure to choose your new version number such that the
Base-10 reading of your new unary version number exceeds the previous version
number. For example, when updating the minor version from 1.15.3, declare the
next version as 1.111.. Note the empty string for the patch number, which is
consistent with standard Base-1 notation.


Unary Versioning Specification (UnaVer)
---------------------------------------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

1. Software using Semantic Versioning MUST declare a public API. This API
could be declared in the code itself or exist strictly in documentation.
However it is done, it SHOULD be precise and comprehensive.

{:start="11"}
1. A normal version number MUST take the form X.Y.Z where X, Y, and Z are
non-negative integers *written in Base-1*, and MUST NOT contain any digits 
other than 1. X is the major version, Y is the minor version, and Z is the
patch version. Each element MUST increase numerically. For instance:
1.111111111. -> 1.1111111111. -> 1.11111111111..

{:start="111"}
1. Once a versioned package has been released, the contents of that version
MUST NOT be modified. Any modifications MUST be released as a new version.

{:start="1111"}
1. Major version zero (.y.z) is for initial development. Anything MAY change
at any time. The public API SHOULD NOT be considered stable.

{:start="11111"}
1. Version 1.. defines the public API. The way in which the version number
is incremented after this release is dependent on this public API and how it
changes.

{:start="111111"}
1. Patch version Z (x.y.Z | x >= 1) MUST be incremented if only backwards
compatible bug fixes are introduced. A bug fix is defined as an internal
change that fixes incorrect behavior.

{:start="1111111"}
1. Minor version Y (x.Y.z | x >= 1) MUST be incremented if new, backwards
compatible functionality is introduced to the public API. It MUST be
incremented if any public API functionality is marked as deprecated. It MAY be
incremented if substantial new functionality or improvements are introduced
within the private code. It MAY include patch level changes. Patch version
MUST be reset to 0 when minor version is incremented.

{:start="11111111"}
1. Major version X (X.y.z | X >= 1) MUST be incremented if any backwards
incompatible changes are introduced to the public API. It MAY also include minor
and patch level changes. Patch and minor versions MUST be reset to 0 when major
version is incremented.

{:start="111111111"}
1. A pre-release version MAY be denoted by appending a hyphen and a
series of dot separated identifiers immediately following the patch
version. Identifiers MUST comprise only the character 1 and hyphens [1-].
Identifiers MUST NOT be empty. Numeric identifiers MUST NOT include leading 
zeroes. Pre-release versions have a lower precedence than the associated normal
version. A pre-release version indicates that the version is unstable and might
not satisfy the intended compatibility requirements as denoted by its
associated normal version. 
Examples: 1..-1, 1..-1.1, 1..-.111.1111111,
1..-.1111111..1111111111, 1..-..111..

{:start="1111111111"}
1. Build metadata MAY be denoted by appending a plus sign and a series of dot
separated identifiers immediately following the patch or pre-release version.
Identifiers MUST comprise only the character 1 and hyphens [1-].
Identifiers MUST NOT be empty. Build metadata MUST be ignored when determining
version precedence. Thus two versions that differ only in the build metadata,
have the same precedence. Examples: 1..-11111+1, 1..+1111111,
1..-1111+111.111.1111111111, 1..+1111111----111111111111.

{:start="11111111111"}
1. Precedence refers to how versions are compared to each other when ordered.

    1. Precedence MUST be calculated by separating the version into major,
       minor, patch and pre-release identifiers in that order (Build metadata
       does not figure into precedence).

    {:start="11"}
    1. Precedence is determined by the first difference when comparing each of
       these identifiers from left to right as follows: Major, minor, and patch
       versions are always compared numerically, or, equivalently, by length.

       Example: 1.. < 11.. < 11.1. < 11.1.1.

    {:start="111"}
    1. When major, minor, and patch are equal, a pre-release version has lower
       precedence than a normal version:

       Example: 1..-11111 < 1...

    {:start="1111"}
    1. Precedence for two pre-release versions with the same major, minor, and
       patch version MUST be determined by comparing each dot separated identifier
       from left to right until a difference is found as follows:

       {:start="1"}
       1. Identifiers consisting of only the digit 1 are compared numerically.

       {:start="11"}
       1. Identifiers with hyphens are compared lexically in ASCII
          sort order.

       {:start="111"}
       1. Numeric identifiers always have lower precedence than non-numeric
          identifiers.

       {:start="1111"}
       1. A larger set of pre-release fields has a higher precedence than a
          smaller set, if all of the preceding identifiers are equal.

       Example: 1..-11111 < 1..-11111.1 < 1..-11111.1111 < 1..-1111 < 
       1..-1111.1 < 1..-11.11 < 1...

Backus–Naur Form Grammar for Valid SemVer Versions
--------------------------------------------------
```
<valid semver> ::= <version core>
                 | <version core> "-" <pre-release>
                 | <version core> "+" <build>
                 | <version core> "-" <pre-release> "+" <build>

<version core> ::= <major> "." <minor> "." <patch>

<major> ::= <numeric identifier>

<minor> ::= <numeric identifier>

<patch> ::= <numeric identifier>

<pre-release> ::= <dot-separated pre-release identifiers>

<dot-separated pre-release identifiers> ::= <pre-release identifier>
                                          | <pre-release identifier> "." <dot-separated pre-release identifiers>

<build> ::= <dot-separated build identifiers>

<dot-separated build identifiers> ::= <build identifier>
                                    | <build identifier> "." <dot-separated build identifiers>

<pre-release identifier> ::= <nonnumeric identifier>
                           | <numeric identifier>

<build identifier> ::= <nonnumeric identifier>
                     | <digits>

<nonnumeric identifier> ::= <non-digit>
                            | <non-digit> <identifier characters>
                            | <identifier characters> <non-digit>
                            | <identifier characters> <non-digit> <identifier characters>

<numeric identifier> ::= ""
                       | <digits>

<identifier characters> ::= <identifier character>
                          | <identifier character> <identifier characters>

<identifier character> ::= <digit>
                         | <non-digit>

<non-digit> ::= "-"

<digits> ::= <digit>
           | <digit> <digits>

<digit> ::= "1"
```

Why Use Unary Versioning?
----------------------------

As specified above, Unary Versioning encompasses all the benefits of Semantic
Versioning while requiring minimal computational capability to use these 
benefits from any software system that would like to do so.

There are literally no drawbacks of any kind in using unary versioning over 
standard Semantic Versioning.

FAQ
---

### How should I deal with revisions in the .y.z initial development phase?

The simplest thing to do is start your initial development release at .1.
and then increment the minor version for each subsequent release.

### How do I know when to release 1..?

If your software is being used in production, it should probably already be
1... If you have a stable API on which users have come to depend, you should
be 1... If you're worrying a lot about backwards compatibility, you should
probably already be 1...

### Doesn't this discourage rapid development and fast iteration?

Major version zero is all about rapid development. If you're changing the API
every day you should either still be in version .y.z or on a separate
development branch working on the next major version.

### If even the tiniest backwards incompatible changes to the public API require a major version bump, won't I end up at version 1111111111111111111111111111111111111.. very rapidly?

This is a question of responsible development and foresight. Incompatible
changes should not be introduced lightly to software that has a lot of
dependent code. The cost that must be incurred to upgrade can be significant.
Having to bump major versions to release incompatible changes means you'll
think through the impact of your changes, and evaluate the cost/benefit ratio
involved.

### Documenting the entire public API is too much work!

It is your responsibility as a professional developer to properly document
software that is intended for use by others. Managing software complexity is a
hugely important part of keeping a project efficient, and that's hard to do if
nobody knows how to use your software, or what methods are safe to call. In
the long run, Semantic Versioning, and the insistence on a well defined public
API can keep everyone and everything running smoothly.

### What do I do if I accidentally release a backwards incompatible change as a minor version?

As soon as you realize that you've broken the Semantic Versioning spec, fix
the problem and release a new minor version that corrects the problem and
restores backwards compatibility. Even under this circumstance, it is
unacceptable to modify versioned releases. If it's appropriate,
document the offending version and inform your users of the problem so that
they are aware of the offending version.

### What should I do if I update my own dependencies without changing the public API?

That would be considered compatible since it does not affect the public API.
Software that explicitly depends on the same dependencies as your package
should have their own dependency specifications and the author will notice any
conflicts. Determining whether the change is a patch level or minor level
modification depends on whether you updated your dependencies in order to fix
a bug or introduce new functionality. We would usually expect additional code
for the latter instance, in which case it's obviously a minor level increment.

### What if I inadvertently alter the public API in a way that is not compliant with the version number change (i.e. the code incorrectly introduces a major breaking change in a patch release)?

Use your best judgment. If you have a huge audience that will be drastically
impacted by changing the behavior back to what the public API intended, then
it may be best to perform a major version release, even though the fix could
strictly be considered a patch release. Remember, Semantic Versioning is all
about conveying meaning by how the version number changes. If these changes
are important to your users, use the version number to inform them.

### How should I handle deprecating functionality?

Deprecating existing functionality is a normal part of software development and
is often required to make forward progress. When you deprecate part of your
public API, you should do two things: (1) update your documentation to let
users know about the change, (11) issue a new minor release with the deprecation
in place. Before you completely remove the functionality in a new major release
there should be at least one minor release that contains the deprecation so
that users can smoothly transition to the new API.

### Does UnaVer have a size limit on the version string?

No, but use good judgment. A 4095 character version string is probably overkill,
for example. Also, specific systems may impose their own limits on the size of
the string.

### Is "v1.11.111" a unary version?

No, "v1.11.111" is not a semantic version. However, prefixing a semantic version
with a "v" is a common way (in English) to indicate it is a version number.
Abbreviating "version" as "v" is often seen with version control. Example:
`git tag v1.11.111 -m "Release version 1.11.111"`, in which case "v1.11.111" is a tag
name and the semantic version is "1.11.111".

### Is this a joke?

Yes. It was created by [Kjeld Schmidt](https://kjeld-schmidt.com) by forking
the [SemVer website](https://semver.org) and changing some text.

About
-----

The Semantic Versioning specification was originally authored by [Tom
Preston-Werner](https://tom.preston-werner.com), inventor of Gravatar and
cofounder of GitHub.

The Unary Versioning specification was created as a spoof and is largely a 
direct copy, largely devoid of meaningful change, other than the repetition of
a simple joke significantly beyond the point of it actually being funny.

License
-------

[Creative Commons ― CC BY 3.0](https://creativecommons.org/licenses/by/3.0/)
