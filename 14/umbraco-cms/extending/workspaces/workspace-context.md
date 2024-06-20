---
description: Add additional API to the Context of a Workspace. Enabling UI to communicate across the Workspace.
---

# Workspace Context

{% hint style="warning" %}
This page is a work in progress and may undergo further revisions, updates, or amendments. The information contained herein is subject to change without notice.
{% endhint %}

A Workspace Context is a API that is provided for the scope of a workspace. A workspace most provide a Workspace Context and can provide addtional ones, mainly via the `workspaceContext` Extension Type.

The default Workspace Context of a Workspace is where the logic of the workspace is located.

A Workspace Context can be what makes sense for its Workspace, but the Core Workspaces(for example Context, Media, Member etc) houses these responsibilities:
* The data of the entity that the workspace is working on.
* It must declare the entity type (for example content, media, member, etc.) and provide the unique string for the current entity (for most implementations this is the key written as a GUID, but is can be any string that is unique to the entity).
* It is responsible for loading and saving the data to the server.
* Methods to communicate with the server.


## Integrate property Editor UIs in a Context

If a workspace wants to utilize Property Editor UIs, then it must provide a Property Dataset Context.

Property Editor UIs communicates via a Property Dataset Context, and is therefor the joint between the Workspace Context and the Property Editors.

Read more about Property Dataset Context, which is jet to come.



## Example of Workspace Context Extension

The API will be initiated with the same host as the default Workspace Context.

```typescript
{
    type: 'workspaceContext',
    alias: 'My.WorkspaceContext.Counter',
    name: 'My Counter Context',
    api: 'my-workspace-counter.context.js',
    conditions: [
        {
            alias: 'Umb.Condition.WorkspaceAlias',
            match: 'Umb.Workspace.Document',
        }
    ]
}
```

The code of such an API file could look like this:

```typescript
import {
    UmbController,
    UmbControllerHost,
} from "@umbraco-cms/backoffice/controller-api";
import { UmbContextToken } from "@umbraco-cms/backoffice/context-api";
import { UmbNumberState } from "@umbraco-cms/backoffice/observable-api";

export class MyContextApi extends UmbController {
    #counter = new UmbNumberState(0);
    readonly counter = this.#counter.asObservable();

    constructor(host: UmbControllerHost) {
        super(host);
        this.provideContext(UMB_APP_CONTEXT, this);
    }

    increment() {
        this.#counter.next(this.#counter.value + 1);
    }
}

export const api = MyContextCounterApi;
```

{% hint style="info" %}
Context APIs have to be self-providing. To do so it has to be an Umbraco Controller.
{% endhint %}

A Context Token for a Workspace Context Extension should look like this:

```typescript
export const UMB_APP_CONTEXT = new UmbContextToken<MyContextCounterApi>(
    "UmbWorkspaceContext",
    "My.WorkspaceContext.Counter"
);
```

We recommend using `UmbWorkspaceContext` as the Context Alias for your Context Token. This will ensure that the requester only retrieves this Context if it's present at their nearest Workspace Context. Use the Extension Manifest Alias as the API Alias for your Context Token. For more information, see the [Context API](../backoffice-setup/working-with-data/context-api.md) article.
