---
title: Kända problem med SCIM 2,0-protokollets kompatibilitet – Azure AD
description: Så här löser du vanliga kompatibilitetsproblem med att lägga till ett program som inte är en galleri som stöder SCIM 2,0 till Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522942"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Kända problem och lösningar med SCIM 2,0 protokoll kompatibilitet för Azure AD-tjänsten för användar etablering

Azure Active Directory (Azure AD) kan automatiskt etablera användare och grupper för program eller system som är uppkopplade av en webb tjänst med det gränssnitt som definierats i [systemet för scim (Cross-Domain Identity Management) 2,0 protokoll specifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Azure AD: s stöd för SCIM 2,0-protokollet beskrivs i [använda system för scim (Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md), som listar de delar av protokollet som det implementerar för att automatiskt etablera användare och grupper från Azure AD till program som stöder scim 2,0.

I den här artikeln beskrivs aktuella och tidigare problem med Azure AD-tjänsten för användar etablering i SCIM 2,0-protokollet och hur du kan lösa problemen.

> [!IMPORTANT]
> Den senaste uppdateringen av SCIM-klienten för Azure AD-tjänsten för användar etablering gjordes den 18 december 2018. Den här uppdateringen åtgärdade kända kompatibilitetsproblem som anges i tabellen nedan. Mer information om den här uppdateringen finns i vanliga frågor och svar nedan.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2,0-kompatibilitetsproblem och status

| **SCIM 2,0-kompatibilitetsproblem** |  **Fastsatt?** | **Åtgärds datum**  |  
|---|---|---|
| Azure AD kräver att "/scim" finns i roten för programmets SCIM-slutpunkts-URL  | Ja  |  18 december 2018 | 
| Attributen för tillägg använder punkt "."-notation före attributnamn i stället för kolon ":"-notation |  Ja  | 18 december 2018  | 
|  Uppdaterings begär Anden för flervärdesattribut innehåller ogiltig syntax för Sök vägs filter | Ja  |  18 december 2018  | 
|  Begär Anden om att skapa grupper innehåller en ogiltig schema-URI | Ja  |  18 december 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Har tjänsterna korrigeringar som beskrivs automatiskt tillämpade på min befintliga SCIM-app?

Nej. Eftersom den skulle ha gjort en större ändring i SCIM-appar som har kodats för att fungera med det äldre beteendet, tillämpades inte ändringarna automatiskt på befintliga appar.

Ändringarna tillämpas på alla nya [icke-Galleri scim-appar](../manage-apps/configure-single-sign-on-non-gallery-applications.md) som kon figurer ats i Azure Portal efter datumet för korrigeringen.

Information om hur du migrerar ett befintligt etablerings jobb för användare för att inkludera de senaste korrigeringarna finns i nästa avsnitt.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Kan jag migrera ett befintligt SCIM-baserat användar etablerings jobb för att inkludera de senaste tjänst korrigeringarna?

Ja. Om du redan använder den här program instansen för enkel inloggning och behöver migrera det befintliga etablerings jobbet för att inkludera de senaste korrigeringarna följer du stegen nedan. Den här proceduren beskriver hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att ta bort ditt gamla etablerings jobb från din befintliga SCIM-app och skapa en ny som visar det nya beteendet.

> [!NOTE]
> Om ditt program fortfarande är i utvecklings läge och ännu inte har distribuerats för enkel inloggning eller användar etablering, är den enklaste lösningen att ta bort program posten i avsnittet **Azure Active Directory > företags program** i Azure Portal och bara lägga till en ny post för programmet med alternativet **skapa program > icke-Galleri** . Detta är ett alternativ till att köra proceduren nedan.
 
1. Logga in på Azure Portal på https://portal.azure.com.
2. I avsnittet **Azure Active Directory > företags program** i Azure Portal letar du reda på och väljer ditt befintliga scim-program.
3. I avsnittet **Egenskaper** i din befintliga scim-app kopierar du **objekt-ID: t**.
4. I ett nytt webbläsarfönster går du till https://developer.microsoft.com/graph/graph-explorer och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att hitta ID: t för ditt etablerings jobb. Ersätt "[Object-ID]" med tjänstens huvud namns-ID (objekt-ID) som har kopierats från det tredje steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Hämta jobb](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Hämta jobb") 


6. I resultatet kopierar du den fullständiga "ID"-strängen som börjar med antingen "customappsso" eller "scim".
7. Kör kommandot nedan för att hämta konfigurationen för attributet-mappning så att du kan göra en säkerhets kopia. Använd samma [objekt-ID] som tidigare och Ersätt [Job-ID] med etablerings jobbets ID som har kopierats från det sista steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Hämta schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Hämta schema") 

8. Kopiera JSON-utdata från det sista steget och spara den i en textfil. Detta innehåller anpassade attribut-mappningar som du har lagt till i din gamla app och bör vara cirka några tusen rader med JSON.
9. Kör kommandot nedan för att ta bort etablerings jobbet:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Kör kommandot nedan för att skapa ett nytt etablerings jobb som har de senaste tjänst korrigeringarna.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. I resultatet för det sista steget kopierar du den fullständiga "ID"-strängen som börjar med "scim". Du kan också tillämpa dina gamla attribut mappningar på nytt genom att köra kommandot nedan, ersätta [New-Job-ID] med det nya jobb-ID som du precis kopierat och ange JSON-utdata från steg #7 som begär ande texten.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Gå tillbaka till det första webbläsarfönstret och välj fliken **etablering** för ditt program.
13. Verifiera konfigurationen och starta sedan etablerings jobbet. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Kan jag lägga till en ny app som inte är en galleri som har den gamla användar etablerings funktionen?

Ja. Om du har kodat ett program till det gamla beteende som fanns före korrigeringarna och behöver distribuera en ny instans av det, följer du anvisningarna nedan. Den här proceduren beskriver hur du använder Microsoft Graph API och Microsoft Graph API Explorer för att skapa ett SCIM etablerings jobb som visar det gamla beteendet.
 
1. Logga in på Azure Portal på https://portal.azure.com.
2. i avsnittet **Azure Active Directory > företags program > Skapa program** i Azure Portal skapar du ett nytt program **utanför galleriet** .
3. I avsnittet **Egenskaper** i din nya anpassade app kopierar du **objekt-ID: t**.
4. I ett nytt webbläsarfönster går du till https://developer.microsoft.com/graph/graph-explorer och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att initiera etablerings konfigurationen för din app.
   Ersätt "[Object-ID]" med tjänstens huvud namns-ID (objekt-ID) som har kopierats från det tredje steget.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Gå tillbaka till det första webbläsarfönstret och välj fliken **etablering** för ditt program.
7. Slutför konfigurationen för användar etablering precis som vanligt.


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om etablering och avetablering för SaaS-program](user-provisioning.md)

