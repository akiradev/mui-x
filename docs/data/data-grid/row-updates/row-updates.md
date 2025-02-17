# Data Grid - Row updates

<p class="description">Always keep your rows up to date.</p>

## The `rows` prop

The simplest way to update the rows is to provide the new rows using the `rows` prop.
It replaces the previous values. This approach has some drawbacks:

- You need to provide all the rows.
- You might create a performance bottleneck when preparing the rows array to provide to the grid.

{{"demo": "UpdateRowsProp.js", "bg": "inline", "disableAd": true}}

## The `updateRows` method

If you want to only update part of the rows, you can use the `apiRef.current.updateRows` method.

{{"demo": "UpdateRowsApiRef.js", "bg": "inline", "disableAd": true}}

The default behavior of `updateRows` API is to upsert rows.
So if a row has an id that is not in the current list of rows then it will be added to the grid.

Alternatively, if you would like to delete a row, you would need to pass an extra `_action` property in the update object as below.

```ts
apiRef.current.updateRows([{ id: 1, _action: 'delete' }]);
```

> The free version of the `DataGrid` is limited to a single row update per `apiRef.current.updateRows` call.
> Multiple row updates at a time are supported in [Pro](/x/introduction/licensing/#pro-plan) and [Premium](/x/introduction/licensing/#premium-plan) plans.

## Infinite loading [<span class="plan-pro"></span>](/x/introduction/licensing/#pro-plan)

The grid provides a `onRowsScrollEnd` prop that can be used to load additional rows when the scroll reaches the bottom of the viewport area.

In addition, the area in which `onRowsScrollEnd` is called can be changed using `scrollEndThreshold`.

{{"demo": "InfiniteLoadingGrid.js", "bg": "inline", "disableAd": true}}

## Lazy loading [<span class="plan-pro"></span>](/x/introduction/licensing/#pro-plan)

:::warning
This feature is experimental, it needs to be explicitly activated using the `lazyLoading` experimental feature flag.

```tsx
<DataGridPro experimentalFeatures={{ lazyLoading: true }} {...otherProps} />
```

:::

Lazy Loading works like a pagination system, but instead of loading new rows based on pages, it loads them based on the viewport.
It loads new rows in chunks, as the user scrolls through the grid and reveals empty rows.

The data grid builds the vertical scroll as if all the rows are already there, and displays empty (skeleton) rows while loading the data. Only rows that are displayed get fetched.

To enable lazy loading, there are a few steps you need to follow:

First, set `rowsLoadingMode="server"`.
Then, set `rowCount` to reflect the number of available rows on the server.
Third, set a callback function on `onFetchRows` to load the data corresponding to the row indices passed within `GridFetchRowsParams`.
Finally, replace the empty rows with the newly fetched ones using `apiRef.current.unstable_replaceRows()` like in the demo below.

{{"demo": "LazyLoadingGrid.js", "bg": "inline", "disableAd": true}}

:::warning
The `onFetchRows` callback is called every time a new row is in the viewport, so when you scroll, you can easily send multiple requests to your backend. We recommend developers limit those by implementing debouncing.
:::

:::warning
For now, lazy loading rows does not work with row grouping or tree data.
:::

:::info
In order for filtering and sorting to work you need to set their modes to `server`.
You can find out more information about how to do that on the [server-side filter page](/x/react-data-grid/filtering/#server-side-filter) and on the [server-side sorting page](/x/react-data-grid/sorting/#server-side-sorting).
:::

## High frequency [<span class="plan-pro"></span>](/x/introduction/licensing/#pro-plan)

Whenever the rows are updated, the grid has to apply the sorting and filters. This can be a problem if you have high frequency updates. To maintain good performances, the grid allows to batch the updates and only apply them after a period of time. The `throttleRowsMs` prop can be used to define the frequency (in milliseconds) at which rows updates are applied.

When receiving updates more frequently than this threshold, the grid will wait before updating the rows.

The following demo updates the rows every 10 ms, but they are only applied every 2 seconds.

{{"demo": "ThrottledRowsGrid.js", "bg": "inline"}}

## API

- [DataGrid](/x/api/data-grid/data-grid/)
- [DataGridPro](/x/api/data-grid/data-grid-pro/)
- [DataGridPremium](/x/api/data-grid/data-grid-premium/)
