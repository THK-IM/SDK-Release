# THK IM Binary SDK

This public repository distributes compiled Android and iOS SDK modules only.
SDK implementation source and build credentials remain in private repositories.
Public binaries can be downloaded by anyone without a GitHub token.

## Platforms

- [Android integration](docs/android.md): five AAR modules in `android/maven`.
- [iOS integration](docs/ios.md): five XCFramework modules in Releases, with podspecs in `ios/Specs`.

**No version has been published by this setup yet.** Use an actual published version,
not the placeholder versions in the integration examples.

Release versions are immutable. Android and iOS have independent version numbers;
all five modules of a platform share a version. Source JARs, SDK implementation
Swift files, dSYM files, signing credentials and Demo emoji assets are not distributed.
Binary distribution does not prevent reverse engineering; public APIs remain visible.

## Maintainers

Initialize this repository on `main` with these documents before enabling publication.
Build workflows live in the private source repositories and default to validation only.
Set the Actions repository secret `SDK_RELEASE_TOKEN` in each private source repository;
limit its token to this release repository with Contents read/write.
If your plan supports private environments, optionally enable `environment: sdk-release`
in each workflow and use an environment secret. Required reviewers on private
environments need an eligible plan; they are not assumed by these workflows.
See [GitHub's environment availability](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments).
Customers do not need or receive this credential. Never grant the public repository
workflow access to private source merely to publish binaries.

Do not force-push release history or overwrite an existing artifact version.
Review artifacts before the first public release; preserve third-party notices.
