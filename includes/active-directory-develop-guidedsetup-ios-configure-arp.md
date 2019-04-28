---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 594b4090fcdfa18432563269743b88cb9d2d723b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300612"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i din app

I det här steget måste du lägga till program-ID i projektet:

1. I `ViewController.swift`, ersätter du raden som börjar med ”`let kClientID`' med:

```swift
let kClientID = "[Enter the application Id here]"
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
CTRL + klicka <code>Info.plist</code> öppna snabbmenyn och klickar sedan på: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
Under den <code>dict</code> root node, Lägg till följande:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
        </array>
    </dict>
</array>
```
