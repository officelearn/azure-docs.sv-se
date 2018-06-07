---
title: Användarnas integritet och Azure Active Directory direkt-autentisering | Microsoft Docs
description: Den här artikeln handlar om Azure Active Directory (AD Azure) direkt autentisering och BNPR kompatibilitet.
services: active-directory
keywords: Azure AD Connect direkt-autentisering, BNPR, nödvändiga komponenter för Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 3343cebb85124f19fe773822e296312abad53d96
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591182"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Användarnas integritet och Azure Active Directory direkt-autentisering


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt

Azure AD direkt autentisering skapar följande loggtyp som kan innehålla personuppgifter:

- Azure AD Connect-spårningsloggfilerna.
- Autentisering Agent spårningsloggfilerna.
- Loggfiler för Windows-händelse.

Förbättra användarnas integritet för direkt autentisering på två sätt:

1.  Hämta data för en person och ta bort data från den personen från installationer på begäran.
2.  Se till att inga data sparas 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Här följer instruktioner för varje typ:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort spårningsloggfilerna i Azure AD Connect

Kontrollera innehållet i **%ProgramData%\AADConnect** mapp och ta bort spårningen logga innehållet (**trace -\*.log** filer) i den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändra direkt autentisering konfiguration som den här åtgärden kan skapa data som omfattas av BNPR.

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

### <a name="delete-authentication-agent-event-logs"></a>Ta bort autentiseringsagent händelseloggar

Den här produkten kan också skapa **Windows-händelseloggar**. Mer information finns i [i den här artikeln](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Om du vill visa loggar som rör autentiseringsagent direkt, öppna den **Loggboken** på servern och söker under **program och tjänsten Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Ta bort autentiseringsagent spårningsloggfilerna

Du bör regelbundet kontrollera innehållet i **%ProgramData%\Microsoft\Azure AD ansluta autentisering Agent\Trace\**  och ta bort innehållet i den här mappen varje 48 timmar. 

>[!IMPORTANT]
>Om Authentication Agent-tjänsten körs, kommer det inte att ta bort den aktuella loggfilen i mappen. Stoppa tjänsten innan du försöker igen. För att undvika användaren logga in fel du bör redan har konfigurerat direkt autentisering för [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

Du kan granska och ta bort dessa filer med hjälp av Utforskaren eller du kan använda följande skript för att utföra nödvändiga åtgärder:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Om du vill schemalägga skriptet ska köras så varje 48 timmar här:

1.  Spara skriptet i en fil med det ”. Ps1 ”tillägg.
2.  Öppna **Kontrollpanelen** och klicka på **System och säkerhet**.
3.  Under den **Administrationsverktyg** rubrik, klicka på ”**schemalägga aktiviteter**”.
4.  I **Schemaläggaren**, högerklickar du på ”**schema Uppgiftsbibliotek**” och klicka på ”**Skapa standardaktivitet...** ”.
5.  Ange namnet på den nya aktiviteten och klicka på **nästa**.
6.  Välj ”**dagliga**” för den **Aktivitetslösare** och på **nästa**.
7.  Ställa in återkommande två dagar och klicka på **nästa**.
8.  Välj ”**starta ett program**” som åtgärd och klicka på **nästa**.
9.  Typen ”**PowerShell**” i rutan för Program eller skript och i rutan med texten ”**lägga till argument (valfritt)**”, ange den fullständiga sökvägen till det skript som du skapade tidigare och klicka på **nästa**.
10. På nästa skärm visas en sammanfattning av uppgiften som du ska skapa. Kontrollera värdena och klicka på **Slutför** att skapa aktiviteten:
 
### <a name="note-about-domain-controller-logs"></a>Observera om Domain controller loggar

Om granskningsloggning är aktiverad kan kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Om du vill veta mer om hur du konfigurerar granskningsprinciper kan läsa det här [artikel](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy-policy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
