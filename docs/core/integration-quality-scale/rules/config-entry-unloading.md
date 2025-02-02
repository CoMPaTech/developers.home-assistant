---
title: "Support config entry unloading"
related_rules:
  - entity-event-setup
---
import RelatedRules from './_includes/related_rules.jsx'

## Reasoning

Integrations should support config entry unloading.
This allows Home Assistant to unload the integration on runtime, allowing the user to remove the integration or to reload it without having to restart Home Assistant.

This improves the user experience, since the user can do more actions without having to restart Home Assistant.

## Example implementation

In the `async_unload_entry` interface function, the integration should clean up any subscriptions and close any connections opened during the setup of the integration.

The method that has to be added to `__init__.py` looks very similar to the `async_setup_entry` method.
In this example we have a listener, stored in the `runtime_data` of the config entry, which we want to clean up to avoid memory leaks.

`__init__.py`:
```python showLineNumbers
async def async_unload_entry(hass: HomeAssistant, entry: MyConfigEntry) -> bool:
    """Unload a config entry."""
    if (unload_ok := await hass.config_entries.async_unload_platforms(entry, PLATFORMS))
        entry.runtime_data.listener()
    return unload_ok
```

:::info
You can also use `entry.async_on_unload` to register a callback that will be called when the config entry is unloaded.
This can be useful to clean up resources without having to keep track of the removal methods yourself.
:::

## Additional resources

More information about config entries and their lifecycle can be found in the [config entry documentation](../../../config_entries_index).

## Exceptions

There are no exceptions to this rule.

## Related rules

<RelatedRules relatedRules={frontMatter.related_rules}></RelatedRules>