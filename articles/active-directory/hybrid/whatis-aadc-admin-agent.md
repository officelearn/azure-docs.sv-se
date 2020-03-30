---
title: Vad är Azure AD Connect Admin Agent - Azure AD Connect | Microsoft-dokument
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049383"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Vad är Azure Active Directory Connect-administratörsagenten? 
Azure AD Connect Administration Agent är en ny komponent i Azure Active Directory Connect som kan installeras på en Azure Active Directory Connect-server. Den används för att samla in specifika data från Active Directory-miljön som hjälper en Microsoft-supporttekniker att felsöka problem när du öppnar ett supportärende. 

>[!NOTE]
>Administratörsagenten är inte installerad och aktiverad som standard.  Du måste installera agenten för att samla in data för att hjälpa till med supportärenden.

När azure AD Connect Administration Agent installeras väntar på specifika begäranden om data från Azure Active Directory, hämtar begärda data från synkroniseringsmiljön och skickar dem till Azure Active Directory, där den presenteras för Microsoft-supporten Ingenjör. 

Informationen som Azure AD Connect Administration Agent hämtar från din miljö lagras inte på något sätt - den visas bara för Microsofts supporttekniker för att hjälpa dem att undersöka och felsöka Azure Active Directory Connect relaterat supportärende som du öppnade Azure AD Connect Administration Agent är inte installerat på Azure AD Connect Server som standard. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installera Azure AD Connect Administration Agent på Azure AD Connect-servern 

Krav:
1.    Azure AD Connect är installerat på servern
2.    Azure AD Connect Health är installerat på servern

![admin agent](media/whatis-aadc-admin-agent/adminagent0.png)

Binärfilerna för Azure AD Connect Administration Agent placeras på AAD Connect-servern. Så här installerar du agenten:

1.    Öppna powershell i admin-läge
2.    Navigera till katalogen där programmet finns cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    Kör ConfigureAdminAgent.ps1

Ange dina globala administratörsuppgifter för Azure AD när du uppmanas att ange dina globala administratörsuppgifter för Azure AD. Detta bör vara samma autentiseringsuppgifter som anges under Azure AD Connect-installationen.

När agenten har installerats visas följande två nya program i listan "Lägg till/ta bort program" på Kontrollpanelen på servern: 

![admin agent](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Vilka data i min synkroniseringstjänst visas för Microsofts tjänsttekniker? 
När du öppnar ett supportärende kan Microsoft Support Engineer för en viss användare se relevanta data i Active Directory, Active Directory-anslutningsutrymmet i Azure Active Directory Connect-servern, Azure Active Directory-anslutningsutrymmet i Azure Active Directory Connect-servern och metaversumet i Azure Active Directory Connect-servern. 

Microsoft Support Engineer kan inte ändra några data i systemet och kan inte se några lösenord. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Vad gör jag om jag inte vill att Microsofts supporttekniker ska komma åt mina data? 
När agenten har installerats, Om du inte vill att Microsofts tjänsttekniker ska komma åt dina data för ett supportsamtal, kan du inaktivera funktionen genom att ändra tjänstens config-fil enligt beskrivningen nedan: 

1.    Öppna **C:\Program\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** i anteckningar.
2.    Inaktivera **UserDataEnabled-inställningen** enligt nedan. Om **UserDataEnabled-inställningen** finns och är inställd på true ställer du in den på false. Om inställningen inte finns lägger du till inställningen som visas nedan.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Spara konfigurationsfilen.
4.    Starta om Azure AD Connect Administration Agent-tjänsten enligt nedan

![admin agent](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
