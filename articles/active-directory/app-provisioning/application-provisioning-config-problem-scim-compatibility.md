---
title: Kända problem med SCIM 2.0-protokollefterlevnad – Azure AD
description: Så här löser du vanliga problem med protokollkompatibilitet när du lägger till ett program som inte är galleri som stöder SCIM 2.0 i Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522942"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Kända problem och lösningar med SCIM 2.0-protokollefterlevnad för Azure AD-tjänsten för användaretablering

Azure Active Directory (Azure AD) kan automatiskt etablera användare och grupper till alla program eller system som frontas av en webbtjänst med gränssnittet som definieras i [SCIM-specifikationen (System for Cross-Domain Identity Management).](https://tools.ietf.org/html/draft-ietf-scim-api-19) 

Azure AD:s stöd för SCIM 2.0-protokollet beskrivs i [Använda SYSTEM för SCIM (Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md), som listar de specifika delar av protokollet som implementeras för att automatiskt etablera användare och grupper från Azure AD till program som stöder SCIM 2.0.

I den här artikeln beskrivs aktuella och tidigare problem med Azure AD-användarens etableringstjänsts efterlevnad av SCIM 2.0-protokollet och hur du kan lösa dessa problem.

> [!IMPORTANT]
> Den senaste uppdateringen av AZURE AD-användarens etableringstjänst SCIM-klient gjordes den 18 december 2018. Den här uppdateringen åtgärdade de kända kompatibilitetsproblem som anges i tabellen nedan. Mer information om den här uppdateringen finns i de vanligaste frågorna nedan.

## <a name="scim-20-compliance-issues-and-status"></a>FRÅGOR och status för överensstämmelse och status för SCIM 2.0

| **Överensstämmelseproblem för SCIM 2.0** |  **Fast?** | **Åtgärda datum**  |  
|---|---|---|
| Azure AD kräver att "/scim" är i roten till programmets SCIM-slutpunkts-URL  | Ja  |  den 18 december 2018 | 
| Tilläggsattribut använder punkt "." notation före attributnamn i stället för kolon ":" notation |  Ja  | den 18 december 2018  | 
|  Korrigeringsbegäranden för attribut med flera värden innehåller ogiltig sökvägsfiltersyntax | Ja  |  den 18 december 2018  | 
|  Begäranden om gruppskapande innehåller en ogiltig schema-URI | Ja  |  den 18 december 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Har de tjänster som beskrivs automatiskt tillämpas på min befintliga SCIM-app?

Nej. Eftersom det skulle ha utgjort en brytande förändring av SCIM-appar som kodades för att fungera med det äldre beteendet, tillämpades inte ändringarna automatiskt på befintliga appar.

Ändringarna tillämpas på alla nya [SCIM-appar som inte är gallerier](../manage-apps/configure-single-sign-on-non-gallery-applications.md) som konfigurerats i Azure-portalen efter korrigeringsdatumet.

Information om hur du migrerar ett befintligt jobb för användaretablering till de senaste korrigeringarna finns i nästa avsnitt.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kan jag migrera ett befintligt SCIM-baserat användaretableringsjobb för att inkludera de senaste tjänstkorrigeringarna?

Ja. Om du redan använder den här programinstansen för enkel inloggning och behöver migrera det befintliga etableringsjobbet för att inkludera de senaste korrigeringarna följer du proceduren nedan. I den här proceduren beskrivs hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att ta bort ditt gamla etableringsjobb från din befintliga SCIM-app och skapa en ny som uppvisar det nya beteendet.

> [!NOTE]
> Om ditt program fortfarande är under utveckling och ännu inte har distribuerats för antingen enkel inloggning eller användaretablering, är den enklaste lösningen att ta bort programposten i Azure **Active Directory > Enterprise Applications** i Azure-portalen och helt enkelt lägga till en ny post för programmet med alternativet Skapa program > **Icke-galleri.** Detta är ett alternativ till att köra proceduren nedan.
 
1. Logga in på https://portal.azure.comAzure-portalen på .
2. Leta upp och välja ditt befintliga SCIM-program i avsnittet **Azure Active Directory > Enterprise Applications** i Azure Active Directory i Azure Active Directory i Azure-programmet i Azure Active Directory i Azure-portalen.
3. Kopiera **objekt-ID:et i**avsnittet **Egenskaper** i din befintliga SCIM-app .
4. I ett nytt webbläsarfönster https://developer.microsoft.com/graph/graph-explorer går du till och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att hitta ID:et för etableringsjobbet. Ersätt "[object-id]" med tjänstens huvud-ID (objekt-ID) som kopierats från det tredje steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Få jobb](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Få jobb") 


6. I resultaten kopierar du den fullständiga "ID"-strängen som börjar med antingen "customappsso" eller "scim".
7. Kör kommandot nedan för att hämta konfigurationen för attributmappning, så att du kan göra en säkerhetskopia. Använd samma [object-id] som tidigare och ersätt [job-id] med etableringsjobb-ID som kopierats från det sista steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Hämta schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Hämta schema") 

8. Kopiera JSON-utdata från det sista steget och spara det i en textfil. Detta innehåller alla anpassade attributmappningar som du har lagt till i din gamla app och bör vara ungefär några tusen rader JSON.
9. Kör kommandot nedan om du vill ta bort etableringsjobbet:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Kör kommandot nedan för att skapa ett nytt etableringsjobb som har de senaste tjänstkorrigeringarna.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. I resultatet av det sista steget kopierar du den fullständiga "ID"-strängen som börjar med "scim". Du kan också tillämpa dina gamla attributmappningar genom att köra kommandot nedan, ersätta [nytt jobb-ID] med det nya jobb-ID som du just kopierade och ange JSON-utdata från steg #7 som begärandetext.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Gå tillbaka till det första webbläsarfönstret och välj fliken **Etablering** för ditt program.
13. Verifiera konfigurationen och starta etableringsjobbet. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kan jag lägga till en ny app som inte är gallerid som har det gamla användarens etableringsbeteende?

Ja. Om du hade kodat ett program till det gamla beteendet som fanns före korrigeringarna och behöver distribuera en ny instans av det följer du proceduren nedan. I den här proceduren beskrivs hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att skapa ett SCIM-etableringsjobb som uppvisar det gamla beteendet.
 
1. Logga in på https://portal.azure.comAzure-portalen på .
2. i **Azure Active Directory > Enterprise Applications > Skapa programavsnittet** i **Azure-portalen** skapar du ett nytt program som inte är galleri.
3. Kopiera **objekt-ID:t i**avsnittet **Egenskaper** i den nya anpassade appen .
4. I ett nytt webbläsarfönster https://developer.microsoft.com/graph/graph-explorer går du till och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att initiera etableringskonfigurationen för din app.
   Ersätt "[object-id]" med tjänstens huvud-ID (objekt-ID) som kopierats från det tredje steget.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Gå tillbaka till det första webbläsarfönstret och välj fliken **Etablering** för ditt program.
7. Slutför konfigurationen för användaretablering som vanligt.


## <a name="next-steps"></a>Nästa steg
[Läs mer om etablering och avetablering till SaaS-program](user-provisioning.md)

