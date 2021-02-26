# Npm

## Avoid permission errors when running inside a container
When running npm inside a container, hooks (e.g. `postinstall`) may error with something similar to
```
npm WARN lifecycle myproject@1.0.0~postinstall: cannot run in wd myproject@1.0.0 node scripts/myhook.js (wd=/myproject)
```
This is caused by npm lowering its own permissions and not being able to write to the root-owned files & folders in the container.

Solution: specify `--unsafe-perm` to npm.
