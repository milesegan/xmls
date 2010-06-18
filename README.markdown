Summary
================================================================================
Xmls is a small, simple, non-validating xml parser for Common Lisp.  It's
designed to be a self-contained, easily embedded parser that recognizes a useful
subset of the XML spec.  It provides a simple mapping from xml to lisp 
s-expressions and back.

# Features
* Free (BSD license).
* Understands enough of the xml spec to parse many common documents, including
  those occurring in common internet protocols like xml-rpc, webdav, and BEEP.
  Parses 85 out of the 98 valid documents in the oasis parser compliance suite.

* Small and easily embedded.  The entire parser is contained in one
  file and it's currently less than 600 lines of code.  Xmls is written in
  pure lisp and requires no external parsing tools or foreign libraries.

* Supports xml namespaces.

* Threadsafe.

* Serializes s-expr list structures back to xml as well as parsing xml.

Limitations
================================================================================
* Parses entire document into memory and consequently can't handle large
  documents.
* No detailed error reporting.

Xml Representation
================================================================================
Parsed xml is represented as a lisp list.  A node is represented as follows:

    (name (attributes) children*)

A name is either a simple string, if the element does not belong to a namespace,
or a list of (name namespace-url) if the element does belong to a namespace.

Attributes are stored as (name value) lists.

Children are stored as a list of either element nodes or text nodes.

For example, the following xml document:

    <?xml version="1.0"?>
    <!-- test document -->
    <book title='The Cyberiad'>
      <!-- comment in here -->
      <author xmlns='http://authors'>Stanislaw Lem</author>
      <info:subject xmlns:info='http://bookinfo' rank='1'>&amp;quot;Cybernetic Fables&amp;quot;</info:subject>
    </book>

Would parse as:

    ("book" (("title" "The Cyberiad"))
     (("author" . "http://authors") NIL "Stanislaw Lem")
     (("subject" . "http://bookinfo") (("rank" "1")) "\"Cybernetic Fables\""))

Xmls also includes a helper function, make-node for creating xml nodes
of this form:

    (make-node &key name ns attrs children)

Xmls provides the corresponding accessor functions node-name, node-ns
node-attrs, and node-children.

Usage
================================================================================
The interface is straightforward.  The two main functions are parse and toxml.

    (parse source &key (compress-whitespace t))

Parse accepts either a string or an input stream and attempts to parse the xml
document contained therein.  It will return the s-expr parse tree if it's
successful or nil if parsing fails.

If compress-whitespace is t, content nodes will be trimmed of whitespace and
empty whitespace strings between nodes will be discarded.

   (write-xml xml stream &amp;key (indent nil))

write-xml accepts a lisp list in the format described above and writes the
equivalent xml string to stream.  Currently, if nodes use namespaces xmls will not 
assign namespaces prefixes but will explicitly assign the namespace to each node.  This
will be changed in a later release.

Xmls will indent the generated xml output if indent is non-nil.

    (toxml node &amp;key (indent nil))

Toxml is a convenience wrapper around write-xml that returns the in a newly
allocated string.

Installation
================================================================================
xmls can be installed as a standalone file, or as an asdf system.  An asdf
system definition is provided with the distribution.
