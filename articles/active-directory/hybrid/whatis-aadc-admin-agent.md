---
title: Vad är Azure AD Connect-administratören agenten – Azure AD Connect | Microsoft Docs
description: Beskriver de verktyg som används för att synkronisera och övervaka din lokala miljö med Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687228"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Vad är Azure Active Directory Connect-administratörsagenten? 
Azure AD Connect Administration-agenten är en ny komponent i Azure Active Directory Connect som kan installeras på en Azure Active Directory Connect-server. Den används för att samla in specifika data från Active Directory-miljön som hjälper till att ett Microsoft-support-tekniker för att felsöka problem när du öppnar ett supportärende. 

>[!NOTE]
>Admin-agenten är inte installerat och aktiverat som standard.  Du måste installera agenten för att samla in data som hjälper till med supportärenden.

När installeras, support den Azure AD Connect Administration agenten väntar för specifika begäranden för data från Azure Active Directory, hämtar den begärda informationen från miljön för synkronisering och skickar det till Azure Active Directory, där den visas på Microsoft tekniker. 

Den information som Azure AD Connect Administration agenten hämtar från din miljö lagras inte på något sätt – det är bara visas för Microsoft-supporttekniker att hjälpa dem att undersöka och felsöka Azure Active Directory Connect relaterade support-ärende som du öppnade Azure AD Connect Administration-agenten installeras inte på den Azure AD Connect-servern som standard. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installera Azure AD Connect Administration-agenten på Azure AD Connect-servern 
Azure AD Connect Administration agenten binärfiler placeras i AAD Connect-servern. Om du vill installera agenten, gör du följande: 



1. Öppna powershell i administratörsläge 
2. Gå till den katalog där programmet är placerad cd ”C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles” 
3. Kör programmet AADConnectAdminAgentSetup.exe 
 
När du uppmanas ange dina autentiseringsuppgifter för Azure AD-global administratör. 

>[!NOTE]
>Det finns ett känt problem där du uppmanas att dina autentiseringsuppgifter flera gånger. Detta kommer att åtgärdas i nästa version.

När agenten har installerats, visas följande två nya program i listan ”Lägg till/ta bort program” i Kontrollpanelen på din server: 

![Admin-agenten](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Vilka data i mitt synkroniseringstjänsten visas till servicetekniker Microsoft? 
När du öppnar ett supportärende kan Microsoft Support-tekniker för en viss användare, relevanta data i Active Directory, Active Directory-anslutarplatsen i Azure Active Directory Connect-server Se Azure Active Directory-anslutarplatsen i Azure Active Directory Connect-servern och metaversum i Azure Active Directory Connect-server. 

Microsoft Support-tekniker kan inte ändra data i systemet och kan inte se eventuella lösenord. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Vad händer om jag inte vill att Microsoft-supporttekniker att komma åt mina data? 
När agenten har installerats, om du inte vill att Microsoft servicetekniker komma åt dina data för kundsupport, kan du inaktivera funktionen genom att ändra konfigurationsfilen för tjänsten enligt följande: 

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
