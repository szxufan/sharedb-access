## sharedb-access
[![NPM](https://nodei.co/npm/sharedb-access.png?downloads=true)](https://nodei.co/npm/sharedb-access/)

### Note



### Installation

- Install: `npm install sharedb-access`


### Usage

```js
const shareDbAccess = require('sharedb-access')
shareDbAccess(backend)
```

Using `sharedb-access` you can control `create`, `read`, `update`, and `delete` 
database operation for every collection. You can use two types of rules: 
`allow` and `deny`. By default all the operations are denied. So, you should
add some rules to allow them. If at least one `allow`-rule allows the write, and
no `deny`-rules deny the write, then the write is allowed to proceed. 

You can call `allow` and `deny`-rules as many times as you like. The functions 
should return `true` if they think the operation should be allowed for `allow` 
rules and denied for `deny`-rules. Otherwise they should return `false`, or 
nothing at all (`undefined`).

#### Create

```js
// Allow create-operation for collection 'items'

// docId - id of your doc for access-control
// doc   - document object
// session - your connect session

backend.allowCreate('items', async (docId, doc, session) => {
  return true
})

// Deny creation if user is not admin
backend.denyCreate('items', async (docId, doc, session) => {
  return !session.isAdmin
})

// So, finally, only admins can create docs in 'items' collection
// the same results is if you just write:

backend.allowCreate('items', async (docId, doc, session) => {
  return session.isAdmin
})
```
#### Read

Interface is like `create`-operation

```js
backend.allowRead('items', async (docId, doc, session) => {
  // Allow all operations
  return true
})

backend.denyRead('items', async (docId, doc, session) => {
  // But only if the reader is owner of the doc
  return doc.ownerId !== session.userId
})
```

#### Delete

Interface is like `create`-operation

```js
backend.allowDelete('items', async (docId, doc, session) => {
  // Only owners can delete docs
  return doc.ownerId === session.userId
})

backend.denyDelete('items', async (docId, doc, session) => {
  // But deny deletion if it's a special type of docs
  return doc.type === 'liveForever'
})
```

#### Update

```js
// docId - id of your doc for access-control
// oldDoc  - document object (before update)
// newDoc  - document object (after update)
// ops    - array of OT operations
// session - your connect session

const allowUpdateAll = async (docId, oldDoc, newDoc, ops, session) => {
  return true
}

backend.allowUpdate('items', allowUpdateAll);
```

## MIT License 2017 by Artur Zayats
