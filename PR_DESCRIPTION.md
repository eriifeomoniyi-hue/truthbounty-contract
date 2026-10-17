# Description

Closes #767

> If this PR was opened in response to a task brief, dependency advisory, or CI remediation, link that context here so reviewers can verify the scope.

---

## Context

This PR resyncs the repository's dependency declarations with the committed lockfile.

- `package.json` devDependencies were pinned to concrete versions.
- `package-lock.json` was regenerated to match those pinned versions and the installed `node_modules` state.

This is a dependency/lockfile hygiene change, not a contract logic change.

### Why this matters

- Pinning avoids silent resolution differences across environments and CI.
- Keeping `package.json` and `package-lock.json` consistent reduces "works on my machine" failures.
- Deterministic lockfile state makes installs and CI less fragile.

## Changes proposed

### What was requested

- Make dependency declarations explicit / aligned with the lockfile.
- Avoid relying on SemVer range resolution for the relevant dev tooling.

### What was done

- Updated `package.json` devDependencies to pinned versions for the following tooling:

  - `@nomicfoundation/hardhat-chai-matchers`
  - `@nomicfoundation/hardhat-ethers`
  - `@nomicfoundation/hardhat-ignition`
  - `@nomicfoundation/hardhat-ignition-ethers`
  - `@nomicfoundation/hardhat-network-helpers`
  - `@nomicfoundation/hardhat-toolbox`
  - `@nomicfoundation/hardhat-verify`
  - `@openzeppelin/hardhat-upgrades`
  - `@typechain/ethers-v6`
  - `@typechain/hardhat`
  - `hardhat`
  - `typescript`

- Regenerated `package-lock.json` to reflect the pinned `package.json`.

#### Files changed

- `package.json`
- `package-lock.json`

## Why pinned versions instead of ranges

- Pinned versions make the installed toolchain reproducible across machines and CI runs.
- Lockfile drift is a common source of intermittent CI failures.
- For developer tooling, deterministic installs usually matter more than automatic minor/patch upgrades through range resolution.

If automatic non-breaking updates are preferred, this can be revisited with a separate Dependabot/Renovate workflow instead of inline manual pinning.

## Backwards compatibility

- [ ] No contract code changed
- [ ] No on-chain behavior changed
- [ ] This is a local/dev dependency update only

### Potential follow effects

Even when a change is only in dev tooling, it can affect:

- Hardhat test runs
- TypeChain generation
- Ignition deploy workflows
- Gas reporting and coverage tooling

If this PR is merged, the next step should be to confirm that `npm install` and the project's test commands still work in CI.

## Verification

### How to verify

1. Remove existing generated artifacts if the project expects a clean install.
2. Install dependencies from the updated `package.json` and `package-lock.json`.
3. Run the project's compile and test commands.
4. Confirm no new resolution errors, version mismatches, or test failures appear.

### Suggested verification commands

- install dependencies
- compile
- run tests
- run gas/coverage suites if they are part of the normal PR process

If you want, I can run those verification commands in this environment before the PR is reviewed.

## Security considerations

- This PR changes dev dependencies only. It does not modify contract code, deployment scripts, or on-chain logic.
- Even so, dev tooling can affect build reproducibility and CI integrity, so the lockfile should be treated as a security-relevant artifact.
- If any updated package later turns out to be compromised or broken, the fix is to revert the pinning and/or update the lockfile again.

## Dependencies

- This PR is self-contained: it only touches `package.json` and `package-lock.json`.
- If other tooling in the repo depends on a specific package version, that should be checked before merge.

## Checklist

- [ ] My code follows the code style of this project.
- [ ] This PR does not contain plagiarized content.
- [ ] The title and description of the PR is clear and explains the approach.
- [ ] I am making a pull request against the intended base branch.
- [ ] My commit messages match the project's requested structure.
- [ ] My code additions will fail neither code linting checks nor unit tests.
- [ ] I am only making changes to files I was requested to change.

## Reviewer notes

- The substantive diff is in `package.json` and `package-lock.json`.
- The main risk is not contract logic but environment reproducibility.
- If the project prefers range-based versions, this PR can be rewritten to match that policy instead of pinning.

## References

- Issue: #767
- Changed files: `package.json`, `package-lock.json`
