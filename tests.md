CreateBook Message

```javascript
{
  "name": "{{book_name}}",
  "author_id": {{author_id}},
  "publisher_id": {{publisher_id}},
  "publication_year": {{publication_year}}
}
```

CreateBook Scripts Before Invoke

```javascript
const uniqueName = `Postman Test Book ${Date.now()}`;

pm.variables.set("book_name", uniqueName);
pm.variables.set("author_id", 42);
pm.variables.set("publisher_id", 123);
pm.variables.set("publication_year", 3027);
```

CreateBook Scripts After Response

```javascript
const responseData = pm.response.messages.idx(0).data;
const book = responseData.book || responseData;

pm.test("Created book exists", function () {
  pm.expect(book).to.exist;
});

pm.test("Created book has id", function () {
  pm.expect(book.id).to.exist;
});

pm.test("Created book name matches", function () {
  pm.expect(String(book.name)).to.eql(String(pm.variables.get("book_name")));
});

pm.collectionVariables.set("created_book_id", String(book.id));
pm.collectionVariables.set("created_book_name", String(book.name));
pm.collectionVariables.set("created_author_id", String(book.author_id));
pm.collectionVariables.set("created_publisher_id", String(book.publisher_id));
pm.collectionVariables.set(
  "created_publication_year",
  String(book.publication_year),
);

console.log(
  "saved created_book_id:",
  pm.collectionVariables.get("created_book_id"),
);
console.log(
  "saved created_book_name:",
  pm.collectionVariables.get("created_book_name"),
);
```

GetBookById Message

```javascript
{
    "book_id": 1000
}
```

GetBookById Scripts Before Invoke

```javascript
pm.test("created_book_id is available", function () {
  const createdBookId = pm.collectionVariables.get("created_book_id");
  pm.expect(createdBookId).to.exist;
});
```

GetBookById Scripts After Response

```javascript
const responseData = pm.response.messages.idx(0).data;
const book = responseData.book || responseData;

pm.test("Returned book has id 1000", function () {
  pm.expect(String(book.id)).to.eql("1000");
});

pm.test("Returned book has expected name", function () {
  pm.expect(String(book.name)).to.eql(
    "Harry Potter and the Sorcerer's Stone (Book 1)",
  );
});

pm.test("Returned book has author_id 394", function () {
  pm.expect(String(book.author_id)).to.eql("394");
});

pm.test("Returned book has publisher_id 74", function () {
  pm.expect(String(book.publisher_id)).to.eql("74");
});

pm.test("Returned book has publication_year 1976", function () {
  pm.expect(Number(book.publication_year)).to.eql(1976);
});
```

GetBookById = 666 Message

```javascript
{
    "book_id": 666
}
```

GetBookById = 666 Scripts After Response

```javascript
pm.test("Missing book returns NOT_FOUND", () =>
  pm.expect(pm.response.statusCode).to.eql(5),
);
```

WatchBooks Message

```javascript
{
}
```

WatchBooks Scripts Before Invoke

```javascript
pm.collectionVariables.set("watchbooks_found", "false");
pm.collectionVariables.unset("watched_book_id");
```

WatchBooks Scripts On Message

```javascript
const raw = pm.message.toJSON ? pm.message.toJSON() : pm.message;
const responseData = raw.data ? raw.data : raw;
const book = responseData.book || responseData;

const expectedId = String(pm.collectionVariables.get("created_book_id"));
/*
const expectedName = String(pm.collectionVariables.get("created_book_name"));
const expectedAuthorId = String(pm.collectionVariables.get("created_author_id"));
const expectedPublisherId = String(pm.collectionVariables.get("created_publisher_id"));
const expectedYear = String(pm.collectionVariables.get("created_publication_year"));
*/
const actualId = book ? String(book.id) : "";
/*
const actualName = book ? String(book.name) : "";
const actualAuthorId = book ? String(book.author_id) : "";
const actualPublisherId = book ? String(book.publisher_id) : "";
const actualYear = book ? String(book.publication_year) : "";
*/
console.log("expectedId:", expectedId);
/*
console.log("expectedName:", expectedName);
console.log("expectedAuthorId:", expectedAuthorId);
console.log("expectedPublisherId:", expectedPublisherId);
console.log("expectedYear:", expectedYear);
*/

console.log("actualId:", actualId);
/*
console.log("actualName:", actualName);
console.log("actualAuthorId:", actualAuthorId);
console.log("actualPublisherId:", actualPublisherId);
console.log("actualYear:", actualYear);
*/
const idMatches = actualId === expectedId;
/*
const fieldMatches =
    actualName === expectedName &&
    actualAuthorId === expectedAuthorId &&
    actualPublisherId === expectedPublisherId &&
    actualYear === expectedYear;
*/
console.log("idMatches:", idMatches);
// console.log("fieldMatches:", fieldMatches);
/*
if (idMatches || fieldMatches) {
    pm.collectionVariables.set("watchbooks_found", "true");
    pm.collectionVariables.set("watched_book_id", actualId);
}
*/
```

WatchBooks Scripts After Response

```javascript
pm.test("WatchBooks saw the created book", function () {
  pm.expect(pm.collectionVariables.get("watchbooks_found")).to.eql("true");
});

pm.test("WatchBooks received the same id as CreateBook", function () {
  pm.expect(pm.collectionVariables.get("watched_book_id")).to.eql(
    pm.collectionVariables.get("created_book_id"),
  );
});
```
