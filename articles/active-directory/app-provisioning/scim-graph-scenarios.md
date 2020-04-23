---
title: Använda SCIM, Microsoft Graph och Azure AD-etableringstjänsten för att etablera användare och berika ditt program med de data som behövs | Microsoft-dokument
description: Använda SCIM och Microsoft Graph tillsammans för att etablera användare och berika ditt program med de data som behövs .
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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087626"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Använda SCIM och Microsoft Graph tillsammans för att etablera användare och berika ditt program med de data som behövs

**Målgrupp:** Det här dokumentet riktar sig till utvecklare som bygger program som är integrerade med Azure AD. För andra som vill integrera ett befintligt program som Zoom, ServiceNow och DropBox kan du hoppa över detta och granska programmets specifika [självstudier](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 

**Vanliga scenarier**

> [!div class="checklist"]
> * Skapa användare automatiskt i mitt program
> * Ta automatiskt bort användare från mitt program när de inte längre ska ha åtkomst
> * Integrera mitt program med flera identitetsleverantörer för etablering
> * Berika mitt program med data från Microsoft-tjänster som Sharepoint, Outlook och Office.
> * Skapa, uppdatera och ta bort användare och grupper automatiskt i Azure AD och Active Directory

![BESLUTsträd för SCIM Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Scenario 1: Skapa användare automatiskt i min app
Idag skapar IT-administratörer manuellt användarkonton i mitt program varje gång någon behöver åtkomst eller regelbundet laddar upp CSV-filer. Processen är tidskrävande för kunder och saktar ner antagandet av min ansökan. Allt jag behöver är grundläggande [användarinformation](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) som namn, e-post och userPrincipalName för att skapa en användare. Dessutom använder mina kunder olika IdPs och jag har inte resurser för att upprätthålla en synkroniseringsmotor och anpassade integrationer med varje IdP. 

**Rekommendation**: Stöd en SCIM-kompatibel [/Användare](https://aka.ms/scimreferencecode) slutpunkt. Dina kunder kan enkelt använda den här slutpunkten för att integrera med Azure AD-etableringstjänsten och automatiskt skapa användarkonton när de behöver åtkomst. Du kan skapa slutpunkten en gång och den kommer att vara kompatibel med alla IdPs, utan att behöva underhålla en synkroniseringsmotor. Kolla in exempelbegäran nedan för hur en användare skulle skapas.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Scenario 2: Ta automatiskt bort användare från min app
Kunderna som använder mitt program är säkerhetsfokuserade och har styrningskrav för att ta bort konton när anställda inte behöver dem längre. Hur kan jag automatisera avetablering från min ansökan?

**Rekommendation:** Stöd för en SCIM-kompatibel /Users-slutpunkt. Azure AD-etableringstjänsten skickar begäranden om att inaktivera och ta bort när användaren inte längre ska ha åtkomst. Vi rekommenderar att du stöder både inaktivering och borttagning av användare. Se exemplen nedan för hur en begäran om att inaktivera och ta bort ser ut. 

Inaktivera användare
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Ta bort användare
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Scenario 3: Automatisera hanteringen av gruppmedlemskap i min app
Mitt program är beroende av grupper för åtkomst till olika resurser och kunder vill återanvända de grupper som de har i Azure AD. Hur importerar jag grupper från Azure AD och håller dem uppdaterade när medlemskapen ändras?  

**Rekommendation:** Stöd för en SCIM-kompatibel /Grupper [slutpunkt](https://aka.ms/scimreferencecode). Azure AD-etableringstjänsten tar hand om att skapa grupper och hantera medlemsuppdateringar i ditt program. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Scenario 4: Berika min app med data från Microsoft-tjänster som Teams, Outlook och OneDrive.
Mitt program är inbyggt i Microsoft Teams och är beroende av meddelandedata. Dessutom lagrar vi filer för användare i OneDrive. Hur kan jag berika mitt program med data från dessa tjänster och i Microsoft?

**Rekommendation:** [Microsoft Graph](https://docs.microsoft.com/graph/) är din startpunkt för att komma åt Microsoft-data. Varje arbetsbelastning exponerar API:er med de data du behöver. Microsoft-diagrammet kan användas tillsammans med [SCIM-etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) för scenarierna ovan. Du kan använda SCIM för att etablera grundläggande användarattribut i ditt program medan du anropar i diagram för att hämta andra data som du behöver. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Scenario 5: Spåra ändringar i Microsoft-tjänster som Teams, Outlook och Azure AD.
Jag måste kunna spåra ändringar i Teams och Outlook-meddelanden och reagera på dem i realtid. Hur kan jag få dessa ändringar skjuts till mitt program?

**Rekommendation:** Microsoft Graph innehåller [ändringsmeddelanden](https://docs.microsoft.com/graph/webhooks) och ändringsspårning för olika resurser. Observera följande begränsningar av ändringsmeddelanden:
- Om en händelsemottagare bekräftar en händelse, men inte agerar på den av någon anledning, kan händelsen gå förlorad
- Om en händelsemottagare bekräftar en händelse, men inte agerar på den av någon anledning, kan händelsen gå förlorad
- Ändringsmeddelanden innehåller inte alltid [resursdata](https://docs.microsoft.com/graph/webhooks-with-resource-data) Av ovanstående orsaker använder utvecklare ofta ändringsmeddelanden tillsammans med ändringsspårning för synkroniseringsscenarier. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Scenario 6: Etablera användare och grupper i Azure AD.
Mitt program skapar information om en användare som kunderna behöver i Azure AD. Detta kan vara ett HR-program än hanterar anställning, en kommunikationsapp som skapar telefonnummer för användare eller någon annan app som genererar data som skulle vara värdefulla i Azure AD. Hur fyller jag i användarposten i Azure AD med dessa data? 

**Rekommendation** Microsoft-diagrammet visar slutpunkter för /Users och /Groups som du kan integrera med idag för att etablera användare i Azure AD. Observera att Azure Active Directory inte stöder att skriva tillbaka dessa användare till Active Directory. 

> [!NOTE]
> Microsoft har en etableringstjänst som hämtar data från HR-program som Workday och SuccessFactors. Dessa integrationer byggs och hanteras av Microsoft. För introduktion av ett nytt HR-program till vår tjänst kan du begära det på [UserVoice.](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests) 

## <a name="related-articles"></a>Relaterade artiklar

- [Granska dokumentationen för synkroniseringen av Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrera en anpassad SCIM-app med Azure AD](use-scim-to-provision-users-and-groups.md)
