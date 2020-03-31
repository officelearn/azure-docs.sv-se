---
title: 'Snabbstart: Kom igång med Azure Sentinel'
description: Azure Sentinel Snabbstart – Kom igång med Azure Sentinel
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 95dcc135593c566eb1319ed52df3df6c1ada6609
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067688"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Snabbstart: Kom igång med Azure Sentinel




I den här snabbstarten får du lära dig hur du snabbt kan visa och övervaka vad som händer i hela din miljö med Azure Sentinel. När du har anslutit dina datakällor till Azure Sentinel får du omedelbar visualisering och analys av data så att du vet vad som händer i alla dina anslutna datakällor. Azure Sentinel ger dig arbetsböcker som ger dig full kraft av verktyg som redan finns i Azure samt tabeller och diagram som är inbyggda för att ge dig analyser för dina loggar och frågor. Du kan antingen använda inbyggda arbetsböcker eller enkelt skapa en ny arbetsbok, från grunden eller baserat på en befintlig arbetsbok. 

## <a name="get-visualization"></a>Hämta visualisering

För att visualisera och få analys av vad som händer på din miljö, först, ta en titt på översiktsinstrumentpanelen för att få en uppfattning om säkerhetshållningen i din organisation. Du kan klicka på varje element i dessa paneler för att öka detaljnivån till de rådata som de skapas från. För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka använder Azure Sentinel en fusionsteknik för att korrelera aviseringar till incidenter. **incidenter** är grupper av relaterade aviseringar som tillsammans skapar en användbar incident som du kan undersöka och lösa.

- I Azure-portalen väljer du Azure Sentinel och väljer sedan den arbetsyta som du vill övervaka.

  ![Översikt över Azure Sentinel](./media/qs-get-visibility/overview.png)

- Verktygsfältet överst visar hur många händelser du har under den valda tidsperioden, och det jämför det med de föregående 24 timmarna. Verktygsfältet talar om för dig från dessa händelser, de aviseringar som utlöstes (det lilla talet representerar förändring under de senaste 24 timmarna), och sedan talar om för dig för dessa händelser, hur många som är öppna, pågår och stängs. Kontrollera att det inte finns en dramatisk ökning eller minskning av antalet händelser. Om det finns en droppe kan det vara så att en anslutning har stoppats från att rapportera till Azure Sentinel. Om det blir en ökning kan något misstänkt ha hänt. Kontrollera om du har nya aviseringar.

   ![Azure Sentinel tratt](./media/qs-get-visibility/funnel.png)

Huvudtexten på översiktssidan ger en inblick i arbetsytans säkerhetsstatus:

- **Händelser och aviseringar över tid**: Visar antalet händelser och hur många aviseringar som har skapats från dessa händelser. Om du ser en topp som är ovanlig, bör du se varningar för det - om det finns något ovanligt där det finns en topp i händelser men du inte ser varningar, kan det vara anledning till oro.

- **Potentiella skadliga händelser**: När trafik identifieras från källor som är kända för att vara skadliga varnar Azure Sentinel dig på kartan. Om du ser orange är det inkommande trafik: någon försöker komma åt din organisation från en känd skadlig IP-adress. Om du ser Utgående (röd) aktivitet innebär det att data från nätverket strömmas ut från organisationen till en känd skadlig IP-adress.

   ![Azure Sentinel-karta](./media/qs-get-visibility/map.png)


- **Senaste incidenter**: För att visa dina senaste incidenter, deras allvarlighetsgrad och antalet aviseringar i samband med incidenten. Om du ser som plötslig topp i en viss typ av avisering, kan det innebära att det finns en aktiv attack som för närvarande körs. Om du till exempel har en plötslig topp på 20 Pass-the-hash-händelser från Azure ATP är det möjligt att någon för närvarande försöker attackera dig.

- **Datakälla avvikelser:** Microsofts dataanalytiker skapade modeller som ständigt söker data från dina datakällor efter avvikelser. Om det inte finns några avvikelser visas ingenting. Om avvikelser upptäcks, bör du djupdyka i dem för att se vad som hände. Klicka till exempel på toppen i Azure Activity. Du kan klicka på **Diagram** för att se när spiken inträffade och sedan filtrera efter aktiviteter som inträffade under den tidsperioden för att se vad som orsakade spiken.

   ![Azure Sentinel-karta](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Använda inbyggda arbetsböcker<a name="dashboards"></a>

Inbyggda arbetsböcker innehåller integrerade data från dina anslutna datakällor så att du djupdykning i de händelser som genereras i dessa tjänster. De inbyggda arbetsböckerna inkluderar Azure AD, Azure-aktivitetshändelser och lokala, som kan vara data från Windows-händelser från servrar, från aviseringar från första part, från alla tredjepartsvarningar, inklusive brandväggstrafikloggar, Office 365 och osäkra protokoll baserade på Windows Händelser. Arbetsböckerna är baserade på Azure Monitor-arbetsböcker för att ge dig förbättrad anpassningsbarhet och flexibilitet när du utformar din egen arbetsbok. Mer information finns i [Arbetsböcker](../azure-monitor/app/usage-workbooks.md).

1. Under **Inställningar**väljer du **Arbetsböcker**. Under **Installerad**kan du se alla installerade arbetsböcker. Under **Alla**kan du se hela galleriet med inbyggda arbetsböcker som är tillgängliga för installation. 
2. Sök efter en specifik arbetsbok för att se hela listan och beskrivningen av vad varje erbjudanden. 
3. Om du antar att du använder Azure AD för att komma igång med Azure Sentinel rekommenderar vi att du installerar minst följande arbetsböcker:
   - **Azure AD**: Använd något av eller båda av följande:
       - **Azure AD-inloggningar analyserar** inloggningar över tid för att se om det finns avvikelser. Den här arbetsböcker innehåller misslyckade inloggningar av program, enheter och platser så att du snabbt kan märka om något ovanligt händer. Var uppmärksam på flera misslyckade inloggningar. 
       - **Azure AD-granskningsloggar** analyserar administratörsaktiviteter, till exempel ändringar i användare (lägg till, ta bort osv.), gruppskapande och ändringar.  

   - Lägg till en arbetsbok för brandväggen. Lägg till exempel till Palo Alto-arbetsboken. Arbetsboken analyserar brandväggstrafiken och ger dig korrelationer mellan brandväggsdata och hothändelser och belyser misstänkta händelser över entiteter. Arbetsböcker ger dig information om trender i trafiken och låter dig öka detaljnivån och filtrera resultat. 

      ![Pal Alto instrumentpanel](./media/qs-get-visibility/palo-alto-week-query.png)


Du kan anpassa arbetsböckerna antingen genom ![](./media/qs-get-visibility/edit-query-button.png)att redigera huvudfrågeknappen . Du kan klicka ![](./media/qs-get-visibility/go-to-la-button.png) på knappknappen för att gå till [Log Analytics för att redigera frågan där,](../azure-monitor/log-query/get-started-portal.md)och du kan välja ellips (...) och välja Anpassa **paneldata**, vilket gör att du kan redigera huvudtidsfiltret eller ta bort de specifika panelerna från arbetsboken.

Mer information om hur du arbetar med frågor finns i [Självstudiekurs: Visuella data i Logganalys](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Lägga till en ny panel

Om du vill lägga till en ny panel kan du lägga till den i en befintlig arbetsbok, antingen en som du skapar eller en inbyggd Azure Sentinel-arbetsbok. 
1. Skapa en panel i Logganalys med hjälp av instruktionerna i [Självstudiekurs: Visuella data i Logganalys](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. När panelen har skapats väljer du arbetsboken där du vill att panelen ska visas under **Fäst.**

## <a name="create-new-workbooks"></a>Skapa nya arbetsböcker
Du kan skapa en ny arbetsbok från grunden eller använda en inbyggd arbetsbok som grund för den nya arbetsboken.

1. Om du vill skapa en ny arbetsbok från grunden väljer du **Arbetsböcker** och sedan **+Ny arbetsbok**.
2. Välj den prenumeration som arbetsboken skapas i och ge den ett beskrivande namn. Varje arbetsbok är en Azure-resurs som alla andra, och du kan tilldela den roller (RBAC) för att definiera och begränsa vem som kan komma åt. 
3. Om du vill att den ska visas i arbetsböckerna för att fästa visualiseringar på måste du dela den. Klicka på **Dela** och sedan **på Hantera användare**. 
 
1. Använd **checkåtkomst-** och **rolltilldelningarna** på samma sätt som för andra Azure-resurser. Mer information finns i [Dela Azure-arbetsböcker med hjälp av RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Exempel på nya arbetsböcker

Med följande exempelfråga kan du jämföra trafiktrender över veckor. Du kan enkelt växla vilken enhetsleverantör och datakälla du kör frågan på. I det här exemplet används SecurityEvent från Windows, du kan växla det till att köras på AzureActivity eller CommonSecurityLog på någon annan brandvägg.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Du kanske vill skapa en fråga som innehåller data från flera källor. Du kan skapa en fråga som tittar på Azure Active Directory-granskningsloggar för nya användare som just har skapats och sedan kontrollerar dina Azure-loggar för att se om användaren har börjat göra rolltilldelningsändringar inom 24 timmar efter skapandet. Den misstänkta aktiviteten skulle dyka upp på den här instrumentpanelen:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Du kan skapa olika arbetsböcker baserat på rollen för den person som tittar på data och vad de letar efter. Du kan till exempel skapa en arbetsbok för nätverksadministratören som innehåller brandväggsdata. Du kan också skapa arbetsböcker baserat på hur ofta du vill titta på dem, om det finns saker som du vill granska dagligen och andra objekt som du vill kontrollera en gång i timmen, till exempel kanske du vill titta på dina Azure AD-inloggningar varje timme för att söka efter avvikelser . 

## <a name="create-new-detections"></a>Skapa nya identifieringar

Generera identifieringar på de [datakällor som du har anslutit till Azure Sentinel](connect-data-sources.md) för att undersöka hot i din organisation.

När du skapar en ny identifiering kan du utnyttja de inbyggda identifieringarna som skapats av Microsofts säkerhetsforskare och som är anpassade till de datakällor som du har anslutit.

Om du vill visa alla färdiga identifieringar går du till **Analytics** och sedan **regelmallar**. Den här fliken innehåller alla azure sentinel-inbyggda regler.

   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Mer information om hur du får färdiga identifieringar finns [i Självstudiekurs: Få inbyggd analys](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten lärde du dig hur du kommer igång med Azure Sentinel. Fortsätt till handledningen för [hur man upptäcker hot](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Skapa anpassade regler för identifiering av hot](tutorial-detect-threats-custom.md) för att automatisera dina svar på hot.

