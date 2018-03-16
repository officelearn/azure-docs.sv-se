---
title: "Azure AD Connect: Sömlös Single Sign-On - BNPR efterlevnad | Microsoft Docs"
description: "Den här artikeln handlar om Azure Active Directory (AD Azure) sömlös SSO och BNPR kompatibilitet."
services: active-directory
keywords: "Vad är Azure AD Connect, BNPR, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Azure AD sömlös enkel inloggning: BNPR kompatibilitet

## <a name="overview"></a>Översikt

I kan 2018, Europeiska sekretesslagstiftning den [allmänna Data Protection förordning (BNPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm), upphör att gälla. BNPR inför nya regler på företag, myndigheter, icke-vinst och andra organisationer att erbjudande varor och tjänster till personer i Europeiska unionen (EU), eller att samla in och analysera data som är knutna till Europa boende. BNPR gäller oavsett där du befinner dig. 

Microsoftprodukter och tjänster finns i dag som hjälper dig att uppfylla kraven BNPR. Läs mer om Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

Azure AD sömlös SSO skapar följande loggtyp som kan innehålla EUII:

- Azure AD Connect-spårningsloggfilerna.

BNPR kompatibilitet för sömlös SSO kan nås på två sätt:

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

- [**Felsöka** ](active-directory-aadconnect-troubleshoot-sso.md) – Lär dig att lösa vanliga problem med funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
