## CSF Timezone Module Documentation

**Table of Contents:**

* [Overview](#overview)
* [Monkey Patches](#monkey-patches)
* [Hooks](#hooks)
* [Connection Patch](#connection-patch)
* [Console Logging](#console-logging)

### Overview

The `csf_tz` module provides a mechanism for modifying Frappe/ERPNext functionality by leveraging "monkey patching" - dynamically altering existing code at runtime. This module utilizes a combination of hooks and connection patching to ensure consistent application of these modifications. 

### Monkey Patches

The `monkey_patches` directory within the `csf_tz` application is responsible for housing custom code that modifies existing Frappe/ERPNext functionality. This directory will typically contain Python files representing specific modifications.

**Loading Monkey Patches:**

The `load_monkey_patches` function is responsible for loading all monkey patch modules within the `monkey_patches` directory. It iterates through the directory, importing each Python file except for `__init__.py`. The function utilizes `importlib.import_module` to dynamically import the modules.

**Patching Mechanism:**

The `csf_tz` module implements two primary mechanisms for applying monkey patches:

1. **Hooks:** The `get_hooks` function intercepts calls to Frappe's built-in `get_hooks` function, ensuring that the monkey patches are loaded before any hooks defined by the core Frappe/ERPNext application or other custom applications are processed. 
2. **Connection Patch:** The `connect` function patches Frappe's `connect` function, which establishes a connection to the database. This ensures that the monkey patches are loaded once a connection is established, guaranteeing that they are applied before any application code is executed.

**Example:**

```python
# monkey_patches/my_patch.py

def my_patched_function(*args, **kwargs):
    # Custom implementation of the patched function
    print("Custom logic applied!")
    return original_function(*args, **kwargs)

original_function = frappe.get_attr("original_module.original_function")
frappe.get_attr("original_module.original_function") = my_patched_function
```

In this example, the `my_patched_function` overrides the `original_function` by replacing its reference within the `original_module`.

### Hooks

The `csf_tz` module utilizes a simple approach to integrating custom logic with Frappe/ERPNext's hook system. The `get_hooks` function intercepts calls to Frappe's built-in `get_hooks` function, ensuring that the monkey patches are loaded before any hooks defined by the core Frappe/ERPNext application or other custom applications are processed.

**Example:**

```python
# monkey_patches/my_hook.py

def my_custom_hook():
    print("Custom hook logic executed!")

frappe.get_hooks = get_hooks

# Frappe's hook system will execute this custom hook
frappe.get_hooks().my_custom_hook = my_custom_hook
```

### Connection Patch

The `connect` function patches Frappe's `connect` function. This ensures that the monkey patches are loaded once a connection is established, guaranteeing that they are applied before any application code is executed.

**Example:**

```python
# Example of using the connection patch to load monkey patches
frappe.connect(site="my_site", user="my_user", password="my_password")

# Monkey patches are loaded after connection establishment 
```

### Console Logging

The `console` function provides a simple mechanism for logging messages to the Frappe console. It utilizes Frappe's real-time messaging functionality to publish the data to the console.

**Example:**

```python
# Sending a message to the console
frappe.console("This message will be logged to the console.")

# Sending a list of messages
frappe.console(["This is message 1", "This is message 2"])
```

This function allows for debugging and logging within the context of Frappe/ERPNext's environment. 
