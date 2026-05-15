# Protection SDK (.xcframework) Integration Guide

## Overview

The Protection SDK provides runtime security, integrity verification, environment checks, and application protection features for iOS applications. The SDK is distributed as an `.xcframework` package for easy integration across device and simulator architectures.

This document explains how to install, configure, and use the Protection SDK in your iOS application.

---

### Supported Threat Types
### SSL pinning failure
### Jailbreak detection
### Debugger detection
### Hooking detection
### Runtime manipulation
### Integrity compromise
### Screen recording
### Screen mirroring
### VPN detection

---

# Package Contents

```text
ProtectionSDK.xcframework
README.md
LICENSE
```

---

# Requirements

* iOS 13.0 or later
* Xcode 15 or later
* Swift 5.9+

---

# Integration Methods

## Method 1 — Manual Integration

1. Open your iOS project in Xcode.
2. Drag and drop `ProtectionSDK.xcframework` into the Xcode project navigator.
3. Ensure the following options are enabled:

   * ✅ Copy items if needed
   * ✅ Add to targets
4. Go to:

```text
Target → General → Frameworks, Libraries, and Embedded Content
```

5. Ensure `ProtectionSDK.xcframework` is set to:

```text
Embed & Sign
```

---

## Method 2 — Swift Package Wrapper (Optional)

If your organization provides a Swift Package wrapper around the `.xcframework`, add the package URL through:

```text
File → Add Packages
```

---

# Importing the SDK

In Swift:

```swift
import ProtectionSDK
```

In Objective‑C:

```objective-c
@import ProtectionSDK;
```

---

# Initialization

Initialize the SDK during application startup.

# Configuration

## Environment Selection

```swift
.environment(.development)
.environment(.staging)
.environment(.production)
```

---

# Core Features

## Runtime Protection

The SDK can detect:

* Jailbroken devices
* Runtime hooking
* Debugger attachment
* Emulator execution
* Tampered application binaries
* Reverse engineering attempts

---

## Integrity Verification

```swift
let result = ProtectionManager.shared.verifyIntegrity()

switch result {
case .secure:
    print("Application integrity verified")
case .compromised(let reason):
    print("Integrity compromised: \(reason)")
}
```

---

## Device Risk Evaluation

```swift
let riskLevel = ProtectionManager.shared.deviceRiskLevel
```

Possible values:

```text
low
medium
high
critical
```

---

# Event Callbacks

Register security event listeners.

```swift
ProtectionManager.shared.setEventHandler { event in
    switch event {
    case .jailbreakDetected:
        print("Jailbreak detected")

    case .debuggerDetected:
        print("Debugger attached")

    case .tamperingDetected:
        print("Binary tampering detected")

    default:
        break
    }
}
```

---

# Best Practices

* Initialize the SDK as early as possible.
* Enable release optimizations in production builds.
* Do not expose API keys in public repositories.
* Use server-side validation for high-security workflows.
* Combine device risk evaluation with backend fraud detection.

---

# Build Settings

Ensure the following settings are configured correctly.

## Enable Dead Code Stripping

```text
DEAD_CODE_STRIPPING = YES
```

## Strip Debug Symbols in Release

```text
STRIP_INSTALLED_PRODUCT = YES
```

---

# Troubleshooting

## Framework Not Found

Verify:

* The framework is added to the correct target
* The framework is embedded and signed
* Xcode derived data has been cleaned

Clean derived data:

```text
~/Library/Developer/Xcode/DerivedData
```

---

## Undefined Symbols

Verify:

* Correct SDK version
* Deployment target compatibility
* Required Apple frameworks are linked

---

# Logging

Enable verbose logging for debugging.

```swift
ProtectionManager.shared.enableLogging(true)
```

Disable logging in production.

---

# Updating the SDK

1. Remove the previous `.xcframework`
2. Add the updated version
3. Clean build folder:

```text
Shift + Command + K
```

4. Rebuild the application

---

# Security Recommendations

For maximum protection:

* Use certificate pinning
* Enable server-side token validation
* Obfuscate sensitive business logic
* Rotate API credentials periodically
* Monitor security telemetry events

---

# Version Information

| Component      | Version |
| -------------- | ------- |
| Protection SDK | 1.0.0   |
| Minimum iOS    | 13.0    |
| Swift          | 5.9     |

---

# Flutter Integration

## Add the iOS Framework

1. Open the Flutter iOS project:

```text
ios/Runner.xcworkspace
```

2. Drag and drop:

```text
SecureShield.xcframework
```

into the Xcode project.

3. Ensure:

```text
Embed & Sign
```

is enabled.

---

## Flutter AppDelegate Integration

Update:

```text
ios/Runner/AppDelegate.swift
```

```swift
import UIKit
import Flutter
import SecureShield

@main
@objc class AppDelegate: FlutterAppDelegate {

    var apps: [String] = []
    var errorList: [String] = []

    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {

        GeneratedPluginRegistrant.register(with: self)

        configureSecureShield()
        callBack()

        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }

    private func configureSecureShield() {
        let config = SecureShieldConfiguration(
            enableRuntimeMonitoring: true,
            monitoringInterval: 10,
            failClosedOnPinningError: true,
            expectedTeamIdentifier: "9NPKQVN8KG",
            expectedBundleIdentifier: Bundle.main.bundleIdentifier ?? "com.example.myApp"
        )

        SecureShield.configure(config)

        SecureShield.configure(
            SecureShieldConfiguration(
                monitoredAppSchemes: [
                    "anydesk",
                    "teamviewer",
                    "zoomus",
                    "msteams"
                ]
            )
        )

        SecureShield.start()

        apps = AppDetector.installedApps()

        print("All screen recording apps = \(apps)")

        if SecureShield.isJailbroken() {
            print("Jailbreak detected - Device compromised")
        }
    }

    private func callBack() {
        SecureShield.onThreatDetected { threat in
            DispatchQueue.main.async { [self] in
                switch threat.type {
                case .sslPinningFailure:
                    errorList.append("SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")
                    print("SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")

                case .jailbreak:
                    errorList.append("JAILBREAK DETECTED: The device environment is compromised.")
                    print("JAILBREAK DETECTED: The device environment is compromised.")

                case .debugger:
                    errorList.append("DEBUGGER DETECTED: A debugger is attached to the process.")
                    print("DEBUGGER DETECTED: A debugger is attached to the process.")

                default:
                    errorList.append("Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                    print("Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                }

                self.errorList = Array(Set(errorList))
            }
        }
    }
}
```

---

# React Native Integration

## Add the Framework

1. Open:

```text
ios/YourProject.xcworkspace
```

2. Add:

```text
SecureShield.xcframework
```

3. Set framework embedding to:

```text
Embed & Sign
```

---

## React Native AppDelegate Example

```swift
import UIKit
import React
import React_RCTAppDelegate
import SecureShield

@main
class AppDelegate: RCTAppDelegate {

    var apps: [String] = []
    var errorList: [String] = []

    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {

        configureSecureShield()
        callBack()

        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }

    private func configureSecureShield() {
        let config = SecureShieldConfiguration(
            enableRuntimeMonitoring: true,
            monitoringInterval: 10,
            failClosedOnPinningError: true,
            expectedTeamIdentifier: "9NPKQVN8KG",
            expectedBundleIdentifier: Bundle.main.bundleIdentifier ?? "com.example.myApp"
        )

        SecureShield.configure(config)

        SecureShield.configure(
            SecureShieldConfiguration(
                monitoredAppSchemes: [
                    "anydesk",
                    "teamviewer",
                    "zoomus",
                    "msteams"
                ]
            )
        )

        SecureShield.start()

        apps = AppDetector.installedApps()

        print("All screen recording apps = \(apps)")

        if SecureShield.isJailbroken() {
            print("Jailbreak detected - Device compromised")
        }
    }

    private func callBack() {
        SecureShield.onThreatDetected { threat in
            DispatchQueue.main.async { [self] in
                switch threat.type {
                case .sslPinningFailure:
                    errorList.append("SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")
                    print("SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")

                case .jailbreak:
                    errorList.append("JAILBREAK DETECTED: The device environment is compromised.")
                    print("JAILBREAK DETECTED: The device environment is compromised.")

                case .debugger:
                    errorList.append("DEBUGGER DETECTED: A debugger is attached to the process.")
                    print("DEBUGGER DETECTED: A debugger is attached to the process.")

                default:
                    errorList.append("Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                    print("Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                }

                self.errorList = Array(Set(errorList))
            }
        }
    }
}
```

---

# SwiftUI Integration (.App)

Use the following implementation inside your SwiftUI `.App` entry point.

```swift
import SwiftUI
import SecureShield

@main
struct SecureShieldDemoApp: App {

    @State private var errorList: [String] = []

    init() {
        // 1. Configure the shield for Reverse Engineering protection and SSL Pinning
        let config = SecureShieldConfiguration(
            enableRuntimeMonitoring: true,
            monitoringInterval: 10,
            failClosedOnPinningError: true,
            expectedTeamIdentifier: "9NPKQVN8KG",
            expectedBundleIdentifier: "Neosoft.SecureScreenShotUsingScrollView"
        )

        SecureShield.configure(config)

        // 2. Configure SSL Pinning (Placeholder)
        // To enable SSL Pinning, add your certificate data here:
        /*
        if let certPath = Bundle.main.path(forResource: "api_cert", ofType: "cer"),
           let certData = try? Data(contentsOf: URL(fileURLWithPath: certPath)) {
            SecureShield.enableSSLPinning(certificates: [certData])
        }
        */

        // 3. Start active monitoring for threats
        SecureShield.start()

        // 4. Initial Integrity Check
        if SecureShield.isJailbroken() {
            print("⚠️ Jailbreak detected - Device compromised")
        }
    }

    @MainActor
    func callBack() async {
        // Register a threat handler to react to security breaches
        SecureShield.onThreatDetected { threat in
            DispatchQueue.main.async {
                switch threat.type {
                case .sslPinningFailure:
                    errorList.append("🚨 SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")
                    print("🚨 SSL PINNING ERROR: The server certificate does not match the pinned certificate. The connection might be intercepted!")

                case .jailbreak:
                    errorList.append("⚠️ JAILBREAK DETECTED: The device environment is compromised.")
                    print("⚠️ JAILBREAK DETECTED: The device environment is compromised.")

                case .debugger:
                    errorList.append("🛠 DEBUGGER DETECTED: A debugger is attached to the process.")
                    print("🛠 DEBUGGER DETECTED: A debugger is attached to the process.")

                default:
                    errorList.append("🚨 Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                    print("🚨 Security Threat Detected: \(threat.type) - Level: \(threat.level)")
                }

                self.errorList = Array(Set(errorList))
            }
        }
    }

    var body: some Scene {
        WindowGroup {
            ContentView(errorList: $errorList)
                .preventScreenshot()
                .onAppear() {
                    Task {
                        await callBack()
                    }
                }
        }
    }
}
```
# Swift Integration (SceneDelegate)
---
let window = UIWindow(windowScene: windowScene)
        
        let mainVC = UIStoryboard(name: "Main", bundle: nil)
            .instantiateViewController(withIdentifier: "ViewController") as! ViewController
        
        // Wrap UIKit VC → SwiftUI → ScreenshotPreventView
        
        let navigationController =
                    UINavigationController(rootViewController: mainVC)

        let protectedRoot = ScreenshotPreventView {
                    NavigationControllerWrapper(
                        navigationController: navigationController
                    )
                }
        
        let hostingController =
                    UIHostingController(rootView: protectedRoot)
        
        window.rootViewController = hostingController
        self.window = window
        window.makeKeyAndVisible()



---

# Additional Features

## Screen Recording App Detection

The SDK can detect installed remote-screen or screen-recording related applications.

Example monitored applications:

```text
anydesk
teamviewer
zoomus
msteams
```

---

## Screenshot Protection

SwiftUI Example:

```swift
.preventScreenshot()
```

---

# Support

For integration support or issue reporting, contact your SDK provider.

Example:

```text
support@yourcompany.com
```

---

# License

Copyright © 2026 Your Company.
All rights reserved.
