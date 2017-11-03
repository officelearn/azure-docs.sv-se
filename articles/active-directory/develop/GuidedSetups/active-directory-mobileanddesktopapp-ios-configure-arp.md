---
title: "Azure AD v2 iOS komma igång - konfigurera ARP () | Microsoft Docs"
description: "Hur iOS (Swift)-program kan anropa ett API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 50cb4a2803b6aebe8b39ec9fb02da2293c1065fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Lägg till programmets registreringsinformation i appen

I det här steget måste du lägga till det program-Id i projektet:

1.  I `ViewController.swift`, ersätter den rad som börjar med '`let kClientID`' med:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
CTRL + klicka <code>Info.plist</code> öppna snabbmenyn och klicka sedan på: <code>Open As</code>> <code>Source Code</code>
</li>
<li>
Under den <code>dict</code> rot nod, Lägg till följande:
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
            <string>auth</string>
        </array>
    </dict>
</array>
```
