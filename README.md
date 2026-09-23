# THK IM Binary SDK

本仓库公开分发 Android / iOS 的五个二进制 SDK 模块；客户拉取依赖无需 GitHub Token。
SDK 实现源码和构建凭据保留在私有仓库。两端独立版本号，同一平台的五个模块使用相同版本。

<!-- android-release:start -->
<!-- android-version: 0.5.9 -->
## Android

最新版本：`0.5.9`（发布 tag：`android-v0.5.9`）。

Add the public Maven directory in `settings.gradle`:

```groovy
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
        maven { url 'https://raw.githubusercontent.com/vizoss/webrtc-build/maven-repo/' }
        exclusiveContent {
            forRepository {
                maven { url 'https://raw.githubusercontent.com/THK-IM/SDK-Release/main/android/maven' }
            }
            filter { includeGroup 'io.github.thk-im' }
        }
    }
}
```

Select the modules used by your application, replacing `VERSION` with a published version:

```groovy
implementation 'io.github.thk-im:core:0.5.9'
implementation 'io.github.thk-im:ui:0.5.9'
implementation 'io.github.thk-im:provider:0.5.9'
implementation 'io.github.thk-im:preview:0.5.9'
implementation 'io.github.thk-im:rtc:0.5.9'
```

`ui` depends on `core`; `provider` and `preview` depend on `core` and `ui`;
`rtc` depends on `core`. Declare modules whose public types your application imports
explicitly. Do not use `+`, `latest.release` or SNAPSHOT versions.

No credentials are needed for SDK downloads. Third-party dependencies still use their
own repositories; moving SDK publication does not remove Maven Central as a dependency source.
The minimum Android version is API 24; the SDK is currently built with compileSdk 36.
<!-- android-release:end -->

<!-- ios-release:start -->
<!-- ios-version: unpublished -->
## iOS

最新版本：尚未发布。下方 VERSION 仅为占位符，首次发布后自动替换。

Each SDK module has its own Swift module and XCFramework. Binary downloads from
public GitHub Releases and podspec downloads do not require a token.

Add this spec repository and CocoaPods' public specs to your Podfile:

```ruby
source 'https://github.com/THK-IM/SDK-Release.git'
source 'https://cdn.cocoapods.org/'
platform :ios, '13.0'
use_frameworks!

target 'YourApp' do
  pod 'THKIMCore', 'VERSION'
  pod 'THKIMUI', 'VERSION'
  # Optional modules:
  # pod 'THKIMProvider', 'VERSION'
  # pod 'THKIMPreviewer', 'VERSION'
  # pod 'THKIMRTC', 'VERSION'
end

# Compatibility setup used by the binary integration tests.
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '13.0'
    end
  end
  # Some pinned third-party sources import a header absent from recent SDKs.
  Dir.glob('Pods/**/*.{m,mm}').each do |file|
    content = File.read(file)
    next unless content.include?('#import <netinet6/in6.h>')
    File.chmod(0644, file)
    File.write(file, content.gsub('#import <netinet6/in6.h>', ''))
  end
end
```

Merge this hook with an existing `post_install`; do not define it twice. If the
application requires a higher iOS minimum, use that minimum instead of `13.0`.

The release process also installs the podspecs at the standard root `Specs/` path
for CocoaPods indexing; `ios/Specs/` is the platform-organized copy.

Import the selected modules (`import THKIMCore`, `import THKIMUI`, etc.) instead of
the former single `import THKIMSDK`. UI requires Core. Provider, Previewer and RTC
require Core and UI, but not one another. Selecting only Core must not install
the UI or WebRTC dependency trees.

Provider and RTC use external binary pods. The release metadata must include their
tested specs in this repository so customers do not need private source access.
Use one SDK version for all selected modules and keep the resolved Podfile.lock.

XCFrameworks contain device and simulator slices, public Swift interfaces and
required resources. Public interfaces are necessary for compilation; implementation
source is not shipped. Third-party Swift ABI/toolchain compatibility must be verified
for each supported Xcode version; module stability alone is not a blanket guarantee.
The initial build and integration checks use Xcode 26.6. Older Xcode releases are
not yet part of the validated compatibility matrix.
<!-- ios-release:end -->

## 发布与版本管理

- 私有源码仓库推送 main 历史上的 `v版本号` tag 后，自动构建、验证并发布；手动运行仍可选择仅验证。
- Android 发布 tag 为 `android-v版本号`；iOS 为 `ios-v版本号`，互不冲突。
- 每次发布将平台产物/规格及本 README 的对应平台版本一起提交，并原子推送 main 与新 tag。新 tag 指向该次 main 发布提交。
- 历史版本和 tag 不覆盖、不移动；后续另一平台发布会继续推进 main，不会修改上一平台的历史 tag。
- iOS ZIP 上传完成后再发布 GitHub Release。若发布中断，先检查运行日志与草稿，不要覆盖同版本产物或强推 tag。
- 两个私有仓库均需要 `SDK_RELEASE_TOKEN`，仅授予本仓库 Contents 读写权限；客户不需要此凭据。
- 不分发 source JAR、SDK 实现 Swift 文件、dSYM、签名凭据或 Demo 表情资源。公开接口仍然可见，二进制不等于防逆向。
- 第三方依赖的许可证与声明应保留。
