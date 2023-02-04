---
sidebar_position: 3
---

# Storage

Juno Storage is designed for app developers who need to store and serve user-generated content, such as photos or videos.

It offers a powerful and cost-effective object storage solution on the blockchain.

:::note

To use Juno Storage's features, you must [install](../add-juno-to-an-app/install-the-sdk-and-initialize-juno.md) and initialize the Juno SDK in your app.

:::

## How does it work?

Each [satellite] you create has a "Storage," which can have as many collections as you wish.

A collection is identified by a customizable key, which is a `string`.

Each collection can hold a list of assets, which consist of indexation information (such as a path), content (a blob), and a technical user

The technical user is used to grant read and write permissions. Unlike the [datastore](datastore.md), the keys of the assets must be unique across all collections.

:::warning

Unless you use the optional `token` parameter to persist an asset in your [satellite] and make its URL difficult to guess, any asset stored in Juno Storage will be publicly available on the internet.

:::

## Limitation

Each [satellite] can hold up to 32GB of data for all services combined.

## Collections and rules

Collection and their rules can be created or updated in the [storage](https://console.juno.build/storage) view in Juno's console under tab "Rules".

Collections and their rules can be created or updated in the Juno console's [storage](https://console.juno.build/storage) view under the "Rules" tab.

A collection is identified by a `key`, a `string`.

Read and write permissions can be defined as follows:

:::warning

Assets remain publicly available on the internet regardless of the permission schema. The rules are only applied when accessing the data through the library.

:::

- `public`: anyone can read and write in the related collection.
- `private`: only a registered user can read and write in the related collection. Note that even you, the owner of the [satellite], cannot access the data stored in this collection.
- `managed`: both the user and the [controllers] read and write in the related collection.
- `controllers`: only the [controllers] can read and write in the related collection.

:::tip

- Rules can be modified at any time and changes will be immediately applied
- Any collection with read permission set as`public`, `managed` or `controllers` can be viewed in the Juno console's [storage](https://console.juno.build/storage) view.

:::

In addition to the permissions, you can set an optional parameter to limit the size (in bytes) of the assets that can be uploaded in a collection.

## Upload asset

To upload an asset, use the following code:

:::note

Uploading a file with the same name as an existing file will overwrite the previous file.

:::

```typescript
import { uploadFile } from "@junobuild/core";

const result = await uploadFile({
  data,
  collection: "images",
});
```

The `data` parameter is the file you want to upload. This is commonly selected using an HTML `<input type="file" /> ` element.

The `uploadFile` function provides various options, including:

- `filename`: By default, Juno will use the file's filename. You can overwrite this and provide a custom filename.
- `fullPath`: Juno will automatically compute the `fullPath`, which is the unique path (or key) that is used to make the asset available on the internet. The `fullPath` is the filename encoded as a URL and prefixed with `/`.
- `headers`: The headers can affect how the browser handles the asset. If no headers are provided, Juno will extract the `Content-Type` from the file type.
- `encoding`: The type of encoding for the file. For example, `'identity' | 'gzip' | 'compress' | 'deflate' | 'br'`.

### Protected asset

While all assets can be found on the internet, it is possible to make their URL difficult to guess so that they remain undiscoverable (**as long as they are not shared**) and considered "private".

Juno achieves this by using the `token` parameter.

```typescript
import { uploadFile } from "@junobuild/core";
import { nanoid } from "nanoid";

const result = await uploadFile({
  data,
  collection: "images",
  token: nanoid(),
});
```

For example, consider uploading a file "mydata.jpg" with a token. Attempting to access it through the URL "https://yoursatellite/mydata.jpg" will not work. The asset can only be retrieved if a token is provided: "https://yoursatellite/mydata.jpg?token=the-long-nanoid-generated".

## List assets

The "Storage" provider offers a way to list assets.

```typescript
import { listAssets } from "@junobuild/core";

const myList = await listAssets({
  collection: "images",
  filter: {},
});
```

The `listAssets` function - in addition to specifying the collection to query - accepts various optional parameters:

- `matcher`: a regex to apply to the assets' `fullPath`
- `paginate`: an object used to query a subset of the assets
- `order`: requests entries sorted in ascending or descending order

## Delete asset

To delete an asset, it must be listed first. This ensures that only the most recent asset is deleted.

```typescript
import { deleteAsset } from "@junobuild/core";

await deleteAsset({
  collection: "images",
  storageFile: myAsset,
});
```

[satellite]: ../terminology.md#satellite