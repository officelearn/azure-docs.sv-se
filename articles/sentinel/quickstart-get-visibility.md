---
title: Azure Sentinel-Snabbstart – Kom igång med Azure Sentinel-förhandsversionen | Microsoft Docs
description: Azure Sentinel-Snabbstart – Kom igång med Azure Sentinel
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: a80c4db1b81dd2bc0b223a2781e28ccb1f5ba68e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805748"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Snabbstart: Kom igång med Azure Sentinel-förhandsversionen

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


I den här snabbstarten lär du dig att snabbt kunna visa och övervaka vad som händer i din miljö med Azure Sentinel. När du har anslutit dina datakällor till Azure Sentinel, får du omedelbar visualisering och analys av data så att du vet vad som händer i alla dina anslutna datakällor. Azure Sentinel får du instrumentpaneler som ger dig alla fördelar med verktyg som redan finns i Azure samt tabeller och diagram som är inbyggda i tillhandahåller analyser för dina loggar och frågor. Du kan använda inbyggda instrumentpaneler, eller så kan du skapa en ny instrumentpanel enkelt, från början eller baserat på en befintlig instrumentpanel. 

## <a name="get-visualization"></a>Hämta visualisering

Om du vill visualisera och få analys av vad som händer på din miljö, först ta en titt på Översikt över instrumentpanelen för att få en uppfattning om säkerhetspositionen för din organisation. Du kan klicka på varje element i dessa paneler för att öka detaljnivån till de rådata som de har skapats. Hjälper dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka, används Sentinel-Azure en fusion teknik kan korrelera aviseringar till fall. **Fall** är grupper av relaterade varningar som tillsammans skapar en användbara incident som du kan undersöka och lösa.

- Välj Sentinel-Azure i Azure-portalen och välj sedan den arbetsyta som du vill övervaka.

  ![Översikt över Azure Sentinel](./media/qs-get-visibility/overview.png)

- I verktygsfältet högst upp får du hur många händelser som du har fått genom den valda tidsperioden och det jämförs med föregående 24 timmar. Verktygsfältet berättas från dessa händelser, de aviseringar som har utlösts (små tal representerar ändringen under de senaste 24 timmarna) och meddelar du för de händelserna som hur många är öppna, pågår och stängda. Kontrollera att se att det inte finns en dramatisk ökning eller ta bort i antalet händelser. Om det finns en minskning kan bero det på att en anslutning har stoppats för att rapportera till Sentinel-Azure. Om det finns en ökning något misstänkt kan ha hänt. Kontrollera om du har nya aviseringar.

   ![Azure Sentinel tratt](./media/qs-get-visibility/funnel.png)

Brödtext på översiktssidan ger insikt i korthet i säkerhetsstatusen för din arbetsyta:

- **Händelser och varningar över tid**: Visar antalet händelser och hur många aviseringar som skapades från dessa händelser. Om du ser en topp är ovanlig, borde du se aviseringar för det – om det inte finns något annorlunda där det finns en topp i händelser men du inte ser aviseringar, kan det vara orsaken till problemet.

- **Potentiellt skadliga händelser**: När trafik identifieras från källor som har visat sig vara skadlig varnar Sentinel-Azure dig på kartan. Om du ser en orange, är det inkommande trafik: någon försöker få åtkomst till organisationen från en känd skadlig IP-adress. Om du ser utgående (röd) aktivitet, betyder det att data från ditt nätverk strömmas utanför din organisation till en känd skadlig IP-adress.

   ![Azure Sentinel-karta](./media/qs-get-visibility/map.png)


- **Senaste fall**: Visa senaste ärenden, deras allvarlighetsgrad och antalet aviseringar som är associerade med fallet. Om du ser som plötsliga topp i en viss typ av avisering kan det betyda att det finns ett pågående angrepp som för närvarande körs. Exempelvis är det möjligt att någon för närvarande försöker attackera du om du har en stor topp 20 Pass-the-hash-händelser från Azure ATP.

- **Datakällans avvikelser**: Microsofts dataanalytiker skapa modeller som ständigt söka efter data från dina datakällor efter avvikelser. Om det inte finns några avvikelser, inget att visas. Om avvikelserna identifieras, bör du djupgående genomgång av att se vad som hände. Klicka exempelvis på topp i Azure-aktivitet. Du kan klicka på **diagram** att se när insamling inträffade och sedan filtrera efter aktiviteter som inträffat under tidsperioden att se vad som orsakade insamling.

   ![Azure Sentinel-karta](./media/qs-get-visibility/anomolies.png)

## Använd inbyggda instrumentpaneler<a name="dashboards"></a>

Inbyggda instrumentpaneler tillhandahåller integrerad data från dina anslutna datakällor så att du bättre fördjupa dig i de händelser som genereras i dessa tjänster. Instrumentpaneler för inbyggda innehåller ID för Azure, Azure aktivitetshändelser och på plats, vilket kan vara data från Windows-händelser från servrar, från första part aviseringar från tredje part, inklusive loggar från brandväggen för trafiken, Office 365 och osäkra protokoll baserat på Windows händelser.

1. Under **inställningar**väljer **instrumentpaneler**. Under **installerad**, du kan se alla installerade instrumentpaneler. Under **alla** visas helt galleri med inbyggda instrumentpaneler som är tillgängliga för installation. 
2. Sök efter en specifik instrumentpanel för att se hela listan och beskrivning av vad varje erbjuder. 
3. Förutsatt att du använder Azure AD för att komma igång och körs med Azure Sentinel, rekommenderar vi att du installerar minst följande instrumentpaneler:
   - **Azure AD**: Använd ett eller båda av följande:
       - **Azure AD-inloggningar** analyserar inloggningar över tid för att se om det finns avvikelser. Den här instrumentpanelen innehåller misslyckade inloggningar med program, enheter och platser så att du kan se, på ett ögonblick om något annorlunda sker. Ta hänsyn till flera misslyckade inloggningar. 
       - **Azure AD-granskningsloggar** analyserar administratörsaktiviteter, till exempel ändringar i användare (lägga till, ta bort osv.), skapa en grupp och ändringar.  

   - Lägg till en instrumentpanel för brandväggen. Lägg exempelvis till Palo Alto-instrumentpanelen. Instrumentpanelen analyserar din brandvägg-trafik, att ge dig korrelationer mellan dina data och threat brandväggshändelser, och visar misstänkta händelser mellan entiteter. Instrumentpaneler ger dig information om trender i trafiken och kan du öka detaljnivån och filtrera resultaten. 

      ![PAL Alto instrumentpanel](./media/qs-get-visibility/palo-alto-week-query.png)


Du kan anpassa instrumentpanelerna genom att redigera huvudfrågan ![knappen](./media/qs-get-visibility/edit-query-button.png). Du kan klicka på knappen ![knappen](./media/qs-get-visibility/go-to-la-button.png) att gå till [Log Analytics för att redigera frågan det](../azure-monitor/log-query/get-started-portal.md), och du kan välja ellipsen (...) och välja **anpassa paneldata**, vilket gör att du Redigera huvudsakliga tidsfiltret eller ta bort specifika panelerna på instrumentpanelen.

Mer information om hur du arbetar med frågor finns i [självstudien: Visuella data i Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Lägg till en ny panel

Om du vill lägga till en ny panel kan du lägga till den till en befintlig instrumentpanel, en som du skapar eller en inbyggd Sentinel-Azure-instrumentpanel. 
1. I Log Analytics, skapar du en panel med hjälp av instruktionerna i [självstudien: Visuella data i Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. När panelen har skapats under **PIN-kod**, markera den instrumentpanel som du vill att panelen visas.

## <a name="create-new-dashboards"></a>Skapa nya instrumentpaneler
Du kan skapa en ny instrumentpanel från grunden eller använda en inbyggd instrumentpanel som bas för din nya instrumentpanel.

1. Om du vill skapa en ny instrumentpanel från grunden, Välj **instrumentpaneler** och sedan **+ ny instrumentpanel**.
2. Välj den prenumeration som instrumentpanelen har skapats i och ge den ett beskrivande namn. Varje instrumentpanel är en Azure-resurs som med andra och du kan tilldela roller (RBAC) för att definiera och ange vem som kan komma åt. 
3. Du måste dela den om du vill aktivera så att den visas i instrumentpanelen för att fästa visualiseringar på. Klicka på **resursen** och sedan **hantera användare**. 
 
1. Använd den **Kontrollera åtkomst** och **rolltilldelningar** precis som för andra Azure-resurser. Mer information finns i [dela Azure-instrumentpaneler med hjälp av RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Nya instrumentpanelen-exempel

Följande exempelfråga kan du jämföra trender av trafik över veckor. Du kan enkelt växla vilka leverantören av enheten och en datakälla som du kör frågan på. Det här exemplet använder SecurityEvent från Windows, kan du växla den kan köras på AzureActivity eller CommonSecurityLog på någon annan brandvägg.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Du kanske vill skapa en fråga som innehåller data från flera källor. Du kan skapa en fråga som tittar på Azure Active Directory-granskningsloggarna för nya användare som bara har skapats och kontroller som din Azure loggar för att se om användaren har börjat göra rollen tilldelningsändringarna inom 24 timmar efter skapandet. Den misstänkta aktiviteten skulle visas på den här instrumentpanelen:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Du kan skapa olika instrumentpaneler som baseras på roll person som tittar på data och vad de letar efter. Exempelvis kan du skapa en instrumentpanel för nätverket administratören som innehåller data för brandväggen. Du kan också skapa instrumentpaneler som baseras på hur ofta du vill titta på dem, oavsett om det finns saker som du vill gå igenom varje dag och andra objekt som du vill kontrollera en gång i timmen, till exempel du kanske vill titta på din Azure AD-inloggningar varje timme för att söka efter anomali ES. 

## <a name="create-new-detections"></a>Skapa nya identifieringar

Generera identifieringar på den [datakällor som du är ansluten till Azure Sentinel](connect-data-sources.md) att undersöka hot i din organisation.

När du skapar en ny identifiering kan utnyttja inbyggda identifieringar särskild genom Microsofts säkerhetsforskare som är specialanpassade för datakällor som du är ansluten.

1. [I GitHub-gemenskapen](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) går du till den **identifieringar** mapp och Välj relevanta mappar.
   ![relevanta mappar](./media/qs-get-visibility/detection-folders.png)
 
3.  Gå till den **Analytics** fliken och markera **lägga till**.
   ![Skapa regel i Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Kopiera alla parametrar för regeln och klicka på **skapa**.
   ![Skapa aviseringsregel](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Nästa steg
I den här snabbstarten lärde du dig att komma igång med Azure Sentinel. Fortsätta till självstudien för [så identifierar hot](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Identifiera hot](tutorial-detect-threats.md) att automatisera dina svar på hot.

