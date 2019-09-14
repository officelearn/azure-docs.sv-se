---
title: Etablering av loggar i Azure Active Directory portal (förhands granskning) | Microsoft Docs
description: Introduktion till etablering av aktivitets rapporter i Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983505"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Etablering av rapporter i Azure Active Directory portal (för hands version)

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om användningen av hanterade program och användar inloggnings aktiviteter.
    -  - Gransknings loggar[gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
    - **Etablerings loggar** – ger system aktivitet om användare, grupper och roller som tillhandahålls av Azure AD Provisioning-tjänsten. 

- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användar konto som kan ha komprometterats.

I det här avsnittet får du en översikt över etablerings rapporten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollerna säkerhets administratör, säkerhets läsare, rapport läsare, program administratör och moln program administratör
* Globala administratörer


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Vilken Azure AD-licens behöver du för att få åtkomst till etablerings aktiviteter?

Din klient organisation måste ha en Azure AD Premium licens kopplad till sig för att se rapporten all etablerings aktivitet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. 

## <a name="provisioning-logs"></a>Etableringsloggar

Etablerings loggarna ger svar på följande frågor:

* Vilka grupper har skapats i ServiceNow?
* Hur roller importerades från Amazon Web Services?
* Vad användarna inte kunde skapa i DropBox?

Du kan komma åt etablerings loggarna genom att välja **etablerings loggar** i avsnittet **övervakning** på bladet **Azure Active Directory** i [Azure Portal](https://portal.azure.com). Det kan ta upp till två timmar för vissa etablerings poster att visas i portalen.

![Etablerings loggar](./media/concept-provisioning-logs/access-provisioning-logs.png "Etablerings loggar")


En etablerings logg har en Standardlistvy som visar:

- Identiteten
- Åtgärden
- Käll systemet
- Mål systemet
- Status
- Datum


![Standard kolumner](./media/concept-provisioning-logs/default-columns.png "Standard kolumner")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Kolumn väljare](./media/concept-provisioning-logs/column-chooser.png "Kolumn väljare")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Tillgängliga kolumner](./media/concept-provisioning-logs/available-columns.png "Tillgängliga kolumner")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Detaljerad information](./media/concept-provisioning-logs/steps.png "Filtrera")


## <a name="filter-provisioning-activities"></a>Filtrera etablerings aktiviteter

Om du vill begränsa de rapporterade data till en nivå som passar dig kan du filtrera etablerings data med hjälp av följande standard fält. Observera att värdena i filtren fylls i dynamiskt baserat på din klient. Om du till exempel inte har några skapa-händelser i din klient organisation, så finns det inget filter alternativ för att skapa.

- Identitet
- Action
- Käll system
- Mål system
- State
- Date


![Filtrera](./media/concept-provisioning-logs/filter.png "Filtrera")

Med filtret **identitet** kan du ange namnet eller identiteten som du bryr dig om. Den här identiteten kan vara en användare, grupp, roll eller något annat objekt. Du kan söka efter objektets namn eller ID. ID varierar beroende på scenario. När ett objekt till exempel konfigureras från Azure AD till SalesForce, är käll-ID: t objekt-ID för användaren i Azure AD medan TargetID är användarens ID i Salesforce. Vid etablering från arbets dagar till Active Directory, är käll-ID: t arbets dagen anställdas anställnings-ID. Observera att namnet på användaren kanske inte alltid finns i identitets kolumnen. Det kommer alltid att finnas ett ID. 

Med filtret för **käll systemet** kan du ange var identiteten ska hämtas från. När ett objekt till exempel konfigureras från Azure AD till ServiceNow är käll systemet Azure AD. 

Med **mål system** filtret kan du ange var identiteten ska hämtas till. När ett objekt till exempel konfigureras från Azure AD till ServiceNow, är mål systemet ServiceNow. 

Med **status** filtret kan du välja:

- Alla
- Lyckades
- Fel
- Hoppades över

Med **Åtgärds** filtret kan du filtrera:

- Skapa 
- Uppdatera
- Ta bort
- Inaktivera
- Annat

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden är:

- 1 månad
- 7 dagar
- 30 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera ett start datum och ett slutdatum.


Förutom standard fälten, kan du även inkludera följande fält i filtret:

- **Jobb-ID** – ett unikt jobb-ID är associerat med varje program som du har aktiverat etableringen för.   

- **Cykel-ID** – identifierar etablerings cykeln unikt. Du kan dela detta ID för att få stöd för att leta upp den cykel där händelsen inträffade.

- **Ändra ID** – unik identifierare för etablerings händelsen. Du kan dela detta ID för att ge stöd för att leta upp etablerings händelsen.   



  

## <a name="provisioning-details"></a>Information om etablering 

När du väljer ett objekt i etablerings listans vy får du mer information om det här objektet.
Detaljerna grupperas baserat på följande kategorier:

- Steg

- Felsöka och rekommendationer

- Ändrade egenskaper

- Sammanfattning


![Filtrera](./media/concept-provisioning-logs/provisioning-tabs.png "Flikar")



### <a name="steps"></a>Steg

På fliken **steg** beskrivs de steg som vidtas för att etablera ett objekt. Etablering av ett objekt kan bestå av fyra steg: 

- Importera objekt
- Avgöra om objektet är i omfånget
- Matcha objekt mellan källa och mål
- Etablera objekt (vidta åtgärder – det kan vara en skapa, uppdatera, ta bort eller inaktivera)



![Filtrera](./media/concept-provisioning-logs/steps.png "Filtrera")


### <a name="troubleshoot-and-recommendations"></a>Felsöka och rekommendationer


På fliken **fel sökning och rekommendationer** visas felkoden och orsaken. Fel informationen är endast tillgänglig om det skulle uppstå ett fel. 


### <a name="modified-properties"></a>Ändrade egenskaper

De **ändrade egenskaperna** visar det gamla värdet och det nya värdet. I fall där det inte finns något gammalt värde är kolumnen gammalt värde tom. 


### <a name="summary"></a>Sammanfattning

Fliken **Sammanfattning** ger en översikt över vad som hände och identifierare för objektet i käll-och mål systemet. 

## <a name="what-you-should-know"></a>Det här bör du känna till

- Azure Portal lagrar rapporterade etablerings data i 30 dagar om du har en Premium-version och 7 dagar om du har en kostnads fri version.

- Du kan använda attributet ändra ID som unik identifierare. Detta är till exempel användbart när du interagerar med produkt supporten.

- Det finns för närvarande inget alternativ för att ladda ned etablerings data.

- Det finns för närvarande inget stöd för Log Analytics.

- När du har åtkomst till etablerings loggarna från kontexten för en app filtrerar den inte automatiskt händelser till den aktuella appen som gransknings loggar gör.

## <a name="next-steps"></a>Nästa steg

* [Kontrol lera status för användar etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problem med att konfigurera användar etablering i ett Azure AD Gallery-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


