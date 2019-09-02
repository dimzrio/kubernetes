### emptyDir
emptyDir are volumes that get created empty when a Pod is created.
While a Pod is running its emptyDir exists. If a container in a Pod crashes the emptyDir content is unaffected. Deleting a Pod deletes all its emptyDirs.

