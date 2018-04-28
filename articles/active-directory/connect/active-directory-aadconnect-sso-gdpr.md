---
title: Användarnas integritet och Azure AD sömlös enkel inloggning | Microsoft Docs
description: Den här artikeln handlar om Azure Active Directory (AD Azure) sömlös SSO och BNPR kompatibilitet.
services: active-directory
keywords: Vad är Azure AD Connect, BNPR, nödvändiga komponenter för Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bffa5a3aa57c5b01e3361bc6fc6b284348707800
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Användarsekretess och Azure AD sömlös enkel inloggning

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt


Azure AD sömlös SSO skapar följande loggtyp som kan innehålla EUII:

- Azure AD Connect-spårningsloggfilerna.

Sekretess-efterlevnad för sömlös SSO kan nås på två sätt:

1.  Hämta data för en person och ta bort data från den personen från installationer på begäran.
2.  Se till att inga data sparas 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Se följande instruktioner för varje typ:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort spårningsloggfilerna i Azure AD Connect

Kontrollera innehållet i **%ProgramData%\AADConnect** mapp och ta bort spårningen logga innehållet (**trace -\*.log** filer) i den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändra sömlös SSO-konfiguration som den här åtgärden kan skapa data som omfattas av BNPR.

>[!IMPORTANT]
>Ta inte bort den **PersistedState.xml** filen i den här mappen som den här filen används för att upprätthålla tillståndet för den tidigare installationen av Azure AD Connect och används när en uppgraderingen är klar. Den här filen innehåller data om en person aldrig och aldrig ska tas bort.

Du kan granska och ta bort dessa spårningsloggfilerna Utforskaren eller du kan använda följande PowerShell-skript för att utföra nödvändiga åtgärder:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med det ”. Ps1 ”tillägg. Köra detta skript efter behov.

Mer information om relaterade Azure AD Connect BNPR krav, se [i den här artikeln](active-directory-aadconnect-gdpr.md).

### <a name="note-about-domain-controller-logs"></a>Observera om Domain controller loggar

Om granskningsloggning är aktiverad kan kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Om du vill veta mer om hur du konfigurerar granskningsprinciper kan läsa det här [artikel](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
