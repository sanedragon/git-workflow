git-workflow
============
The purpose of this repository is to document some ideas about git workflow.

There is often a conflict between having a clean history that makes it easy to see when and why
changes were made, and avoiding rewriting history, so that it is possible for development
branches to be rebased without difficulty. This workflow is an attempt to get the best of both
worlds.

Branches
--------
- master: Stable branch suitable for users or casual developers. There is a convincing argument
  that it is better to avoid a branch named master since different workflows use the branch
  differently.
    - Rewriting history is not allowed.
    - All commits to this branch should be merges of release candidate branches.

- candidate: A proving ground and common base for changes for the next release.
    - This branch is on top of master, that is, it includes every commit from master.
    - Rewriting history is allowed but it will require communication with all contributors to the
      release, so they do not accidentally add removed commits back in.
    - Reverts can be tricky, and clutter the history, so if a feature needs to be delayed
      to a later release, prefer rewriting history to remove the merge commit rather than reverting
      it. If a fix is available prior to the release deadline, just merge the fix to candidate.
    - All commits to this branch should be merges of topic branches.
    - Continuous integration testing goes here.
    - Prior to release, do a feature freeze (stop adding new features to the release) and do your
      manual regression testing and QA.
    - Merge to master as the last step of deployment, after checking that the live environment is
      working properly. Merge with --no-ff, to be sure to get a merge commit.

- topic: A branch for feature development. The branch should have the owner identified, the
  subject matter identified and if you are using a work ticket tracker, the ticket identifier
  should appear in the branch name also.
    - This branch is on top of the candidate branch for the next release, or the branch for the
      release the feature is planned for.
    - Rewriting history is encouraged. Create large unfinished checkpoint commits that will later
      be split up into smaller manageable portions. Create lots of small commits to manage bug fixes
      and alternative implementations that will later be squashed. When ready for merge, the commits
      should be focussed but few. Each commit should have working code, or at a bare minimum, should
      compile cleanly. This way, if you have a subtle bug, it's easier to use git bisect to find
      where it was introduced. In addition, each commit should focus on a single feature, component or
      bug fix. It should be clear from the commit message what the change is and why it is needed,
      either a work ticket reference or a prose explanation.
    - Always merge to candidate with the --no-ff option, to be sure to get a merge commit.