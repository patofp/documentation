---
title: "Tests"
date: "2022-10-05"
sidebar_position: 400
---

```mdx-code-block
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
```

:::tip
On this page, `<package>` can be one of: `web`, `mobile`

:::

The packages contains tests for both the scratch and derived models. Depending on your use case you might not want to run all tests in production, for example to save costs. There are several tags included in the packages to help select subsets of tests. Tags:

- `this_run`: Any model with the `_this_run` suffix
- `scratch`: Any model in the scratch sub directories.
- `derived`: Any of the derived models i.e. page views, sessions and users.
- `primary-key`: Any test on the primary keys of all models in this package.

For example if your derived tables are very large you may want to run the full test suite on the `this_run` tables, which act as the input for the derived tables, but only primary key schema tests on the derived tables to ensure no duplicates. If using such a set up, we would also recommend including the `page/screen_view_in_session_value` data test for the page/screen views derived tables. For Media Player tests depending on the selector chosen it will include the web tests as well as the bespoke media tests.

This is our recommended approach to testing and can be implemented using the selector flag (see [YAML selectors](/docs/modeling-your-data/modeling-your-data-with-dbt/dbt-operation/index.md#yaml-selectors) section for more details) as follows:

<Tabs groupId="dbt-packages" queryString>
<TabItem value="web+mobile" label="Snowplow Web/Mobile" default>

```bash
dbt test --selector snowplow_<package>_lean_tests
```

This is equivalent to:

```bash
dbt test --select snowplow_<package>,tag:this_run # Full tests on _this_run models
dbt test --select snowplow_<package>,tag:manifest # Full tests on manifest models
dbt test --select snowplow_<package>,tag:primary-key,tag:derived # Primary key tests only on derived tables.
dbt test --select snowplow_<package>,tag:derived,test_type:data  # Include the page/screen_view_in_session_value data test
```

Alternatively, if you wanted to run all available tests both in the Snowplow package, and your custom modules:

```bash
dbt test --selector snowplow_<package>
```

</TabItem>
<TabItem value="media" label="Snowplow Media Player">

```bash
dbt test --selector snowplow_web_lean_and_media_player_tests
```

This is equivalent to running the lean tests on the web-model as well as all media_player tests and any tests on any custom models tagged `snowplow_media_player`.

Alternatively, if you wanted to run all available tests in both the Snowplow Web and Media Player package (plus any tests on any custom models tagged `snowplow_media_player`):

```bash
dbt test --selector snowplow_media_player_tests
```

</TabItem>
<TabItem value="ecommerce" label="Snowplow E-commerce">

```bash
dbt test --selector snowplow_ecommerce_lean_tests
```

This is equivalent to:

```bash
dbt test --select snowplow_ecommerce,tag:this_run # Full tests on _this_run models
dbt test --select snowplow_ecommerce,tag:manifest # Full tests on manifest models
dbt test --select snowplow_ecommerce,tag:primary-key,tag:derived # Primary key tests only on derived tables.
```

Alternatively, if you wanted to run all available tests in both the Snowplow e-commerce package and your custom modules:

```bash
dbt test --selector snowplow_ecommerce
```

</TabItem>
</Tabs>
