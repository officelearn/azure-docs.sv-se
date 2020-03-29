---
title: Användarsekretess och Azure AD Sömlös enkel inloggning | Microsoft-dokument
description: Den här artikeln behandlar Azure Active Directory (Azure AD) Sömlös SSO- och GDPR-efterlevnad.
services: active-directory
keywords: vad är Azure AD Connect, GDPR, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242111"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Användarsekretess och sömlös enkel inloggning för Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt


Azure AD Seamless SSO skapar följande loggtyp, som kan innehålla personuppgifter: 

- Azure AD Connect-spårningsloggfiler.

Förbättra användarnas integritet för Seamless SSO på två sätt:

1.  På begäran extrahera data för en person och ta bort data från den personen från installationerna.
2.  Se till att inga data lagras längre än 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är lättare att implementera och underhålla. Se följande instruktioner för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort Azure AD Connect-spårningsloggfiler

Kontrollera innehållet i mappen **%ProgramData%\AADConnect** och ta bort spårningslogginnehållet **(trace-\*.log** files) i den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändring av Sömlös SSO-konfiguration, eftersom den här åtgärden kan skapa data som omfattas av GDPR.

>[!IMPORTANT]
>Ta inte bort filen **PersistedState.xml** i den här mappen, eftersom den här filen används för att upprätthålla tillståndet för den tidigare installationen av Azure AD Connect och används när en uppgraderingsinstallation är klar. Den här filen innehåller aldrig några data om en person och bör aldrig tas bort.

Du kan antingen granska och ta bort dessa spårningsloggfiler med Utforskaren eller använda följande PowerShell-skript för att utföra nödvändiga åtgärder:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med ". PS1" förlängning. Kör skriptet efter behov.

Mer information om relaterade GDPR-krav för Azure AD Connect finns i [den här artikeln](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Anmärkning om domänkontrollantloggar

Om granskningsloggning är aktiverat kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Mer information om hur du konfigurerar granskningsprinciper finns i den här [artikeln](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg

* [Läs Microsofts sekretesspolicy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
  - [**Felsöka**](tshoot-connect-sso.md) - Lär dig hur du löser vanliga problem med funktionen.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - För att lämna in nya funktionsförfrågningar.