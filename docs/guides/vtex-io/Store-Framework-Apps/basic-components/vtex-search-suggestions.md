---
title: "Suggestions"
slug: "vtex-search-suggestions"
hidden: false
createdAt: "2021-02-23T17:09:08.541Z"
updatedAt: "2022-09-15T11:39:25.159Z"
---

`Suggestions` is a component used to suggest search terms similar to the current query.

## Usage

Add the `search-suggestions` block to the `search-result-layout.desktop` or `search-result-layout.mobile`. For example:

```json
"search-result-layout.desktop": {
    "children": [
        "flex-layout.row#suggestion",
    ],
    "flex-layout.row#suggestion": {
        "children": ["search-suggestions"]
    }
}
```

### Props

| Prop name    | Type     | Description                                                                                                    | Default value |
| ------------ | -------- | -------------------------------------------------------------------------------------------------------------- | ------------- |
| `customPage` | `string` | Defines a custom page to the link of a suggestion. Example: `store.search.custom`. Defaults to `store.search`. |               |

## Customization

In order to apply CSS customizations in this and other blocks, follow the instructions given in the recipe on [Using CSS Handles for store customization](https://developers.vtex.com/docs/guides/vtex-io-documentation-using-css-handles-for-store-customization).

| CSS Handles             |
| ----------------------- |
| `suggestionsList`       |
| `suggestionsListPrefix` |
| `suggestionsListLink`   |
| `suggestionsListItem`   |
