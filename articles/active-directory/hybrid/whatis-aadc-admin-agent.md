---
title: Vad är Azure AD Connect admin-agenten – Azure AD Connect | Microsoft Docs
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305202"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Vad är Azure Active Directory Connect-administratörsagenten? 
Azure AD Connect administrations agent är en ny komponent i Azure Active Directory Connect som kan installeras på en Azure Active Directory Connect-Server. Den används för att samla in specifika data från din Active Directory-miljö som hjälper en support tekniker från Microsoft att felsöka problem när du öppnar ett support ärende. 

>[!NOTE]
>Administratörs agenten är inte installerad och aktive rad som standard.  Du måste installera agenten för att kunna samla in data för att hjälpa till med support ärenden.

När Azure AD Connect administrations agenten är installerad, väntar på vissa begär Anden för data från Azure Active Directory, hämtar begärda data från Sync-miljön och skickar dem till Azure Active Directory, där de presenteras för Microsoft Support tekniker. 

Den information som Azure AD Connect administrations agenten hämtar från din miljö lagras inte på något sätt – den visas bara för Microsoft Support-teknikern för att hjälpa dem att undersöka och felsöka Azure Active Directory Connect relaterat support ärende som du öppnade Azure AD Connect administrations agenten är inte installerad på Azure AD Connect servern som standard. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installera Azure AD Connect administrations agent på Azure AD Connects Server 

Krav:
1.  Azure AD Connect är installerat på servern
2.  Azure AD Connect Health är installerat på servern

![administratörs agent](media/whatis-aadc-admin-agent/adminagent0.png)

Binärfilerna för Azure AD Connect administrations agenten placeras i AAD Connect-servern. Installera agenten genom att göra följande:

1.  Öppna PowerShell i administratörs läge
2.  Navigera till den katalog där programmet finns CD "C:\Program\Microsoft Azure Active Directory Connect\Tools"
3.  Kör ConfigureAdminAgent. ps1

När du uppmanas anger du dina autentiseringsuppgifter som global administratör för Azure AD. Detta bör vara samma autentiseringsuppgifter som angavs under Azure AD Connect installationen.

När agenten har installerats visas följande två nya program i listan Lägg till/ta bort program på kontroll panelen på servern: 

![administratörs agent](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Vilka data i min Sync-tjänst visas för Microsoft Service Engineer? 
När du öppnar ett support ärende kan Microsoft Support-teknikern se, för en specifik användare, relevanta data i Active Directory, Active Directory kopplings utrymmet i Azure Active Directory Connect-servern, Azure Active Directory kopplings utrymme i Azure Active Directory Anslut Server och metaversum i Azure Active Directory Connect-servern. 

Microsoft Support-teknikern kan inte ändra data i systemet och kan inte se några lösen ord. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Vad händer om jag inte vill att Microsoft Support-teknikern ska komma åt mina data? 
Om du inte vill att Microsoft-teknikern ska komma åt dina data för ett support samtal när agenten har installerats kan du inaktivera funktionen genom att ändra tjänstens konfigurations fil enligt beskrivningen nedan: 

1.  Öppna **C:\Program Files\Microsoft Azure AD Connect administration Agent\AzureADConnectAdministrationAgentService.exe.config** i anteckningar.
2.  Inaktivera **UserDataEnabled** -inställningen enligt vad som visas nedan. Om **UserDataEnabled** -inställningen finns och har värdet true anger du den till false. Om inställningen inte finns lägger du till inställningen som visas nedan.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Spara konfigurations filen.
4.  Starta om Azure AD Connect administrations agent tjänst enligt nedan

![administratörs agent](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
