---
title: Vad är Azure AD Connect-administratören agenten – Azure AD Connect | Microsoft Docs
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33143626e136523b4af086e841b92e9ad30fa86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60295030"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Vad är Azure Active Directory Connect-administratörsagenten? 
Azure AD Connect Administration-agenten är en ny komponent i Azure Active Directory Connect som är installerad på en Azure Active Directory Connect-server. Den används för att samla in specifika data från Active Directory-miljön som hjälper till att ett Microsoft-support-tekniker för att felsöka problem när du öppnar ett supportärende.

Gången du installerar support den Azure AD Connect Administration agenten väntar för specifika begäranden för data från Azure Active Directory, får den begärda informationen från sync-miljö och skickar det till Azure Active Directory, där den visas på Microsoft tekniker.

Den information som Azure AD Connect Administration agenten hämtar från din miljö lagras inte på något sätt – det är bara visas för Microsoft-supporttekniker att hjälpa dem att undersöka och felsöka Azure Active Directory Connect relaterade supportärende som du öppnade

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Hur är Azure AD Connect Admin-agenten installerad på Azure AD Connect-servern? 
När administratören-agenten har installerats, visas följande två nya program i listan ”Lägg till/ta bort program” i Kontrollpanelen på din server: 

![Admin-agenten](media/whatis-aadc-admin-agent/adminagent1.png)

Ta bort eller inte avinstallation av program som de är en viktig del av din Azure AD Connect-installationen.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Vilka data i mitt synkroniseringstjänsten visas till servicetekniker Microsoft?
När du öppnar ett supportärende, Microsoft Support-tekniker kan se för en viss användare, relevanta data i Active Directory, Active Directory-anslutarplatsen i Azure Active Directory Connect-server, Azure Active Directory-anslutarplatsen i Azure Active Directory Connect-servern och metaversum i Azure Active Directory Connect-server.

Microsoft Support-tekniker kan inte ändra data i systemet och kan inte se eventuella lösenord.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Vad händer om jag inte vill att Microsoft-supporttekniker att komma åt mina data? 
 
Om du inte vill att Microsoft servicetekniker komma åt dina data för supportsamtal kan du inaktivera detta genom att ändra konfigurationsfilen service enligt beskrivningen nedan: 

1.  Öppna **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** i anteckningar.
2.  Inaktivera **UserDataEnabled** inställningen som visas nedan. Om **UserDataEnabled** inställningen finns och är inställd på true och inställd på false. Om inställningen inte finns, lägger du till inställningen som visas nedan.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Spara konfigurationsfilen.
4.  Starta om tjänsten för Azure AD Connect Administration agenten enligt nedan

![Admin-agenten](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).