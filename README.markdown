What is this?
=============
`.gitlabels` is a file that defines a series of “labels” that can be applied to [git][] commits.

Each label is intended to “describe” a given commit in a human- and machine-readable way. These can then be used
to search and filter commit lists, to bring more ‘signal’ out of the changeset ‘noise.’

  [git]: <http://git-scm.com/> "Git: *Fast* version-control and source-code management system"

Examples
--------
- filtering out typo-fixes and other drudgery by excluding all commits with the `(-)` label
- gathering a quick summary of the changes *you* need to know about in a new release of your favourite library by
  searching for commits between the git tags ‘v1.03’ and ‘v1.04’ that include the label `(api)`
- following the evolution of a particular developer’s personal style by filtering on commits by that developer
  using both of the `(style refactor)` labels

Usage
-----
Applying labels to your project is as simple as these two steps:

### Add a `.gitlabels` file
Your file, like this one, should exist in your project’s root directory (just like `.gitignore` and
`.gitattributes`!), and should consist of lines of the following format:
    
    - (an-awesome-label:<argument_type>) some description of the label over here, guys!
    
Labels can be subcategorized under other labels by indenting the `-` preceding the label by two spaces. Filtering
against a label should theoretically apply to all results that match sublabels as well. Descriptions must be
entirely on the same line (sorry, guys, no hard-wrapping descriptions if they get long!).

Lines containing only whitespace or beginning with a number sign/hash (`#`) in the *first column* will be
discarded during parsing, and completely ignored. You may use these to provide commentary and rationale on your
project’s labeling system, or for any other purpose you desire.

The parenthesis may only contain a single label, that is, they may not contain any of the characters that are not
legal in label names, other than the colon (`:`) necessary to declare an optional argument and its type. The
argument declaration (see below) must be surrounded by angle brackets (`<` and `>`).

### Label your commits
The fun part! Do what you like, just be *absolutely consistent* about it; if you collaborate with other
developers on the project, you should also document the labels using comments in `.gitlabels`, your README, or
some other source, and then encourage your collaborators to apply them as well.

Labels go at the very start of a commit’s commit message in parenthesis, separated by spaces or commas. For
instance:
    
    commit e17f638848fb05a43b2cb431fabdbebc84ac091a
    Author: elliottcable <splat@ell.io>
    Date:   Sat Feb 26 23:24:29 2011 -0500
    
    	(doc closes:1234 see:b3b2e05 -) I love pretty much anything that comes from pigflesh.
    
This commit’s labels comprises `(doc)`, `(fix)`, and `(-)`; in this example, perhaps, these could mean that this
commit comprises an unimportant (in the larger scheme of things) change to some documentation that fixes some
typos or something.

Labels may include a single argument, if described by the `.gitlabels`, which documents a related piece of data.
These arguments may only be of a limited set of data types (defined by whatever label-parsing system you use, but
I suggest at least `<commit>` and `<issue>` types of data). These might include a `(closes:<issue>)` or
`(related:<commit>)` label, for example. Arguments are never “required,” but a given label may not necessarily
make a lot of sense without its intended payload.

Rationale
---------
The purpose of this system is basically to encourage more *granular* committing—that is, creating more commits
for fewer changes, and ensuring each commit is more “enclosed.” I believe a project’s git commit-log should be
sufficient to describe the entire history of the project, *as it was experienced by the developers of the
project*; that is, allow any given ‘noob’ developer to browse the commit history and learn the same things that
the creators of the project learned through their work. The mistakes they made, the improvements they invented,
in a word, the *soul* of the project, should be evidenced by the commit log. “Labels” make this easier, as it’s
less intimidating to have a commit log with thousands of commits if you know people can ignore those commits that
are irrelevant to them.

Notes
-----
- The open-parenthesis (`(`) indicating the start of a commit’s labels must be the very first byte of a commit
  message, and there may be no close-parenthesis until the end of all labels
- The omission of an open-parenthesis (`(`), or the inclusion of an open-parenthesis in the first byte
  immediately followed by a closing-parenthesis (`()`, possibly with some meaningless whitespace/commas),
  indicates no labels apply
- Labels’ names themselves may contain any Unicode character *except*: whitespace, colons (`:`), commas (`,`), or
  parenthesis (`(` or `)`), as these characters are reserved for parsing label data
- Labels’ names are case-sensitive; `FOO` is not to be considered the same label as `foo`
- Descriptions in the `.gitlabels` file are not required, but are suggested; rationale is always a good thing!
- Not all labels must be included in `.gitlabels`, but labels will not include descriptions, be related (as
  parents or children) to other labels, or be legally allowed any arguments, if they are not so included
- Arguments are illegal for a given label unless they are included in `.gitlabels`
- An argumentative payload may be surrounded by a pair of double quotes (`"`) if it contains anything
  disallowed in a label’s name, such as whitespace (see above); it may not, under any circumstances, *contain*
  any double-quote characters
- The same label may appear multiple times on a single commit, and *may* include different arguments each time.
- *Any* series of whitespace and/or commas may separate labels (though newlines are inadvisable)

(I, elliottcable, hereby release everything below the line containing “What is this?” into the public domain.
 Use, distribute, and re-use as you please. Hell, I’d *love* it if you’d spread it around; maybe then, @GitHub
 would be forced to support label filtering on `/compare/` pages! :D
 
 I do, however, request that you either retain this explanatory document below the content of your `.gitlabels`
 file as I do, or include a link to http://github.com/elliottcable/.gitlabels instead. Help spread the word!)
