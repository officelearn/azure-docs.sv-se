---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: brandwe
ms.custom: include file
ms.openlocfilehash: 331d16df55e26df5d49555c636b307499dd052af
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843275"
---
## <a name="register-your-application"></a>Registrera ditt program
Du kan registrera programmet i något av två sätt, enligt beskrivningen i följande två avsnitt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge
Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Registrera ditt program via den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Ange ett namn för ditt program och din e-post
3.  Kontrollera att alternativet för interaktiva installation är markerat
4.  Följ anvisningarna för att hämta program-ID och klistra in den i din kod

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

1.  Gå till [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app)
2.  Ange ett namn för ditt program
3.  Kontrollera att alternativet för interaktiva installation är avmarkerat
4.  Klicka på `Add Platform`och välj sedan `Native Application` och klicka på `Save`
5.  Gå tillbaka till Xcode. I `ViewController.swift`, ersätter du raden som börjar med ”`let kClientID`' med program-ID som du just registrerade:

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
