# slate-sugar

[![NPM version](https://badge.fury.io/js/slate-sugar.svg)](http://badge.fury.io/js/slate-sugar)
[![Build Status](https://travis-ci.org/GitbookIO/slate-sugar.png?branch=master)](https://travis-ci.org/GitbookIO/slate-sugar)

> Create Slate documents using JSX.

The purpose of slate-sugar is to make Slate nodes and documents creation:

* Painless by using smart defaults and inferring properties based on the input
* Comprehensible by offering a declarative way to create structured documents

## Install

```
yarn add slate-sugar
```

## Usage

### Without Defaults

```jsx harmony
/* @jsx h */
import createHyperscript from 'slate-sugar';

const h = createHyperscript();
const document = (
    <document>
        <heading kind="block" data={{ id: 'introduction' }}>
            Introduction
        </heading>
        <paragraph kind="block">
            This is a super <text marks={['bold']}>bold</text> paragraph.
            Also, it has a <link kind="inline" data={{ href: '/' }}>link</link> in it.
        </paragraph>
    </document>
);
```

### With Defaults

```jsx harmony
/* @jsx h */
import createHyperscript from 'slate-sugar';

const h = createHyperscript({
    blocks: [
        'heading',
        'paragraph'
    ],
    inlines: [
        'link'
    ],
    marks: [
        'bold'
    ]
});
const document = (
    <document>
        <heading id="introduction">
            Introduction
        </heading>
        <paragraph>
            This is a super <bold>bold</bold> paragraph.
            Also, it has a <link href="/">link</link> in it.
        </paragraph>
    </document>
);
```

### With Custom Defaults

```jsx harmony
/* @jsx h */
import createHyperscript from 'slate-sugar';

const h = createHyperscript({
    blocks: [
        'heading',
        'paragraph'
    ],
    inlines: [
        'link'
    ],
    marks: [
        'bold'
    ],
    voids: [
        'image'
    ],
    doc: () => ({ kind: 'document' })
}, {
    voids: ({ type, ...data }) => ({
        type,
        kind: 'block',
        isVoid: true,
        data
    })
});
const document = (
    <doc>
        <heading id="introduction">
            Introduction
        </heading>
        <paragraph>
            This is a super <bold>bold</bold> paragraph.
            Also, it has a <link href="/">link</link> in it.
            <image src="/super-image.png" />
        </paragraph>
    </doc>
);
```

## Documentation

### `createHyperscript([groups], [groupsTransformer])`

```
type Transformer = ({ type, ...otherProps }) => Object
type Group = string[] | Transformer
```

* `groups <name: string, Group>`: if `Group` is `string[]`, the matching `Transformer` is taken from `groupsTransformer`. If it's a `Transformer`, it will be used to generate new props for tags matching `name`.
* `groupsTransformer <name: string, Transformer>`: used to transform groups' props with the same name.

Returns a JSX-compatible function.

By default, there are two `Transformer`s:

* `document`: creates a `Slate.Document` for `<document />` tags
* `text`: creates a `Slate.Text` for `<text />` tags and add marks if passed (`<text marks={[...]} />`)

There are also three groups `Transformer`s:

* `blocks`: creates a block with type being the tag name, the rest is considered data.
* `inlines`: creates an inline with type being the tag name, the rest is considered data.
* `marks`: creates a text with type being a mark applied to the text, the rest is considered the mark's data.
