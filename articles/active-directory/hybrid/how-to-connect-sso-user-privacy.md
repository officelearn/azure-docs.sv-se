---
title: Användarsekretess och Azure AD sömlös enkel inloggning | Microsoft Docs
description: Den här artikeln behandlar Azure Active Directory (Azure AD) sömlös SSO och GDPR-efterlevnad.
services: active-directory
keywords: Vad är Azure AD Connect, GDPR, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 96feb5886ed9940668b9566b2eec4fd1dd8f9d3a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175306"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Användarsekretess och Azure AD sömlös enkel inloggning

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt


Sömlös enkel inloggning för den Azure AD skapar följande loggtyp som kan innehålla personuppgifter: 

- Azure AD Connect-spårningsloggfilerna.

Förbättra användarnas integritet för sömlös enkel inloggning på två sätt:

1.  Extrahera data för en person och ta bort data från den personen från installationerna på begäran.
2.  Se till att inga data inte returneras efter 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Se följande instruktioner för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort loggfiler för Azure AD Connect-spårning

Kontrollera innehållet i **%ProgramData%\AADConnect** mapp och ta bort spårningen logga innehållet (**trace -\*.log** filer) på den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändra sömlös SSO-konfiguration som den här åtgärden kan skapa data som omfattas av GDPR.

>[!IMPORTANT]
>Ta inte bort den **PersistedState.xml** fil i den här mappen eftersom den här filen används för att underhålla tillståndet för den tidigare installationen av Azure AD Connect och används när en uppgraderingen är klar. Den här filen innehåller aldrig några data om en person och bör aldrig tas bort.

Du kan granska och ta bort dessa loggfiler över spårning med hjälp av Windows Explorer eller du kan använda följande PowerShell-skript för att utföra åtgärderna som krävs:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med det ”. Ps1 ”tillägget. Kör skriptet efter behov.

Mer information om relaterade Azure AD Connect GDPR krav, se [i den här artikeln](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Observera om Domain controller loggar

Om granskningsloggning aktiveras kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Mer information om hur du konfigurerar granskningsprinciper kan du läsa följande [artikeln](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy principen på Säkerhetscenter](https://www.microsoft.com/trustcenter)
  - [**Felsöka** ](tshoot-connect-sso.md) – Lär dig att lösa vanliga problem med funktionen.
  - [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att skicka in nya funktionbegäran.
