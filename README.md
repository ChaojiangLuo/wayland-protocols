# Wayland protocols

wayland-protocols contains Wayland protocols that add functionality not
available in the Wayland core protocol. Such protocols either add
completely new functionality, or extend the functionality of some other
protocol either in Wayland core, or some other protocol in
wayland-protocols.

A protocol in wayland-protocols consists of a directory containing a set
of XML files containing the protocol specification, and a README file
containing detailed state and a list of maintainers.

## Protocol directory tree structure

Protocols may be "stable", "unstable" or "deprecated", and the interface
and protocol names as well as place in the directory tree will reflect
this.

A stable protocol is a protocol which has been declared stable by
the maintainers. Changes to such protocols will always be backward
compatible.

An unstable protocol is a protocol currently under development and this
will be reflected in the protocol and interface names. See [Unstable
naming convention](#unstable-naming-convention).

A deprecated protocol is a protocol that has either been replaced by some
other protocol, or declared undesirable for some other reason. No more
changes will be made to a deprecated protocol.

Depending on which of the above states the protocol is in, the protocol
is placed within the toplevel directory containing the protocols with the
same state. Stable protocols are placed in the `stable/` directory,
unstable protocols are placed in the `unstable/` directory, and
deprecated protocols are placed in the `deprecated/` directory.

## Protocol development procedure

To propose a new protocol, create a GitLab merge request adding the
relevant files and Makefile.am entry to the repository with the
explanation and motivation in the commit message. Protocols are
organized in namespaces describing their scope ("wp", "xdg" and "ext").
There are different requirements for each namespace, see [GOVERNANCE
section 2](GOVERNANCE.md#2-protocols) for more information.

If the new protocol is just an idea, open an issue on the GitLab issue
tracker. If the protocol isn't ready for complete review yet and is an
RFC, create a merge request and add the "WIP:" prefix in the title.

To propose changes to existing protocols, create a GitLab merge request.

If the changes are backward incompatible changes to an unstable protocol,
see [Unstable protocol changes](#unstable-protocol-changes).

## Interface naming convention

All protocols should avoid using generic namespaces or no namespaces in
the protocol interface names in order to minimize risk that the generated
C API collides with other C API. Interface names that may collide with
interface names from other protocols should also be avoided.

For generic protocols not limited to certain configurations (such as
specific desktop environment or operating system) the `wp_` prefix
should be used on all interfaces in the protocol.

For protocols allowing clients to configure how their windows are
managed, the `xdg_` prefix should be used.

For operating system specific protocols, the interfaces should be
prefixed with both `wp_` and the operating system, for example
`wp_linux_`, or `wp_freebsd_`, etc.

For more information about namespaces, see [GOVERNANCE section 2.1
](GOVERNANCE.md#21-protocol-namespaces).

## Unstable naming convention

Unstable protocols have a special naming convention in order to make it
possible to make discoverable backward incompatible changes.

An unstable protocol has at least two versions: the major version, which
represents backward incompatible changes, and the minor version, which
represents backward compatible changes to the interfaces in the protocol.

The major version is part of the XML file name, the protocol name in the
XML, and interface names in the protocol.

Minor versions are the version attributes of the interfaces in the XML.
There may be more than one minor version per protocol, if there are more
than one global.

The XML file and protocol name also has the word 'unstable' in them, and
all of the interfaces in the protocol are prefixed with `z` and
suffixed with the major version number.

For example, an unstable protocol called `foo-bar` with major version 2
containing the two interfaces `wp_foo` and `wp_bar` both minor version 1
will be placed in the directory `unstable/foo-bar/` consisting of one file
called `README` and one called `foo-bar-unstable-v2.xml`. The XML file
will consist of two interfaces called `zwp_foo_v2` and `zwp_bar_v2` with
the `version` attribute set to 1.

## Unstable protocol changes

During the development of a new protocol it is possible that backward
incompatible changes are needed. Such a change needs to be represented
in the major and minor versions of the protocol.

Assuming a backward incompatible change is needed, the procedure for how to
do so is the following:

- Make a copy of the XML file with the major version increased by 1.
- Increase the major version number in the protocol XML by 1.
- Increase the major version number in all of the interfaces in the
  XML by 1.
- Reset the minor version number (interface version attribute) of all
  the interfaces to 1.

Backward compatible changes within a major unstable version can be done
in the regular way as done in core Wayland or in stable protocols.

## Declaring a protocol stable

Once it is decided that a protocol should be declared stable, meaning no
more backward incompatible changes will ever be allowed, one last
breakage is needed.

The procedure of doing this is the following:

- Create a new directory in the `stable/` toplevel directory with the
  same name as the protocol directory in the `unstable/` directory.
- Copy the final version of the XML that is the version that was
  decided to be declared stable into the new directory. The target name
  should be the same name as the protocol directory but with the `.xml`
  suffix.
- Rename the name of the protocol in the XML by removing the
  `unstable` part and the major version number.
- Remove the `z` prefix and the major version number suffix from all
  of the interfaces in the protocol.
- Reset all of the interface version attributes to 1.
- Update the `README` file in the unstable directory and create a new
  `README` file in the new directory.

There are other requirements for declaring a protocol stable, see
[GOVERNANCE section 2.3](GOVERNANCE.md#23-introducing-new-protocols).

## Releases

Each release of wayland-protocols finalizes the version of the protocols
to their state they had at that time.

## Gitlab conventions

### Triaging merge requests

New merge requests should be triaged. Doing so requires the one doing the
triage to add a set of initial labels:

~"New Protocol" - For a new protocol being added. If it's an amendment to
an existing protocol, apply the label of the corresponding protocol
instead. If none exist, create it.

~"Needs acks" - If the protocol needs one or more acknowledgements.

~"Needs implementations" - If there are not enough implementations of the
protocol.

~"Needs review" - If the protocol is in need of review.

~"In 30 day discussion period" - If the protocol needs a 30 day discussion
period.

For the meaning and requirement of acknowledgments and available
implementations, see the GOVERNANCE.md document.

### Managing merge requests

When merge requests get their needed feedback and items, remove the
corresponding label that marks it as needing something. For example, if a
merge request receives all the required acknowledgments, remove the ~"Needs
acks" label, or if 30 days passed since opening, remove any ~"In 30 days
discussion period" label.

### Nacking a merge request

If the inclusion of a merge request is denied due to one or more Nacks, add
the ~Nacked label.
