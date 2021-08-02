# What is the difference between Workspaces and Caching?

We know that persisting data survives after the process with which it was created, has ended. Now, how can the data be manipulated? CircleCi offers a few options for preservation and reuse of that data throughout different jobs. Keeping things simple, workspaces move data between jobs. Caching moves data into and out of jobs. Knowing which task to use will help speed up builds, improve repeatability, and enhance efficiency.

## Caching

What exactly is caching? A cache stores a file or directory of files in object storage. Each job may contain special steps for caching dependencies from previous jobs. The beauty of this is that the persistent data can happen between the same job in different workflows. Meaning, it can reuse data from expensive fetch operations from previous jobs. This may ensure that future instances run faster because they won't need to redo the work. For example, package dependencies managers do not have to re-download everything on every build. It will only need to download new dependencies. However, keep in mind that caching is global within a project. If it's saved on a branch, it will run on other branches.

## Workspaces

Workspaces can be used to pass along unique data built during a job to other jobs, in the workflow. Fundamentally, each workflow is a temporary workspace. Files and directories can be added when declared in a job and each addition creates a new layer in the workspace file system.

With downstream jobs, you can use the workspace for its own needs or add more layers on top. Another benefit is that workspaces can pass along compiled binaries but they need to be uploaded and downloaded again in each job - that can be slower than passing metadata. A common approach to workspaces is to pass generated version numbers from a build job to a deploy job.

Once the workflow has completed, workspaces are no longer available. Word to the wise: Be specific about what persists in work data to avoid storing data in subsequent jobs you don't need.

## Additional Information

When deciding between Caches or Workspaces, use the table below as a quick reference.

| Type       | Lifetime             | Use                                                                                        | Example                                                                                                                                                                                                                                                                  |
|------------|----------------------|--------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Workspaces | Duration of workflow | Attach the workspace in a downstream container with the ```attach_workspace:``` step.   | The ```attach_workspace``` copies and re-creates the entire workspace content when it runs.                                                                                                                                                                      |
| Caches     | Months               | Store non-vital data that may help the job run faster, <br>for example rpm or Gem packages | The ```save_cache``` job step with a ```path``` to a list of directories<br>to add and a ```key``` to uniquely identify the cache <br>(for example, the branch, build number, or revision). Restore the cache with <br>```restore_cache``` and the appropriate ```key``` |