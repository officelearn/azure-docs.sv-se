---
title: Användarsekretess och Azure Active Directory-direktautentisering | Microsoft Docs
description: Den här artikeln behandlar Azure Active Directory (Azure AD)-direktautentisering och GDPR-efterlevnad.
services: active-directory
keywords: Azure AD Connect direktautentisering, GDPR, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: bcb448296313eedebb8f8389b320c5da5f1a80dd
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311789"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Användarsekretess och Azure Active Directory-direktautentisering


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Översikt

Azure AD-direktautentisering skapar följande loggtyp som kan innehålla personuppgifter:

- Azure AD Connect-spårningsloggfilerna.
- Autentisering-agenten spårningsloggfilerna.
- Windows Event log-filer.

Förbättra användarnas integritet för direktautentisering på två sätt:

1.  Extrahera data för en person och ta bort data från den personen från installationerna på begäran.
2.  Se till att inga data inte returneras efter 48 timmar.

Vi rekommenderar starkt det andra alternativet eftersom det är enklare att implementera och underhålla. Nedan följer anvisningarna för varje loggtyp:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Ta bort loggfiler för Azure AD Connect-spårning

Kontrollera innehållet i **%ProgramData%\AADConnect** mapp och ta bort spårningen logga innehållet (**trace -\*.log** filer) på den här mappen inom 48 timmar efter installation eller uppgradering av Azure AD Connect eller ändra konfigurationen av direktautentisering, som den här åtgärden kan skapa data som omfattas av GDPR.

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

### <a name="delete-authentication-agent-event-logs"></a>Ta bort autentiseringsagent händelseloggar

Den här produkten kan också skapa **Windows-händelseloggar**. Om du vill veta mer kan du läsa [i den här artikeln](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Om du vill visa relaterade till Autentiseringsagenten för direktautentisering, öppna den **Loggboken** på servern och kontrollera under **program och tjänsten Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** .

### <a name="delete-authentication-agent-trace-log-files"></a>Ta bort autentiseringsagent spårningsloggfilerna

Du bör regelbundet kontrollera innehållet i **%ProgramData%\Microsoft\Azure AD ansluta autentisering Agent\Trace\**  och ta bort innehållet i den här mappen varje 48 timmar. 

>[!IMPORTANT]
>Om autentiseringsagent-tjänsten körs, kommer det inte att ta bort den aktuella loggfilen i mappen. Stoppa tjänsten innan du försöker igen. Om du vill undvika användaren inloggningar, du bör redan ha konfigurerat direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Du kan granska och ta bort dessa filer med hjälp av Windows Explorer eller du kan använda följande skript för att utföra åtgärderna som krävs:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Om du vill schemalägga det här skriptet ska köras så varje 48 timmar här:

1.  Spara skriptet i en fil med det ”. Ps1 ”tillägget.
2.  Öppna **Kontrollpanelen** och klicka på **System och säkerhet**.
3.  Under den **Administrationsverktyg** klickar du på ”**schema aktiviteter**”.
4.  I **Schemaläggaren**, högerklickar du på ”**schema Uppgiftsbibliotek**” och klicka på ”**skapa grundläggande uppgift...** ”.
5.  Ange namnet på den nya aktiviteten och klicka på **nästa**.
6.  Välj ”**dagliga**” för den **aktivitetsutlösare** och klicka på **nästa**.
7.  Ställa in återkommande till två dagar och klickar på **nästa**.
8.  Välj ”**programstart**” som åtgärd och klicka på **nästa**.
9.  Typen ”**PowerShell**” i rutan för Program eller skript och i rutan med texten ”**lägga till argument (valfritt)**”, ange den fullständiga sökvägen till det skript som du skapade tidigare och sedan på **nästa**.
10. Nästa skärm visar en sammanfattning av uppgiften som du håller på att skapa. Kontrollera värdena och klicka på **Slutför** att skapa aktiviteten:
 
### <a name="note-about-domain-controller-logs"></a>Observera om Domain controller loggar

Om granskningsloggning aktiveras kan den här produkten generera säkerhetsloggar för domänkontrollanterna. Mer information om hur du konfigurerar granskningsprinciper kan du läsa följande [artikeln](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Nästa steg
* [Granska Microsoft Privacy principen på Säkerhetscenter](https://www.microsoft.com/trustcenter)
- [**Felsöka** ](tshoot-connect-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
