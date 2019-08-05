---
title: Snabb start för Azure Sentinel – kom igång med Azure Sentinel Preview | Microsoft Docs
description: Snabb start för Azure Sentinel – kom igång med Azure Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c6434b4f5808c349d5e0ab04dafc5233f6ec63ac
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780474"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Snabbstart: Kom igång med Azure Sentinel Preview

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


I den här snabb starten får du lära dig hur du snabbt kan visa och övervaka vad som händer i din miljö med hjälp av Azure Sentinel. När du har anslutit dina data källor till Azure Sentinel får du direkt visualisering och analys av data så att du vet vad som händer i alla dina anslutna data källor. Azure Sentinel ger dig till gång till instrument paneler som ger dig alla verktyg som redan är tillgängliga i Azure samt tabeller och diagram som är inbyggda för att ge dig analyser för dina loggar och frågor. Du kan antingen använda inbyggda instrument paneler eller skapa en ny instrument panel enkelt från början eller baserat på en befintlig instrument panel. 

## <a name="get-visualization"></a>Hämta visualisering

Om du vill visualisera och få en analys av vad som händer i din miljö kan du först ta en titt på översikts instrument panelen för att få en uppfattning om organisationens säkerhets position. Du kan klicka på varje element i dessa paneler för att öka detalj nivån till de rå data som de skapats från. För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka, använder Azure Sentinel en fusions teknik för att korrelera aviseringar till incidenter. **incidenter** är grupper med relaterade aviseringar som tillsammans skapar en åtgärds bara incident som du kan undersöka och lösa.

- I Azure Portal väljer du Azure Sentinel och väljer sedan den arbets yta som du vill övervaka.

  ![Översikt över Azure Sentinel](./media/qs-get-visibility/overview.png)

- Verktygsfältet högst upp visar hur många händelser du fick under den valda tids perioden och jämför det med de föregående 24 timmarna. I verktygsfältet får du information om de aviseringar som har utlösts (det lilla värdet representerar de senaste 24 timmarna) och anger sedan för dessa händelser, hur många som är öppna, pågår och stängda. Kontrol lera att det inte finns någon dramatisk ökning eller ta bort antalet händelser. Om det finns ett släpp, kan det vara en anslutning som slutade rapportera till Azure Sentinel. Om det uppstår en ökning kan något misstänkt inträffa. Kontrol lera om du har nya aviseringar.

   ![Azure Sentinel-tratt](./media/qs-get-visibility/funnel.png)

Huvud delen av översikts sidan ger en överblick över en överskådlig säkerhets status för din arbets yta:

- **Händelser och aviseringar över tid**: Visar antalet händelser och hur många aviseringar som har skapats från dessa händelser. Om du ser en topp som är ovanlig bör du se aviseringar för IT – om det är något ovanligt att det finns en insamling i händelser, men du inte ser aviseringar, kan det vara en risk.

- **Potentiella skadliga händelser**: När trafik identifieras från källor som är kända för att vara skadliga, varnar Azure Sentinel dig på kartan. Om du ser orange är det inkommande trafik: någon försöker komma åt din organisation från en känd skadlig IP-adress. Om du ser utgående (röd) aktivitet innebär det att data från nätverket strömmas ut från din organisation till en känd skadlig IP-adress.

   ![Azure Sentinel-karta](./media/qs-get-visibility/map.png)


- **Senaste incidenter**: För att visa dina senaste incidenter, allvarlighets grad och antal aviseringar som är associerade med incidenten. Om du ser som plötslig topp i en speciell typ av avisering, kan det betyda att det finns ett aktivt angrepp som körs för tillfället. Om du till exempel har en plötslig topp på 20 pass-The-hash-händelser från Azure ATP, är det möjligt att någon nu försöker attackera dig.

- **Avvikelser för data källa**: Microsofts data analytiker har skapat modeller som kontinuerligt söker igenom data från dina data källor. Om det inte finns några avvikelser visas inget. Om avvikelser upptäcks bör du ta en djup inblick i dem för att se vad som hände. Klicka till exempel på insamling i Azure-aktivitet. Du kan klicka på **diagrammet** för att se när insamling skedde och sedan filtrera efter aktiviteter som inträffade under den tids perioden för att se vad som orsakade insamling.

   ![Azure Sentinel-karta](./media/qs-get-visibility/anomolies.png)

## Använd inbyggda instrument paneler<a name="dashboards"></a>

Inbyggda instrument paneler tillhandahåller integrerade data från dina anslutna data källor så att du kan få en djup inblick i de händelser som genereras i dessa tjänster. Inbyggda instrument paneler inkluderar Azure ID, Azure Activity events och on-premises, som kan vara data från Windows-händelser från servrar, från första part-aviseringar från alla tredje parter, inklusive brand Väggs trafik loggar, Office 365 och oskyddade protokoll baserade på Windows planering.

1. Välj **instrument paneler**under **Inställningar**. Under **installerad**kan du se alla installerade instrument paneler. Under **alla** kan du se hela galleriet med inbyggda instrument paneler som är tillgängliga för installation. 
2. Sök efter en speciell instrument panel för att se hela listan och en beskrivning av vad de erbjuder. 
3. Förutsatt att du använder Azure AD för att komma igång med Azure Sentinel rekommenderar vi att du installerar minst följande instrument paneler:
   - **Azure AD**: Använd antingen eller båda av följande:
       - I **Azure AD-inloggningar** analyseras inloggningar över tid för att se om det finns avvikelser. Den här instrument panelen tillhandahåller misslyckade inloggningar av program, enheter och platser så att du snabbt kan se om något händer. Var uppmärksam på flera misslyckade inloggningar. 
       - **Azure AD audit-loggar** analyserar administrations aktiviteter, till exempel ändringar i användare (Lägg till, ta bort, osv.), grupp skapande och ändringar.  

   - Lägg till en instrument panel för brand väggen. Lägg till exempel till Palo-instrument panelen. Instrument panelen analyserar brand Väggs trafiken och ger dig korrelationer mellan brand Väggs data och hot händelser och markerar misstänkta händelser mellan entiteter. Med instrument paneler får du information om trender i din trafik och du kan öka detalj nivån och filtrera resultaten. 

      ![PAL-instrument panel](./media/qs-get-visibility/palo-alto-week-query.png)


Du kan anpassa instrument panelerna antingen genom att redigera knappen ![](./media/qs-get-visibility/edit-query-button.png)för huvud frågan. Du kan klicka på knappen ![knapp](./media/qs-get-visibility/go-to-la-button.png) för att gå till [Log Analytics för att redigera frågan där](../azure-monitor/log-query/get-started-portal.md), och du kan välja ellipsen (...) och välja **Anpassa panel data**, vilket gör att du kan redigera huvud tids filtret, eller ta bort vissa paneler från instrument panelen.

Mer information om hur du arbetar med frågor finns [i Självstudier: Visuella data i Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Lägg till en ny panel

Om du vill lägga till en ny panel kan du lägga till den i en befintlig instrument panel, antingen en som du skapar eller en inbyggd instrument panel för Azure-kontroll. 
1. I Log Analytics skapar du en panel med hjälp av anvisningarna i [självstudierna: Visuella data i Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. När panelen har skapats väljer du den instrument panel som du vill att panelen ska visas under **PIN-kod**.

## <a name="create-new-dashboards"></a>Skapa nya instrument paneler
Du kan skapa en ny instrument panel från grunden eller använda en inbyggd instrument panel som grund för din nya instrument panel.

1. Om du vill skapa en ny instrument panel från grunden väljer du **instrument paneler** och sedan **+ ny instrument panel**.
2. Välj den prenumeration som instrument panelen skapas i och ge den ett beskrivande namn. Varje instrument panel är en Azure-resurs på samma sätt som andra, och du kan tilldela IT-roller (RBAC) för att definiera och begränsa vem som har åtkomst till den. 
3. Om du vill att den ska visas på dina instrument paneler för att fästa visualiseringar på, måste du dela den. Klicka på **dela** och **Hantera användare**. 
 
1. Använd **kontrol lera åtkomst** -och **roll tilldelningar** på samma sätt som för andra Azure-resurser. Mer information finns i [dela Azure-instrumentpaneler med hjälp av RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Nya exempel på instrument paneler

I följande exempel fråga kan du jämföra trender för trafik över flera veckor. Du kan enkelt byta enhets leverantör och data källa som du kör frågan på. I det här exemplet används SecurityEvent från Windows, du kan växla den till att köras på AzureActivity eller CommonSecurityLog på någon annan brand vägg.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Du kanske vill skapa en fråga som införlivar data från flera källor. Du kan skapa en fråga som tittar på Azure Active Directory gransknings loggar för nya användare som precis har skapats och sedan kontrollerar dina Azure-loggar för att se om användaren har börjat göra ändringar i roll tilldelningen inom 24 timmar från skapandet. Den misstänkta aktiviteten visas på den här instrument panelen:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Du kan skapa olika instrument paneler baserat på roll för person som tittar på data och vad de letar efter. Du kan till exempel skapa en instrument panel för nätverks administratören som innehåller brand Väggs data. Du kan också skapa instrument paneler baserat på hur ofta du vill titta på dem, om det finns saker som du vill granska varje dag och andra objekt som du vill kontrol lera en gång i timmen, till exempel kanske du vill titta på dina Azure AD-inloggningar varje timme för att söka efter avvikelser ES. 

## <a name="create-new-detections"></a>Skapa nya identifieringar

Generera identifieringar av [data källor som du har anslutit till Azure Sentinel](connect-data-sources.md) för att undersöka hot i din organisation.

När du skapar en ny identifiering kan du utnyttja de inbyggda identifieringarna som är utformade av Microsofts säkerhets forskare som är skräddarsydda för de data källor som du har anslutit.

1. [I GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) -communityn går du till identifierings-mappen och väljer relevanta mappar.
   ![relevanta mappar](./media/qs-get-visibility/detection-folders.png)
 
3.  Gå till fliken **analys** och välj **Lägg till**.
   ![Skapa regel i Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Kopiera alla parametrar till regeln och klicka på **skapa**.
   ![Skapa aviserings regel](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du lärt dig hur du kommer igång med Azure Sentinel. Fortsätt till självstudien för [att identifiera hot](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Identifiera hot](tutorial-detect-threats.md) för att automatisera dina svar på hot.

