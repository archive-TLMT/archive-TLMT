---
title: The Archive Top-Level Media Type for File Archives
abbrev: arcmedia
docname: draft-arcmedia-type-01
date: 2015
category: info

ipr: trust200902
area: General
workgroup: 
keyword: Internet-Draft

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
 -  ins: S. Leonard
    name: Sean Leonard
    org: Penango, Inc.
    street:
    - 5900 Wilshire Boulevard
    - 21st Floor
    city: Los Angeles
    region: CA
    code: 90036
    country: USA
    email: dev+ietf@seantek.com
    uri: http://www.penango.com/
 -
    ins: M. Kerwin
    name: Matthew Kerwin
#    street: "-"
#    city: Brisbane
#    region: QLD
#    country: Australia
    email: matthew@kerwin.net.au
    uri: http://matthew.kerwin.net.au/

normative:
  RFC2045:
  RFC2119:
  RFC6838:

informative:


--- abstract

This document defines a new top-level content-type to be known as
"archive", which defines a fundamental type of content with unique
presentational, hardware, and processing aspects.

--- middle

# Introduction

The purpose of this memo is to propose an update to {{RFC2045}} to
include a new top-level content-type to be known as "archive".
{{RFC6838}} describes mechanisms for specifying and describing the
format of Internet Message Bodies via content-type/subtype pairs.
"archive" defines a fundamental type of content with unique
presentational, hardware, and processing aspects.  Various subtypes
of this top-level type are immediately anticipated, and will be covered
under separate documents.


## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in {{RFC2119}}.


# Definition of an archive  {#definition}

An archive top-level media type identifies data that represents one or
more objects along with metadata.  Archives are used to collect
multiple data objects together into a single object for easier
portability and storage. Archive formats can provide many optional
services, including:

1. compression

2. encryption

3. authentication

4. backup

5. filesystem imaging

6. software packaging and distribution

7. volume-splitting (archive split into multiple contents)

8. block storage

Formats and techniques that support one or more of these services
already exist under separate registrations. For example, the Content-
{::comment}
The fact that they already exists invites a concern that this might
create some confusion.  The draft probably should be proactive and
address this possibility. -- Dave Crocker
{:/comment}
Encoding header can be used to compress Internet message content. The
distinguishing feature of the archive top-level type is that these
services are integrated into the format itself, along with the
inclusion of object-specific metadata. Virtually all formats
contemplated under this top-level type are designed to concatenate
multiple objects into a single data stream, along with names and
other metadata. When an Internet-facing application handles content
labeled with this type, it SHOULD provide handling consistent with
the archive as a discrete data item. For example, an Internet mail
user agent would display an archive-labeled type with an archive
icon, possibly with a preview of the objects contained therein (as
opposed to automatically traversing its contents).


# Encoding and Transport  {#encoding}

Unrecognized subtypes of archive SHOULD be treated as
"archive/file".  Like "application/octet-stream", the purpose of the
"archive/file" is to provide default handling; it does not represent
a particular archive format. Implementations SHOULD pass subtypes of
archive that they do not specifically recognize to a robust
general-purpose archive processing application, if such an application
is available.

If default archive (archive/file) handling is not supported, the
archive MAY be treated as if it were "application/octet-stream".

Unless noted in the subtype registration, subtypes of archive MUST
be assumed to contain binary data, implying a content encoding of
base64 for email and binary transfer for ftp and http.

The formal syntax for the subtypes of the archive top-level type SHOULD
look like this:

> Type name:
> : archive
> 
> Subtype name:
> : xxxxxxxx
> 
> Required parameters:
> : none
> 
> Optional parameters:
> : TBD
> 
> Encoding considerations:
> : base64 encoding is recommended when transmitting archive/* documents
>    through MIME electronic mail.
> 
> Security considerations:
> : see {{security}} below
> 
> Published specification:
> : TBD
> 
> Applications that use this media type:
> : TBD
> 
> Fragment identifier considerations:
> : The considerations of this document, plus any extra syntaxes
>    not inconsistent with this document.
> 
> Additional information:
> : Deprecated alias names for this type:
>    : (Include non-archive alias names, such as those in application.)
> : Magic number(s): TBD
> : File extension(s): TBD
> : Macintosh file type code(s): TBD
> {: vspace="0"}
>
> See {{subtypes}} for references to some of the expected subtypes.
>
> Person and email address to contact for further information:
> : TBD
> 
> Intended usage:
> : TBD (COMMON will be the most common)
> 
> Restrictions on usage:
> : TBD
> 
> Author:
> : TBD
> 
> Change controller:
> : TBD
> 
> Provisional registration? (standards tree only):
> : (Yes/No)
> {: vspace="0"}
> 
> (Any other information that the author deems interesting may be
> added below this line.)

The optional parameters consist of starting conditions and variable
values used as part of the subtypes.


# Common Required and Optional Parameters  {#parameters}

Archive formats usually include a range of parameters (meta-data)
within the format.  Consequently, sub-types of archive SHOULD NOT
specify additional parameters that are external to the format.

Regrettably, not all archive formats are as "universal" or "complete"
as one might assume. This is because some archive
formats are very old or are based on older formats where backwards-
compatibility was a design goal; thus they were not designed with
transport across the Internet in mind. The ZIP file is an example:
although the modern ZIP supports Unicode \[CITE\], the default encoding
of ZIP filenames has always been Code Page 437. Since "archive"
contents are literally archives of computing history, sometimes
communicating the archive as-is, rather than updating the archive to
a more universal format, is necessary.

Implementations that are archive-type aware MUST support the
following parameters for maximum compatibility. At the same time, new
archives SHOULD NOT rely on these parameters for disambiguation; new
archives SHOULD be created in such a way that "universal"
interoperability is achieved with the archive's self-contained
information. \[\[TODO: code page--it's like charset but only applies to
certain strings in the archive, when the archive format is ambiguous;
do NOT attempt to apply this parameter as one would apply charset to
text/*. Endian-ness? Time/Y2K representation issues? Anything else?\]\]


# Split Archives  {#splitarchives}

Several archive formats (notably RAR and ZIP) support split archives.
A "split archive" is stored in multiple files
(when stored as multiple files), or more generally, across multiple
storage media.

The ZIP format, for example, actually has two types of splits: "split
archive" and "spanned archive". A "split archive" is a standard ZIP
archive split over multiple files with the file extensions .z01,
.z02, etc.; the .zip file is the last file. A "spanned archive" is
the original format designed for use with swapping floppy disks. All
archive files have the same filename; the format uses volume labels
(presumably on floppy disks) to store disk numbers. Neither sub-
format is merely a naive division of the octet stream: each ZIP file
is parseable in its own right, and contains its own offset values.

The TAR format (or family of formats, including cpio and ustar) was
originally designed for streaming to and from tape devices, so
splitting is accomplished differently.

\[\[TODO: Consider how to label this content. archive/zip^01?
archive/zip; split=01? Something else? How shall 01 be associated
with 02, 03, etc., when the Content-Disposition: ; filename=""
parameter is "presentation-information" and may be separated from the
Content-Type header information?\]\]

{::comment}
Are "split archives" really enough of a thing to warrant mention here, as
opposed to just handling it in the individual registrations for the types that
have the capability? If they are then this section will actually have to
provide clear guidelines on how split arvhives must be labelled. And you might
want to look at message/partial for one approach to that. (In particular, a
file name is definintely not an acceptable means of tying parts of a particular
instance together. You need a unique identifier of some sort, and it needs to
be part of the content-type, not the content-disposition.)
  -- Ned Freed
{:/comment}


# Fragment Identifier Syntax  {#fragments}

As archives usually store objects in hierarchical structures similar to
filesystems, archives can serve as virtual
filesystems. Respondents have noted that the objects stored in an archive can be
addressed by a fragment syntax that resembles a filesystem path. At
the same time, archives can store objects in different ways (along
with different types of metadata), suggesting that a common baseline
with flexible extension points is more appropriate than a fixed
universal syntax. \[\[TODO: This will be explored in future drafts.
Note the similarities with this and the file: URI...\]\]

\[\[TODO: consider how to provide a fragment for content in the
archive. NB: most archives do NOT provide Content-Type/media type
information! So /foo.html being an HTML file is just an *assumption*,
and possibly a very wrong one at that. There is no IETF registry for
file extensions.\]\]


# Piped-Composite Type Suffix Syntax

\[\[TODO: discuss tar piped through bzip2, gzip, etc. as a distinct
file format, rather than an application of the Content-Encoding:
header. Suggest common suffix like archive/tar|bzip2, where \| is some
useful character but not \+ since \+ is for structured syntaxes.\]\]


# Security Considerations  {#security}

Archives can store files, file metadata, and even entire filesystems; thus,
security issues loom large because archives can contain just about
anything. These concerns are magnified by the arbitrary transport of
such data across the Internet. \[\[TODO: complete.\]\]


--- back

# Expected Subtypes  {#subtypes}

The following archive formats will be explored for registration as
subtypes along with this effort:

Archiving Only
: TAR

Multipurpose (archiving, compression, encryption)
: ZIP, ACE, RAR, 7-Zip, StuffIt, FreeArc

Software Packaging
: MSI, RPM, JAR, XPI, CAB, CRX, APK

Disk Imaging
: ISO, NRG, BIN/CUE, VMDK, WIM, PartImage, IMG/IMA/IMZ, DMG
