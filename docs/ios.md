# iOS

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
