# Release process

Let's see how Docusaurus handles **versioning, releases and breaking changes**.

:::info

This topic is particularly important for highly customized sites that may have difficulties to upgrade.

:::

## Semantic versioning {#semantic-versioning}

Docusaurus versioning is based on the `major.minor.patch` scheme and respects [Semantic Versioning](https://semver.org/).

Respecting Semantic Versioning is important for multiple reasons:

- It **guarantees simple minor version upgrades**, as long as you only use the [public API](##public-api-surface)
- It follows front-end ecosystem conventions
- A new major version is an opportunity to thoroughly document breaking changes
- A new major/minor version is an opportunity to communicate new features through a blog post

:::note

Releasing Docusaurus 2.0 took a very long time. From now on, Docusaurus will **release new major versions more regularly**. In practice, you can expect a new major version every 2–4 months.

[Major version numbers are not sacred](https://tom.preston-werner.com/2022/05/23/major-version-numbers-are-not-sacred.html), but we still group breaking changes together and avoid releasing major versions too often.

:::

### Major versions {#major-versions}

The `major` version number is incremented on **every breaking change**.

Whenever a new major version is released, we publish:

- a blog post with feature highlights, major bug fixes, **breaking changes, and upgrade instructions**.
- an exhaustive changelog entry

:::tip

Read our [public API surface](#public-api-surface) section to clearly understand what we consider as a breaking change.

:::

### Minor versions {#minor-versions}

The `minor` version number is incremented on every significant retro-compatible change.

Whenever a new minor version is released, we publish:

- a blog post with a list of feature highlights and major bug fixes
- an exhaustive changelog entry

:::tip

If you only use our [public API surface](##public-api-surface), you should be able to upgrade in no time!

:::

### Patch versions {#patch-versions}

The `patch` version number is incremented on bugfixes releases.

Whenever a new patch version is released, we publish:

- an exhaustive changelog entry

## Versions {#versions}

```mdx-code-block
import {
  StableMajorVersion,
  NextMajorVersion,
  StableMajorBranchLink,
  NextMajorBranchLink,
} from "@site/src/components/Versions";
```

The Docusaurus team is usually working on 2 major versions at the same time:

- **Docusaurus <StableMajorVersion/>**: the **stable** version, on the <StableMajorBranchLink/> branch
- **Docusaurus <NextMajorVersion/>**: the **next** version, on the <NextMajorBranchLink/> branch

:::note

The <StableMajorBranchLink/> branch is created just before releasing the first v<StableMajorVersion/> release candidate.

:::

### Stable version {#stable-version}

The stable version (v<StableMajorVersion/>, on <StableMajorBranchLink/>) is recommended for most Docusaurus users.

We regularly backport retro-compatible features, bug and security fixes from <NextMajorBranchLink/> to <StableMajorBranchLink/> with `git cherry-pick` to make them available to those not ready for the next version.

:::info

After a new stable version has been released, the former stable version will continue to receive support only for **major security issues** for **3 months**. Otherwise, all features will be frozen and non-critical bugs will not be fixed.

It is recommended to upgrade within that time frame to the new stable version.

:::

### Next version {#next-version}

The next version (v<NextMajorVersion/>, on <NextMajorBranchLink/>) is the version the Docusaurus team is currently working on.

The <NextMajorBranchLink/> branch is the **default target branch** for all pull requests, including core team and external contributions.

This version is recommended for **early adopters** that want to use the latest Docusaurus features as soon as possible. It is also a good way to help us by reporting bugs and giving some feedback.

There are 3 ways to use the next version:

- with an `alpha`, `beta` or `rc` pre-release
- with the `@next` npm dist tag for the latest pre-release
- with a [canary release](./4-canary.md) for the very latest features

:::tip

The next version passes all our automated tests and is used by the Docusaurus site itself. It is relatively safe: don't be afraid to give it a try.

:::

:::caution

Breaking changes can happen on the next version: detailed upgrade instructions are available in the changelog and pull requests.

At the `beta` and `rc` (release candidate) phases, we avoid introducing major breaking changes.

:::

## Public API surface {#public-api-surface}

Docusaurus commits to respecting Semantic Versioning. This means that whenever changes occur in Docusaurus public APIs and break backward compatibility, we will increment the `major` version number.

:::tip

Docusaurus guarantees public API retro-compatibility across `minor` versions. Unless you use internal APIs, `minor` version upgrades should be easy.

:::

We will outline what accounts as the public API surface.

### Core public API {#core-public-api}

✅ Our public API includes:

- Docusaurus config
- Docusaurus client APIs
- Docusaurus CLI
- Preset options
- Plugin options
- Plugin lifecycle APIs
- Theme config
- Core plugins route component props
- `@docusaurus/types` TypeScript types
  - We still retain the freedom to make types stricter (which may break type-checking).

:::tip

For non-theme APIs, any documented API is considered public (and will be stable); any undocumented API is considered internal.

:::

An API being "stable" means if you increment the patch or minor version of your Docusaurus installation without any other change, running `docusaurus start` or `docusaurus build` should not throw an error.

### Theming public API {#theming-public-api}

Docusaurus has a very flexible theming system:

- You can use custom CSS
- You can [swizzle](/docs/swizzling) any React theme component

This system also implicitly creates a very large API surface. To be able to move fast and improve Docusaurus, we can't guarantee retro-compatibility.

✅ Our public theming API includes:

- [Theme class names](/docs/styling-layout#theme-class-names)
- [Infima](/docs/styling-layout#styling-your-site-with-infima) class names and CSS variables
- React components that are [safe to swizzle](/docs/swizzling#what-is-safe-to-swizzle)
- The theme user experience
- Browser support

:::tip

You may not be able to achieve your site customization through this public API.

In this case, please [report your customization use case](https://github.com/facebook/docusaurus/discussions/5468) and we will figure out how to expand our public API.

:::

❌ Our public theming API **excludes**:

- The DOM structure
- CSS module class names with a hash suffix (usually targeted with `[class*='myClassName']` selectors)
- React components that are [unsafe or forbidden to swizzle](/docs/swizzling#what-is-safe-to-swizzle)
- React components that import from `@docusaurus/theme-common/internal`
- The exact visual appearance of the theme

:::note

When [swizzling](/docs/swizzling) safe components, you might encounter components that import undocumented APIs from `@docusaurus/theme-common` (without the `/internal` subpath).

We still maintain retro-compatibility on those APIs (hence they are marked as "safe"), but we don't encourage a direct usage.

:::
