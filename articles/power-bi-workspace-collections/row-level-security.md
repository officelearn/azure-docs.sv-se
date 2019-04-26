---
title: Säkerhet på postnivå med Power BI-arbetsytesamlingar
description: Information om säkerhet på radnivå med Power BI-Arbetsytesamlingar
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: ae9819cd5b27d794f26227f342cb0fdf1b0ab644
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343074"
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Säkerhet på postnivå med Power BI-arbetsytesamlingar

Säkerhet på radnivå (RLS) kan användas för att begränsa användaråtkomst till viss data i en rapport eller datauppsättning, vilket ger flera olika användare kan använda samma rapport när alla se olika data. Power BI-Arbetsytesamlingar stöd för datauppsättningar som konfigurerats med RLS.

![Flödet av säkerhet på radnivå i Power BI-Arbetsytesamlingar](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Power BI-arbetsytesamlingar fasas ut och är tillgänglig till juni 2018 eller det som anges i ditt avtal. Du uppmanas att planera migreringen till Power BI Embedded för att undvika avbrott i programmet. Information om hur du migrerar dina data till Power BI Embedded finns i [Migrera Power BI-arbetsytesamlingar till Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

För att kunna använda RLS är det viktigt att du förstå tre huvudsakliga koncept: Användare, roller och regler. Låt oss ta en närmare titt på var och en:

**Användare** – dessa är de faktiska slutanvändarna som visar rapporter. I Power BI-Arbetsytesamlingar identifieras användare av username-egenskapen i en Apptoken.

**Roller** – användare tillhör roller. En roll är en behållare för regler och har namn som ”säljchef” eller ”Sales Rep”. I Power BI-Arbetsytesamlingar identifieras användare av egenskapen roller i en App-Token.

**Regler** – roller har regler och dessa regler är de faktiska filter som ska tillämpas på data. Detta kan vara så enkla som ”land = USA” eller något mycket mer dynamiskt.

### <a name="example"></a>Exempel

För resten av den här artikeln använder ge vi ett exempel på redigering RLS och förbrukar som i ett inbäddat program. Vårt exempel använder den [detaljhandelsanalys](https://go.microsoft.com/fwlink/?LinkID=780547) PBIX-fil.

![Exempel-försäljningsrapport](media/row-level-security/scenario-2.png)

Vårt detaljhandelsexempel visar försäljning för alla butiker i en viss kedja. Utan RLS, oavsett vilken Regionchef manager loggar in och visar rapporten, ser de samma data. Företagsledningen har fastställt varje distriktschef bör endast se försäljning för butiker de hanterar och då kan vi använda RLS.

RLS har skrivits i Power BI Desktop. När datauppsättningen och rapporten öppnas växlar vi till diagramvy för att visa schemat:

![Modelldiagram i Power BI Desktop](media/row-level-security/diagram-view-3.png)

Här följer några saker att Observera med schemat:

* Alla åtgärder som **totalförsäljning**, lagras i den **försäljning** faktatabell.
* Det finns fyra ytterligare relaterade dimensionstabeller: **Objektet**, **tid**, **Store**, och **distrikt**.
* Pilar på relationsraderna visar hur filter kan flöda från en tabell till en annan. Exempel: om ett filter är placerat på **tid [Date]**, i det aktuella schemat filtrerar den bara ned värdena i den **försäljning** tabell. Inga andra tabeller kan påverkas av det här filtret eftersom alla pilar i relationsraderna pekar på försäljningstabellen och inte bort.
* Den **distrikt** tabell visar vem som är chef för varje distrikt:
  
  ![Distrikt tabellrader](media/row-level-security/district-table-4.png)

Baserat på det här schemat, om vi använder ett filter på den **Distriktschef** kolumnen i tabellen Distrikt och om filtret matchar användaren som visar rapporten, som filtrerar också filter ned den **Store** och  **Försäljning** tabeller att endast visa data för det specifika distrikt manager.

Så här gör du:

1. På fliken modellering **hantera roller**.  
   ![Hantera roller knappen i menyfliksområdet modellering](media/row-level-security/modeling-tab-5.png)
2. Skapa en ny roll som kallas **Manager**.  
   ![Skapa roller i Power BI Desktop](media/row-level-security/manager-role-6.png)
3. I den **distrikt** tabellen anger sedan följande DAX-uttryck: **[Distriktchef] = USERNAME()**  
   ![DAX-filteruttryck för tabellen i rollen](media/row-level-security/manager-role-7.png)
4. Att kontrollera att reglerna fungerar på den **modellering** fliken **Visa som roller**, och ange sedan följande:  
   ![Visa som roller](media/row-level-security/view-as-roles-8.png)

   Rapporterna kommer nu att visa data som om du är inloggad som **Andrew Ma**.

Tillämpar filtret på sätt som vi filtrerar ned alla poster i den **distrikt**, **Store**, och **försäljning** tabeller. Men på grund av filterriktningen för relationerna mellan **försäljning** och **tid**, **försäljning** och **objekt**, och **Objekt** och **tid** tabeller filtreras inte ned.

![Diagramvy med relationer markerat](media/row-level-security/diagram-view-9.png)

Som kan vara ok för det här kravet, men om vi inte vill hanterare kan visa objekt som de inte har någon försäljning kan vi kan aktivera dubbelriktad korsfiltrering för relationen och flow säkerhetsfilter i båda riktningarna. Detta kan göras genom att redigera relationen mellan **försäljning** och **objekt**, så här:

![Korsfilterriktning för relationen](media/row-level-security/edit-relationship-10.png)

Filter kan nu också flöda från tabellen Sales till den **objekt** tabell:

![Filterikonen riktning för relationen i diagramvyn](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Om du använder DirectQuery-läge för dina data, måste du aktivera dubbelriktad mellan filtrering genom att välja de här två alternativen:

1. **Filen** -> **alternativ och inställningar** -> **Förhandsversionsfunktioner** -> **aktivera korsfiltrering i båda riktningarna för DirectQuery** .
2. **Filen** -> **alternativ och inställningar** -> **DirectQuery** -> **Tillåt obegränsade åtgärder i DirectQuery-läge**.

Om du vill veta mer om dubbelriktad korsfiltrering kan hämta den [dubbelriktad korsfiltrering i SQL Server Analysis Services 2016 och Power BI Desktop](https://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional%20cross-filtering%20in%20Analysis%20Services%202016%20and%20Power%20BI.docx) White Paper.

Det här täcker allt arbete som måste göras i Power BI Desktop, men det finns en mer mängd arbete som behöver göras för att göra RLS-regler vi definierade arbete i Power BI Embedded. Användare autentiseras och auktoriseras av ditt program och apptoken som används för att bevilja användaråtkomst till en specifik rapport i Power BI Embedded. Power BI Embedded har inte någon specifik information om vem din användare är. För att RLS ska fungera måste du skicka ytterligare kontext som en del av din apptoken:

* **användarnamn** (valfritt) – används med RLS detta är en sträng som kan användas för att identifiera användaren när du använder RLS-regler. Se använder rad på radnivå med Power BI Embedded
* **roller** – en sträng som innehåller rollerna som kan väljas när du använder säkerhet på radnivå regler. Om du skickar mer än en roll bör de skickas som strängmatris.

Du skapar token med hjälp av den [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metod. Om egenskapen username finns måste du också ange minst ett värde i roller.

Du kan till exempel ändra EmbedSample. DashboardController rad 55 kan uppdateras från

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

till

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Fullständig apptoken ser ut ungefär så här:

![JSON web token-exempel](media/row-level-security/app-token-string-12.png)

Nu, med alla delar är tillsammans när någon loggar in på appen ska visa den här rapporten visas de data som de har behörighet att visa enligt definitionen i våra säkerhet på radnivå.

![Rapporten visas i programmet](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Se också

[Säkerhet på radnivå (RLS) med Power](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Autentisering och auktorisering i Power BI-arbetsytesamlingar](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Inbäddat exempel med JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Fler frågor? [Försök med Power BI Community](https://community.powerbi.com/)
