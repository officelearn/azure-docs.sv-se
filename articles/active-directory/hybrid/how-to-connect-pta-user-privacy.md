---
title: Användarsekretess och Azure Active Directory direktautentisering | Microsoft-dokument
description: Den här artikeln behandlar Azure Active Directory (Azure AD) direktautentisering och GDPR-efterlevnad.
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, GDPR, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931014"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Användarsekretess och Azure Active Directory-direktautentisering


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt

Azure AD-direktautentisering skapar följande loggtyp, som kan innehålla personuppgifter:

- Azure AD Connect-spårningsloggfiler.
- Autentiseringsagenten spåra loggfiler.
- Loggfiler för Windows-händelse.

Förbättra användarnas integritet för direktautentisering på två sätt:

1.  På begäran extrahera data för en person och ta bort data från den personen från installationerna.
2.  Se till att inga data lagras längre än 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är lättare att implementera och underhålla. Följande är instruktionerna för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort Azure AD Connect-spårningsloggfiler

Kontrollera innehållet i mappen **%ProgramData%\AADConnect** och ta bort spårningslogginnehållet **(trace-\*.log** files) i den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändring av direktautentiseringskonfiguration, eftersom den här åtgärden kan skapa data som omfattas av GDPR.

>[!IMPORTANT]
>Ta inte bort filen **PersistedState.xml** i den här mappen, eftersom den här filen används för att upprätthålla tillståndet för den tidigare installationen av Azure AD Connect och används när en uppgraderingsinstallation är klar. Den här filen innehåller aldrig några data om en person och bör aldrig tas bort.

Du kan antingen granska och ta bort dessa spårningsloggfiler med Utforskaren eller använda följande PowerShell-skript för att utföra nödvändiga åtgärder:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Spara skriptet i en fil med ". PS1" förlängning. Kör skriptet efter behov.

Mer information om relaterade GDPR-krav för Azure AD Connect finns i [den här artikeln](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Ta bort autentiseringsagentens händelseloggar

Den här produkten kan också skapa **Windows-händelseloggar**. Mer information finns i [den här artikeln](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Om du vill visa loggar som är relaterade till direktautentiseringsagenten öppnar du **Loggboken-programmet** på servern och kontrollerar under **Program- och tjänstloggar\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Ta bort spårningsloggfiler för autentiseringsagent

Du bör regelbundet kontrollera innehållet i **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Spåra** och ta bort innehållet i den här mappen var 48:e timme. 

>[!IMPORTANT]
>Om tjänsten Authentication Agent körs kan du inte ta bort den aktuella loggfilen i mappen. Stoppa tjänsten innan du försöker igen. Om du vill undvika inloggningsfel för användare bör du redan ha konfigurerat direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Du kan antingen granska och ta bort dessa filer med Utforskaren eller använda följande skript för att utföra nödvändiga åtgärder:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Så här schemalägger du skriptet så här:

1.  Spara skriptet i en fil med ". PS1" förlängning.
2.  Öppna **Kontrollpanelen** och klicka på **System och Säkerhet**.
3.  Klicka på "**Schemalägg aktiviteter " under**rubriken **Administrationsverktyg** ".
4.  Högerklicka på "**Schemabibliotek**" i Schemaläggaren " i **Schemaläggaren**och klicka på "**Skapa grundläggande uppgift...**".
5.  Ange namnet på den nya uppgiften och klicka på **Nästa**.
6.  Välj "**Dagligen**" för **aktivitetsutlösaren** och klicka på **Nästa**.
7.  Ange upprepningen till två dagar och klicka på **Nästa**.
8.  Välj "**Starta ett program**" som åtgärd och klicka på **Nästa**.
9.  Skriv "**PowerShell**" i rutan för programmet/skriptet och i rutan "**Lägg till argument (valfritt)**"anger du den fullständiga sökvägen till skriptet som du skapade tidigare och klickar sedan på **Nästa**.
10. På nästa skärm visas en sammanfattning av den uppgift som du ska skapa. Verifiera värdena och klicka på **Slutför** för att skapa uppgiften:
 
### <a name="note-about-domain-controller-logs"></a>Anmärkning om domänkontrollantloggar

Om granskningsloggning är aktiverat kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Mer information om hur du konfigurerar granskningsprinciper finns i den här [artikeln](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg
* [Läs Microsofts sekretesspolicy på Säkerhetscenter](https://www.microsoft.com/trustcenter)
* [**Felsöka**](tshoot-connect-pass-through-authentication.md) - Lär dig hur du löser vanliga problem med funktionen.
