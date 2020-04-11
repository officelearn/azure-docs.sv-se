---
title: Etablering av loggar i Azure Active Directory-portalen (förhandsversion) | Microsoft-dokument
description: Introduktion till etablering av aktivitetsrapporter i Azure Active Directory-portalen
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30cc8be6ad9ebffcad58c5b2412ae15ff3f26fa5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113316"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Etablera rapporter i Azure Active Directory-portalen (förhandsversion)

Rapporteringsarkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – Information om användningen av hanterade program och användarloggningsaktiviteter.
    - **Granskningsloggar** - [Granskningsloggar](concept-audit-logs.md) ger information om systemaktivitet om användare och grupphantering, hanterade program och katalogaktiviteter.
    - **Etableringsloggar** – Ange systemaktivitet om användare, grupper och roller som etableras av Azure AD-etableringstjänsten. 

- **Säkerhet** 
    - **Riskfyllda inloggningar** - En [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggningsförsök som kan ha utförts av någon som inte är den legitima ägaren av ett användarkonto.
    - **Användare som flaggats för risk** – En [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användarkonto som kan ha komprometterats.

I det här avsnittet får du en översikt över etableringsrapporten.

## <a name="prerequisites"></a>Krav

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollerna Säkerhetsadministratör, Säkerhetsläsare, Rapportläsare, Programadministratör och Molnprogramadministratör
* Globala administratörer


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Vilken Azure AD-licens behöver du för att komma åt etableringsaktiviteter?

Din klient måste ha en Azure AD Premium-licens kopplad till sig för att se rapporten alla upptableringsaktivitet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. 

## <a name="provisioning-logs"></a>Etableringsloggar

Etableringsloggarna ger svar på följande frågor:

* Vilka grupper har skapats i ServiceNow?
* Hur roller importerades från Amazon Web Services?
* Vilka användare skapades utan framgång i DropBox?

Du kan komma åt etableringsloggarna genom att välja **Etableringsloggar** i **avsnittet Övervakning** i Azure **Active Directory-bladet** i [Azure-portalen](https://portal.azure.com). Det kan ta upp till två timmar innan vissa etableringsposter visas i portalen.

![Etableringsloggar](./media/concept-provisioning-logs/access-provisioning-logs.png "Etableringsloggar")


En etableringslogg har en standardlistvy som visar:

- Identiteten
- Åtgärden
- Källsystemet
- Målsystemet
- Statusen
- Datumet


![Standardkolumner](./media/concept-provisioning-logs/default-columns.png "Standardkolumner")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Kolumnväljare](./media/concept-provisioning-logs/column-chooser.png "Kolumnväljare")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Tillgängliga kolumner](./media/concept-provisioning-logs/available-columns.png "Tillgängliga kolumner")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Detaljerad information](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>Filtrera etableringsaktiviteter

Du kan filtrera etableringsdata. Vissa filtervärden fylls dynamiskt baserat på din klient. Om du till exempel inte har några skapa händelser i din klientorganisation finns det inget filteralternativ för att skapa.
I standardvyn kan du välja följande filter:

- Identitet
- Date
- Status
- Åtgärd


![Filter](./media/concept-provisioning-logs/default-filter.png "Filter")

Med **filtret Identitet** kan du ange namnet eller identiteten som du bryr dig om. Den här identiteten kan vara en användare, grupp, roll eller ett annat objekt. Du kan söka efter objektets namn eller ID. ID varierar beroende på scenario. När ett objekt etableras från Azure AD till SalesForce är till exempel käll-ID för användaren i Azure AD medan TargetID är ID för användaren i Salesforce. Vid etablering från arbetsdag till Active Directory är käll-ID arbetstagar-ID: n arbetsdagsanställd. Observera att användarens namn kanske inte alltid finns i kolumnen Identitet. Det kommer alltid att finnas ett ID. 


Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 30 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera ett startdatum och ett slutdatum.


Med **filtret Status** kan du välja:

- Alla
- Lyckades
- Fel
- Överhoppad



Med **Action** åtgärdsfiltret kan du filtrera:

- Skapa 
- Uppdatera
- Ta bort
- Inaktivera
- Annat

Dessutom kan du också ange följande filter för filtren i standardvyn:

- Jobb-ID
- Cykel-ID
- Ändra ID
- Käll-ID
- Mål-ID
- Program


![Välj ett fält](./media/concept-provisioning-logs/add-filter.png "Välj ett fält")


- **Jobb-ID** - Ett unikt jobb-ID är associerat med varje program som du har aktiverat etablering för.   

- **Cykel-ID** - Identifierar etableringscykeln unikt. Du kan dela det här ID:t för att kunna slå upp den cykel som den här händelsen inträffade i.

- **Ändrings-ID** - Unik identifierare för etableringshändelsen. Du kan dela det här ID:t som stöd för att slå upp etableringshändelsen.   


- **Källsystem** - Gör att du kan ange var identiteten etableras från. Till exempel när du etablerar ett objekt från Azure AD till ServiceNow, är source-systemet Azure AD. 

- **Målsystem** - Gör att du kan ange var identiteten etableras. När du till exempel etablerar ett objekt från Azure AD till ServiceNow är målsystemet ServiceNow. 

- **Program** - Gör att du bara kan visa poster av program med ett visningsnamn som innehåller en viss sträng.

 

## <a name="provisioning-details"></a>Information om etablering 

När du väljer ett objekt i etableringslistevyn får du mer information om det här objektet.
Detaljerna är grupperade utifrån följande kategorier:

- Steg

- Felsökning och rekommendationer

- Ändrade egenskaper

- Sammanfattning


![Filter](./media/concept-provisioning-logs/provisioning-tabs.png "Flikar")



### <a name="steps"></a>Steg

På fliken **Steg** beskrivs de steg som vidtagits för att etablera ett objekt. Etablering av ett objekt kan bestå av fyra steg: 

- Importera objekt
- Ta reda på om objektet är i omfånget
- Matcha objekt mellan källa och mål
- Etableringsobjekt (vidta åtgärder – det kan vara en skapa, uppdatera, ta bort eller inaktivera)



![Filter](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>Felsökning och rekommendationer


**Fliken Felsökning och rekommendationer** innehåller felkoden och orsaken. Felinformationen är endast tillgänglig vid ett fel. 


### <a name="modified-properties"></a>Ändrade egenskaper

De **ändrade egenskaperna** visar det gamla värdet och det nya värdet. I de fall där det inte finns något gammalt värde är den gamla värdekolumnen tom. 


### <a name="summary"></a>Sammanfattning

På **fliken Sammanfattning** finns en översikt över vad som hände och identifierare för objektet i käll- och målsystemet. 

## <a name="what-you-should-know"></a>Det här bör du känna till

- Azure-portalen lagrar rapporterade etableringsdata i 30 dagar om du har en premiumutgåva och 7 dagar om du har en kostnadsfri utgåva..

- Du kan använda attributet Ändra ID som unik identifierare. Detta är till exempel användbart när du interagerar med produktsupport.

- Det finns för närvarande inget alternativ för att hämta etableringsdata.

- Det finns för närvarande inget stöd för logganalys.

- När du öppnar etableringsloggarna från en apps kontext filtrerar den inte automatiskt händelser till den specifika appen på det sätt som granskningsloggar gör.

## <a name="error-codes"></a>Felkoder

Använd tabellen nedan för att bättre förstå hur du löser fel som du kan hitta i etableringsloggarna. Om du vill ha felkoder som saknas kan du ge feedback med hjälp av länken längst ned på den här sidan. 

|Felkod|Beskrivning|
|---|---|
|Konflikt, EntryConflict|Korrigera attributvärdena i konflikt i Antingen Azure AD eller programmet, eller granska din matchande attributkonfiguration om det motstridiga användarkontot skulle matchas och tas över. Läs följande [dokumentation](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) för mer information om hur du konfigurerar matchande attribut.|
|TooManyRequests|Målappen avvisade det här försöket att uppdatera användaren eftersom den är överbelastad och tar emot för många begäranden. Det finns inget att göra. Detta försök dras automatiskt tillbaka. Microsoft har också underrättats om problemet.|
|InternalServerError |Målappen returnerade ett oväntat fel. Det kan finnas ett serviceproblem med målprogrammet som hindrar detta från att fungera. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Azure AD kunde autentisera med målprogrammet, men har inte auktoriserats för att utföra uppdateringen. Läs igenom eventuella instruktioner som tillhandahålls av målprogrammet samt respektive [programhandledning](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).|
|UnprocessableEntity|Målprogrammet returnerade ett oväntat svar. Konfigurationen av målprogrammet kanske inte är korrekt, eller så kan det finnas ett serviceproblem med målprogrammet som hindrar detta från att fungera.|
|WebExceptionProtocolError |Ett HTTP-protokollfel uppstod vid anslutning till målprogrammet. Det finns inget att göra. Detta försök kommer automatiskt att dras tillbaka om 40 minuter.|
|InvalidAnchor|Det finns inte längre en användare som tidigare har skapats eller matchats av etableringstjänsten. Kontrollera att användaren finns. Om du vill tvinga fram en ommatchning av alla användare använder du MS Graph API för att [starta om jobbet](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Observera att omstart av etablering utlöser en inledande cykel, vilket kan ta tid att slutföra. Cachetjänsten tar också bort den cache som etableringstjänsten använder för att fungera, vilket innebär att alla användare och grupper i klienten måste utvärderas igen och vissa etableringshändelser kan tas bort.|
|Har inte implementerats | Målappen returnerade ett oväntat svar. Konfigurationen av appen kanske inte är korrekt eller så kan det finnas ett serviceproblem med målappen som hindrar detta från att fungera. Läs igenom eventuella instruktioner som tillhandahålls av målprogrammet samt respektive [programhandledning](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). |
|ObligatorisktFieldsMissing, MissingValues |Det gick inte att skapa användaren eftersom obligatoriska värden saknas. Korrigera de saknade attributvärdena i källposten eller granska din matchande attributkonfiguration för att säkerställa att de obligatoriska fälten inte utelämnas. [Läs mer](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) om hur du konfigurerar matchande attribut.|
|SchemaAttributeNotFound |Det gick inte att utföra åtgärden eftersom ett attribut angavs som inte finns i målprogrammet. Se [dokumentationen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) om attributanpassning och se till att konfigurationen är korrekt.|
|InternalError |Ett internt tjänstfel uppstod i Azure AD-etableringstjänsten. Det finns inget att göra. Detta försök kommer automatiskt att försöka igen i 40 minuter.|
|Ogiltigt Ogiltigt |Det gick inte att utföra åtgärden på grund av ett attributvärde som innehåller ett ogiltigt domännamn. Uppdatera domännamnet på användaren eller lägg till det i den tillåtna listan i målprogrammet. |
|Timeout |Det gick inte att slutföra åtgärden eftersom målprogrammet tog för lång tid att svara. Det finns inget att göra. Detta försök kommer automatiskt att försöka igen i 40 minuter.|
|LicenseLimitExceeded|Det gick inte att skapa användaren i målprogrammet eftersom det inte finns några tillgängliga licenser för den här användaren. Antingen skaffa ytterligare licenser för målprogrammet eller granska dina användartilldelningar och attributmappningskonfiguration för att säkerställa att rätt användare tilldelas med rätt attribut.|
|Duplicera ArgetEntries  |Det gick inte att slutföra åtgärden eftersom mer än en användare i målprogrammet hittades med de konfigurerade matchande attributen. Ta antingen bort dubblettanvändaren från målprogrammet eller konfigurera om attributmappningarna enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplikatResurserPoster | Det gick inte att slutföra åtgärden eftersom mer än en användare hittades med de konfigurerade matchande attributen. Ta antingen bort dubblettanvändaren eller konfigurera om attributmappningarna enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Nästa steg

* [Kontrollera status för användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problem med att konfigurera användaretablering till ett Azure AD Gallery-program](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


