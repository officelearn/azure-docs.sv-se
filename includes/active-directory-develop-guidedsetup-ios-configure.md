---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: a4b0c2e2d7732c6d2a4f57411731f7145cf3e73e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967672"
---
## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera programmet i något av två sätt, enligt beskrivningen i följande två avsnitt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:

1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2. Ange ett namn för ditt program och din e-post.
3. Kontrollera att alternativet för interaktiva installation är markerat.
4. Följ anvisningarna för att hämta program-ID och klistra in den i din kod.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

1. Gå till [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app).
2. Ange ett namn för ditt program.
3. Kontrollera att alternativet för interaktiva installation är avmarkerat.
4. Välj `Add Platform` och välj sedan `Native Application`.
5. Välj `Save`.
6. Gå tillbaka till Xcode. I `ViewController.swift`, ersätter du raden som börjar med ”`let kClientID`' med program-ID som du just registrerade:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
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
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Ersätt <i> <code>[Your_Application_Id_Here]</code> </i> med program-Id som du just registrerade
</li>
</ol>
