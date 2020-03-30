---
title: MSAL för iOS & macOS-skillnader | Azure
titleSuffix: Microsoft identity platform
description: I artikeln beskrivs användningsskillnader i Microsoft Authentication Library (MSAL) mellan iOS och macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084979"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Skillnader mellan Microsoft Authentication Library för iOS och macOS

I den här artikeln beskrivs skillnaderna i funktionalitet mellan Microsoft Authentication Library (MSAL) för iOS och macOS.

> [!NOTE]
> På Mac stöder MSAL endast macOS-appar.

## <a name="general-differences"></a>Allmänna skillnader

MSAL för macOS är en delmängd av de funktioner som är tillgängliga för iOS.

MSAL för macOS stöder inte:

- olika webbläsartyper, `ASWebAuthenticationSession` `SFAuthenticationSession`till `SFSafariViewController`exempel , , .
- autentisering via Microsoft Authenticator-appen stöds inte för macOS.

Nyckelringsdelning mellan appar från samma utgivare är mer begränsad på macOS 10.14 och tidigare. Använd [åtkomstkontrollistor](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) för att ange sökvägarna till de appar som ska dela nyckelringen. Användaren kan se ytterligare nyckelringsuppmaningar.

På macOS 10.15+är MSAL:s beteende detsamma mellan iOS och macOS. MSAL använder [nyckelringsåtkomstgrupper](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) för nyckelringsdelning. 

### <a name="conditional-access-authentication-differences"></a>Skillnader i autentisering av villkorad åtkomst

För scenarier för villkorlig åtkomst blir färre användaruppmaningar när du använder MSAL för iOS. Detta beror på att iOS använder mäklarappen (Microsoft Authenticator) som förnekar behovet av att fråga användaren i vissa fall.

### <a name="project-setup-differences"></a>Skillnader i projektinställningar

**Macos**

- När du konfigurerar projektet på macOS kontrollerar du att programmet är signerat med ett giltigt utvecklings- eller produktionscertifikat. MSAL fungerar fortfarande i osignerat läge, men det kommer att bete sig annorlunda när det gäller cache persistens. Appen ska endast köras osignerad för felsökning. Om du distribuerar appen osignerad:
1. På 10,14 och tidigare kommer MSAL att uppmana användaren om ett nyckelringslösenord varje gång de startar om appen.
2. På 10,15 +, KOMMER MSAL fråga användaren för autentiseringsuppgifter för varje token förvärv. 

- macOS-appar behöver inte implementera AppDelegate-anropet.

**iOS**

- Det finns ytterligare steg för att konfigurera projektet för att stödja autentiseringsutjämningsflödet. Stegen kallas ut i handledningen.
- iOS-projekt måste registrera anpassade system i info.plist. Detta krävs inte på macOS.
