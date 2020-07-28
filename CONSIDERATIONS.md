# Considerations


## Fix or fail?

This role make quite some choices, here are the thoughts on why to take a descicion.

1. Fix if the consequences are limited.
2. Fail if changing has consequences.

### Examples

#### 1.1.1.1 Ensure mounting of cramfs filesystems is disabled (Scored)

Disabling cramfs seems straight forward, has limited consequences: Fix

#### 1.1.2 Ensure /tmp is configured

The /tmp directory can be just a directory. Remounting it to it's own filesystem is more involved. For example, files have to be moved: Fail

## Variables

All rules must be selectable with a variable so that consumers of the role can overwrite the CIS guidelines.
