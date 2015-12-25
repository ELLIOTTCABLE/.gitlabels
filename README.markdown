What is this? <img alt='Maintenance status' src="https://img.shields.io/badge/maintained%3F-yes!-brightgreen.svg?style=flat"><img src="http://elliottcable.s3.amazonaws.com/p/8x8.png"><a target="_blank" href="COPYING.markdown"><img alt='Open-source licensing details' src="https://img.shields.io/badge/license-CC--BY 4.0-blue.svg?style=flat"></a><img src="http://elliottcable.s3.amazonaws.com/p/8x8.png"><a target="_blank" href="http://ell.io/IRC"><img alt='Chat on Freenode' src="https://img.shields.io/badge/chat-IRC-blue.svg"></a><img src="http://elliottcable.s3.amazonaws.com/p/8x8.png"><a target="_blank" href="http://twitter.com/ELLIOTTCABLE"><img alt='Twitter followers' src="https://img.shields.io/twitter/follow/ELLIOTTCABLE.svg?style=flat&label=followers&color=blue"></a>
=============
[`.gitlabels`][gh] is a file that defines a series of “labels” that can be applied to [git][]
commits.

These labels are then used to “describe” a given commit in a human- *and* machine-readable way.
These can be used to search and filter commit lists, to bring more ‘signal’ out of the changeset
‘noise.’

  [git]: http://git-scm.com/ "Git: *Fast* version-control and source-code management system"
  [gh]: http://github.com/elliottcable/.gitlabels "The .gitlabels project"

Example applications
--------------------
- filtering out typo-fixes and other drudgery by excluding all commits with the `(-)` label
- gathering a quick summary of the changes *you* need to know about in a new release of your
  favourite library by searching for commits between the git tags ‘v1.03’ and ‘v1.04’ that include
  the label `(api)`
- following the evolution of a particular developer’s personal style by filtering on commits by that
  developer using both of the `(style refactor)` labels

Usage
-----
Using labels in your project is as simple as these two steps:

### Add a `.gitlabels` file
Your file, like this one, should exist in your project’s root directory (just like `.gitignore` and
`.gitattributes`!), and should consist of lines of the following format:

    - (a-label) A cool label that tells you some THINGS about my CODE!

The text after the parentheses is a short description of the label, suitable for displaying in
gitlabels-aware software. Descriptions must be entirely on the same line (sorry, guys, no hard-
wrapping descriptions if they get long!).

Labels can be subcategorized under other labels by indenting the `-` preceding the label by
multiples of two spaces. Filtering against a label should theoretically apply to all results that
match sublabels as well (in the example below, if a commit is labeled `bar`, it is implicitly *also*
labeled `foo`.)

    - (foo) Applies to commits that are cool!
      - (bar) Applies to foo-commits that are *totally* cool!

Multiple aliases for the semantically-same label may be included inside the parentheses; the very
first one being the canonical form (usually the shortest form) of the label, and the very last being
a ‘display form’. Any alias may be substituted for any other in a commit-message, without changing
the meaning.

    - (- m min Minor) Relatively unimportant I guess, in the grand scheme of things!!

It's possible for labels to be ‘parameterized’: that is, they can have an argument that further
qualifies something about the commit being labeled. (Although commits can have labels that are
omitted from the `.gitlabels` file, you *must* use such a declaration if you intend for the label to
be used in a parameterized form.) The data with which a label is parameterized must follow a
specific format, defined by the tooling interpreting your labels (see [below](#gitlabels-syntax));
you select *which* format a given label is to use by specifying that format in the `.gitlabels` file
as follows:

    - (fixes:<issue_id> closes) A commit that is related to a bug-tracker issue!!!!1

In this case, the `(fixes)` or `(closes)` label can be used in that project with an issue-ID as the
‘parameter’ (as in `(fixes:#1234)`.). (And as aliases are semantically identical, the argument-type
need only be declared once.)

Lines containing only whitespace or beginning with a number sign/hash (`#`) as the *first character*
will be discarded during parsing, and completely ignored. You may use these to provide commentary
and rationale on your project’s labeling choices, or for any other purpose you desire.

    # Commits that modify code, but are neither a (fix) nor a (break), are usually (re).
    - (re Refactor) Changes that don't change the semantics are v. cool tho

The parameter-type declaration (see [below](#gitlabels-syntax)) must be surrounded by angle brackets
(`<` and `>`).

### Label your commits
The fun part! Do what you like, just be *absolutely consistent* about it; if you collaborate with
other developers on the project, you should also document the labels using comments in `.gitlabels`,
your README, or some other source, and then encourage your collaborators to apply them as well.

Labels go at the very start of a commit’s commit message in parenthesis, separated by spaces or
commas. For instance:

    commit c571b86549e49bf223cf648388c46288c2241b5a
    Author: ELLIOTTCABLE <splat@ell.io>
    Date:   Sat Feb 26 23:24:29 2011 -0500

        (- doc closes:#123 see:b3b2e05) I love pretty much anything that comes from pigflesh.

This commit’s labels comprises `(doc)`, `(closes)`, `(see)`, and `(-)`; in this example, perhaps,
these could mean that this commit comprises an unimportant (in the larger scheme of things) change
to some documentation that fixes some typos or something. Those typos would have been reported in
the project’s issue tracker as issue number 1234; the other commit whose ID began with b3b2e05 was
somehow relevant as well.

Labels may include a single argument, if described by the `.gitlabels`, which documents a related
piece of data.  These arguments may only be of a limited set of data types (defined by whatever
label-parsing system you use). Arguments are never “required,” but a given label may not necessarily
make a lot of sense without its intended payload. (What does `(closes see)` mean?)

#### Additional formats
In addition to the `(gitlabel syntax)` described above to be used at the *start* of a commit-
message, there are two other locations in which one can encode labels for use on a particular
commit:

 - Pseudo-slashtags: When using longer label names, or for labels which are *relevant enough to
   include* but are of only infrequent interest to repository consumers, you can move such to the
   *very end* of a commit message, and place them on a line of their own, preceded by a `/`:

        commit da39a3ee5e6b4b0d3255bfef95601890afd80709
        Author: ELLIOTTCABLE <splat@ell.io>
        Date:   Sat Feb 26 23:24:29 2011 -0500

            (doc -) Do thing to the stuff

            Blah blah blah this is a meaningful description of the work performed in
            this commit. (You *are* including descriptions like these in your commit
            messages, aren't you? I hope so! ;)

            /closes '#123' '#456' see 'b3b2e05' signed-off-by @dan @jan @flan @stan

   The syntax for a slashtag is slightly different than the syntax for a label; of note:

    - Slashtags are parameterized with whitspace as the seperator, not a colon ...
    - ... and thus require a slightly different form of quoting for arguments. (See
      [below](#slashtag-syntax))
    - There are some valid labels that *cannot* be encoded as slashtags, due to differences in the
      parsing. These include labels whose names begin with `#`, as well as labels including a
      single-quote mark. (See [below](#slashtag-syntax))

 - A `git-notes` entry: The `notes/labels`-ref will be checked for extra labels associated with a
   particular commit, in addition to the various ways described above to include labels in the
   commit *itself*. Each label must appear on a single line; while blank lines and hash-prefixed
   lines are ignored, as described above for the `.gitlabels` file itself.

   Thus, labels can retroactively be assigned to a commit *without* changing its commit-hash as
   follows:

        git notes --ref=labels append -m 'closes:#1234' -m 'see:b3b2e05'

However, while allowed, both of these are **strongly discouraged**, for various reasons. (Amongst
others, a huge part of the point of `.gitlabels` are to move this metadata into a visually-obvious
location; hence placing them in the *summary line*, to assure that they appear above-the-fold in
various git-log presentation tools. Also worth noting is the fact that `git-notes` is very [little
used][1], [not very widely supported][2], and [more than a little broken][3].) It's better practice
to 1. use shortened label-names (such as `-` instead of `minor`, and `re` instead of `refactor`),
and 2. move longer-form metadata into either slashtags or note-*values* (see below).

   [slashtags]: <http://microsyntax.pbworks.com/w/page/20869411/Slashtags>
   [1]: <http://qr.ae/RbfIyt>
   [2]: <https://github.com/blog/707-git-notes-display>
   [3]: <http://git-scm.com/2010/08/25/notes.html>

#### Dislocated payloads
For systems that need a label to include a longer-form *payload* (i.e. payloads including newlines,
or more characters than is practical to include even in the slashtag form), it is possible to
indicate that that *payload* for a given label should be stored as a git note on the commit.
(**N.B.**: This is different from *labeling* commits using a `refs/notes/labels`-note, as described
above!)

This is triggered by declaring the argument-type in `.gitlabels` using a `~` instead of the usual
`<...>`:

    - (a-normal-label:<type>) Something something blah
    - (a-dislocated-label:~type) Blah blah blah description blah!

When so declared, the *payload* for `(a-dislocated-label)` is sourced from a `notes`-ref derived
from the label's name; for the above example, `refs/notes/a-dislocated-label`. In this case, the
payload for this label can then be included with `git-notes`:

    git commit -m '(a-dislocated-label) This is a commit!'
    git notes --ref=a-dislocated-label append -m 'this is some payload content'

(Also, be aware that it will be impossible to attach a dislocated payload to the label if the
label's name contains [characters disallowed in Git ref-names][chars], such as `\` or `:`.)

   [chars]: <https://wincent.com/wiki/Legal_Git_branch_names>

Rationale
---------
The purpose of this system is basically to encourage more *granular* committing—that is, creating
more commits for fewer changes, and ensuring each commit is more “enclosed.” I believe a project’s
git commit-log should be sufficient to describe the entire history of the project, *as it was
experienced by the developers of the project*; that is, allow any given ‘noob’ developer to browse
the commit history and learn the same things that the creators of the project learned through their
work. The mistakes they made, the improvements they invented, in a word, the *soul* of the project,
should be evidenced by the commit log. “Labels” make this easier, as it’s less intimidating to have
a commit log with thousands of commits if you know people can ignore those commits that are
irrelevant to them.

It's important to note that this system assumes labels are *intended to convey state*: That is,
they're not passive notes / associations / tags. As an example, your project's build-system might
reasonably be configured to not re-run pre-commit tests on commits labeled as `(doc)` (or any sub-
labels of `(doc)`); or your continuous-integration might be instructed to only notify team-members
about the failures of commits *not* already including the `(broken!)` label.

Syntax specifics
----------------
Someday, these need to become an actual *specification*. I'll take on that task once I see some
interest into the usage of gitlabels (so let me know if you're using this system!)

### General notes
 - Labels’ names themselves may contain any Unicode character *except*: whitespace, colons (`:`),
   commas (`,`), parenthesis (`(` or `)`), or double-quotes (`"`), as these characters are reserved
   for parsing label data.
 - Labels’ names are case-sensitive; `FOO` is not to be considered the same label as `foo`
 - Not all labels must be included in `.gitlabels`, but labels will not include descriptions, be
   related (as parents or children) to other labels, or be legally allowed any arguments, if they
   are not so declared
 - Descriptions in the `.gitlabels` file are not required, but are suggested; rationale is always a
   good thing!
 - Arguments are a parsing-error(!) for a given label unless that label is specified in
   `.gitlabels`, and it is specified to allow arguments.
 - The same label is only allowed to meaningfully appear multiple times on a single commit, *if* it
   is defined in the `.gitlabels` file to accept argumentation. In this case, it *may* appear
   multiple times with precisely the same payload. For instance, the first of the following is
   meaningless, but the second is legal:

        (closes closes)
        (closes closes:#123 closes:#456)

   (In this example, the latter is intended to encode a *set* of values, of the form
   `['', '123', '456']`.)

 - The argumentative paylod may not *contain* any double-quote characters when encoded into
   initial-labels, slashtags, or the `labels` notes-ref. Double-quotes in the payload *may*,
   however, be serialized by dislocating the payload into the label-specific notes ref.

### Initial-label syntax
 - The open-parenthesis (`(`) indicating the start of a commit’s labels must be the very first byte
   of a commit message, and there may be no close-parenthesis until the end of all labels
 - The inclusion of an open-parenthesis in the first byte immediately followed by a closing-
   parenthesis (`()`, possibly with some meaningless whitespace/commas, `( , )`), indicates *no
   labels apply*. (Yes, this means that slashtag-content at the end of the commit-message, and git
   notes, are ignored as well! This can be used to disable slashtag parsing for particular commits.)
 - An argumentative payload may be surrounded by a pair of double quotes (`"`) if it contains
   anything disallowed in a label’s name, such as whitespace (see above); it may not *contain* any
   double-quote characters when encoded into initial-labels, slashtags, or the `labels` notes-ref.
 - Any series of whitespace and/or commas may separate labels, excluding newlines (obviously.)

### Slashtag syntax
Note: The notion (and syntax) of ‘slashtags’ is defined externally to this project, and they should
be parsed according to whatever prevailing rules exist for the parsing thereof. The notes below do
not supercede general best-practice with slashtags; and should that practice change, parsers should
be improved to follow *that practice*, not what is defined here! **†**

 - The slash indicating the alternation from *data* (the commit-message) to *meta-data* (the labels)
   must be the **first byte** of the **last line** of the commit-message. The slashtag-line may,
   optionally, be terminated by one last newline (`\n`), but only if that is the very last byte in
   the file (a ‘trailing newline.’)
 - The slashtag-line may not include any newlines. (This includes inside argumentative payloads.)
 - As slashtags define `/ #foo` to be equivlent to `/foo`, for backwards-compatibility and
   equivalency with hashtags, the slashtag syntax can not be used to encode any label *names* that
   include a hash-character. (Hashes can, however, be included in the *payload*, with quotes, as per
   usual.)
 - Slashtags can encode multiple values for a partciular label *directly*, instead of by repeating
   the label: `(cc:@foo cc:@bar)` becomes `/cc @foo @bar`. The meaning remains the same.

 > †. I hope to, at some point, write a specification for the parsing and interpretation of
      slashtags. Unfortunately, due to some of the vagaries of Twitter, this is a surprisingly
      complex task. (How does one *both* allow URLs as argumentative payloads, as is already common
      practice with in-the-wild slashtags, *and* ignore Twitter-appended picture-URLs and similar
      end-of-body content?) Until then, gitlabels' use of slashtags is intentionally vague. I don't
      consider this a problem, because I *also* consider slashtag use to be an edge-case and a
      somewhat-bad practice.

### `git-notes` syntax
 - Each line of the content stored in the `git-notes` under the `labels` ref/namespace is treated as
   a single label applied to the commit the note belongs to, disregarding blank lines and lines
   beginning with `#`.
 - Each line is parsed the same way as a single ‘word’ of the ‘initial’ syntax: a colon separates
   label and payload; label names are restricted in precisely the same way. The only differences
   are:
 - Quotes are not necessary (although they *are* allowed, parsed, and ignored) to wrap payloads that
   have spaces or other special characters, as only one label (and thus only one argument) are
   allowed per line. Quotes *are* still needed to encode payloads that have leading or trailing
   whitespace, because:
 - The line is whitespace-trimmed; that is, the line can (meaninglessly) begin with whitespace,
   whitespace at the end is trimmed(!), and whitespace before/after the colon (!)is trimmed. For
   instance, following two lines are equiavlent:

        sign:"ELLIOTTCABLE <splat@ell.io>"
        sign : ELLIOTTCABLE <splat@ell.io>

 - As with all of the formats *except* when using a dislocated payload, newlines in paylodas are
   unserializable into the `labels` ref.

This format is designed to be used with the [`--strategy=cat_sort_uniq` form][strat], which can
resolve merge conflicts in systematic notes like this without the usual trouble. It is *also*
compatible with all the commonly-used extant git-notes, such as the example from the `git-notes`
manpage, `Tested-by: Johannes Sixt <j6t@kdbg.org>`. These existing notes are, however, stored on the
`refs/notes/commits` ref by default; you will have to move them to the `refs/notes/labels` ref if
migrating a project with existing notes. You may want to define aliases for some of the common Git
usages such as `git commit --signoff`:

    - (sign:<person> Signed-off-by) Looks-good-to-me'd by a particular contributor

   [strat]: <https://www.kernel.org/pub/software/scm/git/docs/git-notes.html#_notes_merge_strategies>

### `.gitlabels` syntax
 - Any line with content other than whitespace, a `-`, or a `#` at the start of the line is
   erraneous, and should result treated as a parsing error.
 - The same is true of an *odd* number of whitespace characters, as sub-labels may only be indented
   by multiples of two-spaces (sorry, I'd rather keep a very simple-to-parse syntax, than support
   any rando's preferred indentation flavour. ;)
 - Any content other than (whitespace followed by) an opening-paren, valid label-names, and a
   closing paren, immediately after the opening `-`, is illegal as well. (Basically, each line must
   follow the `- (n na name)` format.)
 - There is no way to encode newlines into a label's ‘description.’ This is intended to be kept
   short and sweet, i.e. as the alt-text for the label on a web interface, or as a note presented
   aside the label when possible labels are presented in a list. In-depth discussion or rationale
   should be relegated to *comments* in the `.gitlabels` file.
 - Payload ‘types’ are completely implementation-specific, but their names should be kept simple,
   including only alphanumeric characters and underscores.
 - Although the payload's content may fail to meet limitations specified by an implementation (for
   instance, a payload to a label specified to take an `issue_id` not beginning with a `#`), that is
   *not* to be interpreted as a parsing error; the payload should instead be ignored, and the commit
   treated as labeled with a payload-less instance of that label.

Of note, although what sorts of *payloads* are useful depends on your specific project and tooling,
here are some examples of ones that might be useful:

 - `<rev_hash>`: `"da39a3e"` A git-object SHA-1, usually abbreviated.
 - `<issue_id>`: `"#1337"` An identifier for a particular bug-tracker entry or pull-request
 - `<person>`: `"Elliott Cable <splat@ell.io>"` A commiter-identity, in the usual Git form
 - `<boolean>`: `true`, `yes`, `false`, `on`, `off`, and so on
 - `<text>`: `"Hello, world"`, free-form textual content
 - `<base64>`: `"aGVsbG8="` An textual encoding of arbitrary binary data

The restrictions on the formatting of the payload-content are thus also implementation-specified.

`git-notes` caveats
-------------------
Additional caveats, if you choose to use `git-notes` *either* for retroactively labelling commits,
or for dislocated metadata:

 - Notes are *not* pushed to servers, or retreived from them, by default! If you use this
   functionality in your gitlabel-workflow, you'll need to ensure that:

   1. You instruct your contributors (or add tooling/hooks) to configure their *local repository* to
      receive notes-refs from the server:

          git config remote.origin.fetch --add refs/notes/labels:refs/notes/labels
          git config notes.labels.mergeStrategy cat_sort_uniq

   2. Similarly, you'll have to instruct them to manually push the `notes` refs whenever they add or
      change labels:

          git push origin refs/notes/labels # or refs/notes/*

   3. If they want to *see* `git-notes`-encoded labels without intervention of gitlabels-aware
      software, they will need to also configure that:

          git config notes.displayRef refs/notes/labels # or refs/notes/*

 - Because of the unreliability and opt-in nature of `git-notes`, it's important to *only* use this
   functionality for non-critical gitlabels; that is, ones which won't be missed by repository
   consumers if they are omitted.
 - Also because they are inevitably ‘omittable’ (due to `notes` refs not being cloned by default),
   the *parsing* of `notes` refs is entirely optional: an implementation supporting the rest of the
   gitlabels syntax may opt to completely ignore the `refs/notes/labels`-ref, as well as payloads
   defined with the `~dislocated` form. (This also means it is possible to create an implementation
   that needs *only* a stream of commit-messages and the `.gitlabels` file, instead of access to a
   full Git repository, or the `git` binary.)

Licensing
---------
The above text is released for public usage under the terms of the Creative Commons [CC-BY 4.0][]
license; more information is available in [OPYING][].

I intend for this effort to be as widely useful as is possible; if that license is in any way too
restrictive for your purposes, please let me know. There's a good chance I'll be willing to provide
you a special dispensation for your particular usage. (=

   [CC-BY 4.0]: <http://creativecommons.org/licenses/by/4.0>
   [COPYING]: <./COPYING.markdown>
