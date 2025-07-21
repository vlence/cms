# cms
An implementation of a generic CMS.

CMS is short for Content Management System. What is Content? Depends entirely on the user. It could be a blog post, an image, a blog post with an image, etc.

The usefulness, or lack thereof, of a CMS comes from its ability to represent the user's content and how easy it is to manage that content.

## Concepts

### Machine Data Types

All data is just 1s and 0s. This is generally not directly useful so we have abstractions like unsigned numbers, floating point numbers and strings. `cms`
assumes all data is one of the following data types:
- Unsigned 32-bit integer
- Unsigned 64-bit integer
- Signed 32-bit integer
- Signed 64-bit integer
- Single precision 32-bit IEEE floating point number
- Double precision 64-bit IEEE floating point number
- UTF-8 encoded strings
- Booleans

`cms` knows these as machine data types.

### Human Data Types

The data types above are useful but by themselves can be too cumbersome to work with. Content usually consists of data which is a combination of multiple types.
For example a blog post can have a title, a body and a timestamp.
- The title is obviously a string
- The body can be rich text, which internally can just be a string.
- The timestamp can be a string or a number

How the data is stored/encoded at the hardware level is usually not the main focus of a CMS. It's important but there are other goals. Consider the title. We
might want our users to be able to search for posts by title. Or consider the timestamp, maybe we want to sort posts latest to oldest. Additionally we don't
want the title to be too long! Maybe we want it to be limited to just 256 characters.

So there's three main questions a CMS tries to answer:
1. How can the data be stored/encoded efficiently?
2. How can the data be searched/indexed efficiently?
3. How can the data be verified for correctness?

`cms` does not assume it can answer any of the above questions questions but rather expects the user to define human data types. A human data type is for the
human, not the machine. The underlying type is a machine data type but what the user sees and interacts is suitable for humans. A human data type wraps a
machine data type and specifies how the data is indexed and verified. So in our example we could define the following human data types:
1. `ShortText` for the title. This can check how long the text is. Indexing strategy could include case-insensitive search or exact matches.
2. `RichText` for the body. Indexing strategy could allow full-text search on the text content.
3. `DateTime` for the timestamp. Indexing strategy could allow sorting.

### Content Definitions

One size does not fit all. One user of `cms` may define a blog post as having a title, body and timestamp. Another may require a cover photo as well. `cms`
provides an API that allows the user to define their content as a series of human data types called fields. A field has a name, an optional description and
a human data type.

Fields define the indexing strategy for the underlying machine data type. The indexing strategy is tied to the field and not the human data type to support
different indexing strategies for two fields using the same human data type. For example a title field and description field could both be using the
`ShortText` human data type. The title field could use an indexing strategy that allows exact matches but the description field may instead use the full-text
search indexing strategy, since exact matching doesn't make sense here.

### Content Relationships

Content seldom exists by itself. For example a blog post could have related blog posts or a "next" and "previous" post. To model this we can simply allow
content to be related to other content and give these relationships names.

## The Main Idea

`cms` allows its users to define human data types, verification checks and indexing strategies. These can be used to define fields and content definitions.
Fields live within content definitions and do not make any sense outside of one. Content definitions can be related to each other.

These basic ideas can be used to represent, create and manage content.

`cms` does not provide any way to render the content. Rendering content, for example to HTML, is entirely up to the user.
