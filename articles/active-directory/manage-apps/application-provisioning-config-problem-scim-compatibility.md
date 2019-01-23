---
title: Kända problem och lösningar med SCIM 2.0-protokollet kompatibiliteten för Azure AD-användare Provisioning-tjänsten | Microsoft Docs
description: Hur du löser vanliga problem för protokollet kompatibilitet har när du lägger till en icke-galleriprogram som stöder SCIM 2.0 till Azure AD
services: active-directory
documentationcenter: ''
author: asmalser
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.openlocfilehash: d67132989910434cd6b5d178ee44425992525496
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475959"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Kända problem och lösningar med SCIM 2.0-protokollet kompatibiliteten för Azure AD-användare Provisioning-tjänsten

Azure Active Directory (Azure AD) automatiskt kan etablera användare och grupper för att alla program och system som är fronted av en webbtjänst med gränssnittet som definieras i den [System för domänerna Identity Management (SCIM) 2.0-protokollet specifikationen](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Stöd för Azure AD för SCIM 2.0-protokollet beskrivs i [med hjälp av System för domänerna Identity Management (SCIM) att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md), där den vissa delar av det protokoll som implementerar för att automatiskt etablera användare och grupper från Azure AD till program som stöder SCIM 2.0.

Den här artikeln beskrivs aktuella och tidigare problem med Azure AD-tjänstens infört SCIM 2.0-protokollet och hur du kan lösa dessa problem för användaretablering.

> [!IMPORTANT]
> Den senaste uppdateringen till Azure AD användaren etablering SCIM tjänstklienten gjordes 18 December 2018. Den här uppdateringen åtgärdas kända kompatibilitetsproblem som anges i tabellen nedan. Se de vanliga frågor och svar nedan för mer information om den här uppdateringen.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 kompatibilitetsproblem och status

| **SCIM 2.0 kompatibilitetsproblem** |  **Fast?** | **Åtgärda datum**  |  
|---|---|---|
| Azure AD kräver ”/ scim” finnas i roten för programmet är SCIM slutpunkts-URL  | Ja  |  18 december 2018 | 
| Tilläggsattribut Använd punkt ”.” notation innan attributnamn i stället för kolon ””: notation |  Ja  | 18 december 2018  | 
|  Patch-begäranden för flera värden attribut innehåller en ogiltig sökväg filtersyntaxen | Ja  |  18 december 2018  | 
|  Skapa begäranden innehåller ett ogiltigt schema URI | Ja  |  18 december 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Tjänster-korrigeringar beskrivs vidtas automatiskt med min befintliga SCIM-app?

Nej. Eftersom det skulle ha utgjorde en viktig ändring till SCIM-appar som har kodats för att fungera med den äldre beteenden, tillämpas inte ändringarna automatiskt till befintliga appar.

Ändringarna tillämpas på alla nya [appar som inte är ett galleriprogram SCIM](configure-single-sign-on-non-gallery-applications.md) konfigurerad i Azure-portalen, efter datumet då korrigeringen.

Information om hur du migrerar en befintlig användare Etableringsjobb för att inkludera de senaste korrigeringarna finns i nästa avsnitt.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kan jag migrera en befintlig SCIM-baserade användare Etableringsjobb för att inkludera de senaste korrigeringarna i tjänsten?

Ja. Följ anvisningarna nedan om du redan använder den här instansen av programmet för enkel inloggning och behöver jag migrera den befintliga etableringsjobbet för att inkludera de senaste korrigeringarna. Den här proceduren beskriver hur du använder Microsoft Graph API och Microsoft Graph API-explorer för att ta bort din gamla Etableringsjobbet från din befintliga SCIM-app och skapa en ny som uppvisar det nya beteendet.

> [!NOTE]
> Om ditt program är fortfarande under utveckling och har ännu inte distribuerats för enkel inloggning eller etableringen av användare, den enklaste lösningen är att ta bort posten program i den **Azure Active Directory > företagsprogram**på Azure portal och Lägg bara till en ny post för programmet med den **skapa program > icke-galleriet** alternativet. Det här är ett alternativ till att köra proceduren nedan.
 
1. Logga in på Azure-portalen på https://portal.azure.com.
2. I den **Azure Active Directory > företagsprogram** avsnitt av Azure-portalen, leta upp och välj ditt befintliga SCIM-program.
3.  I den **egenskaper** avsnittet för din befintliga SCIM-app, kopiera den **objekt-ID**.
4.  I ett nytt webbläsarfönster, går du till https://developer.microsoft.com/en-us/graph/graph-explorer och logga in som administratör för Azure AD-klient där din app har lagts till.
5. I Graph-testaren kör du kommandot nedan för att hitta ID: T för din Etableringsjobbet. Ersätt ”[objekt-id]” med ägar-ID (objekt-ID) som kopieras från det tredje steget-tjänsten.
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

 ![Hämta jobb](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "hämta jobb") 


6. Kopiera den fullständiga ”ID”-sträng som börjar med ”customappsso” eller ”scim” i resultaten.
7. Kör kommandot nedan för att hämta attributmappning konfigurationen, så att du kan göra en säkerhetskopia. Använda samma [objekt-id] som innan. Ersätt [jobb-id] med etablering jobb-ID som kopieras från det sista steget.
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
 ![Hämta schemat](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "hämta schemat") 

8. Kopiera JSON-utdata från det sista steget och spara den till en textfil. Innehåller alla anpassade attribut-mappningar som du har lagts till din gamla app och ska vara ungefär några tusen rader med JSON.
9. Kör kommandot nedan för att ta bort etablering jobbet:
 
 `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Kör kommandot nedan för att skapa ett nytt etablering jobb som har de senaste korrigeringarna i tjänsten.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs `
 `{   templateId: "scim"   } `
   
11. I resultatet av det sista steget, kopierar du den fullständiga ”ID”-sträng som börjar med ”scim”. Du kan också ansöka igen din gamla attributmappningar genom att köra kommandot nedan och Ersätt [ny-jobb-id] med den nya jobb-ID som du kopierade och ange JSON-utdata från steg #7 som begärandetexten.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema `
 `{   <your-schema-json-here>   }`

12. Gå tillbaka till första webbläsarfönstret och välj den **etablering** fliken för ditt program.
13. Kontrollera konfigurationen och sedan starta etablering jobbet. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kan jag lägga till en ny inte är ett galleriprogram-app som har den gamla användaretablering beteende?

Ja. Om du hade kodade ett program till det gamla beteende som gällde före korrigeringar och behöver för att distribuera en ny instans av det, följer du proceduren nedan. Den här proceduren beskriver hur du använder Microsoft Graph API och Microsoft Graph API-explorer för att skapa en SCIM-Etableringsjobbet som uppvisar gamla beteenden.
 
1.  Logga in på Azure-portalen på https://portal.azure.com.
2. i den **Azure Active Directory > företagsprogram > Skapa program** avsnitt av Azure portal, skapa en ny **icke-galleriet** program.
3.  I den **egenskaper** delen av den nya anpassa appen, kopiera den **objekt-ID**.
4.  I ett nytt webbläsarfönster, går du till https://developer.microsoft.com/en-us/graph/graph-explorer och logga in som administratör för Azure AD-klient där din app har lagts till.
5. I Graph-testaren kör du kommandot nedan för att initiera etableringskonfiguration för din app.
Ersätt ”[objekt-id]” med ägar-ID (objekt-ID) som kopieras från det tredje steget-tjänsten.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "customappsso"   }`
 
6. Gå tillbaka till första webbläsarfönstret och välj den **etablering** fliken för ditt program.
7. Slutför konfiguration för användaretablering, som vanligt.


## <a name="next-steps"></a>Nästa steg
[Mer information om etablering och avetablering för SaaS-program](user-provisioning.md)

