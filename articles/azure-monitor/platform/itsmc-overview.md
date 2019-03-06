---
title: IT Service Management Connector i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en översikt av IT Service Management Connector (ITSMC) och information om hur du använder den här lösningen kan du centralt övervaka och hantera ITSM-arbetsobjekt i Azure Log Analytics och lös eventuella problem snabbt.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: a6e4f816d3c5be9d3a4f4824ad7ed26d48763034
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433281"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Ansluta Azure till ITSM-verktyg som använder IT Service Management Connector

![IT Service Management Connector symbol](media/itsmc-overview/itsmc-symbol.png)

IT Service Management Connector (ITSMC) kan du ansluta Azure och en stöds IT Service Management (ITSM) produkt/tjänst.

Azure-tjänster som Log Analytics och Azure Monitor innehåller verktyg för att identifiera, analysera och felsöka problem med Azure- och icke-Azure-resurser. Arbetsobjekt som är relaterade till ett problem som vanligtvis finns dock i ett ITSM-produkt/tjänst. ITSM-anslutningsprogram tillhandahåller en dubbelriktad anslutning mellan Azure och ITSM-verktyg som hjälper dig att lösa problem snabbare.

ITSMC stöder anslutningar med följande ITSM-verktyg:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Med ITSMC kan du

-  Skapa arbetsobjekt i ITSM-verktyg, baserat på din Azure-aviseringar (måttaviseringar, aktivitetslogg aviseringar och Log Analytics-aviseringar).
-  Du kan också kan du synkronisera din incident och ändra data för programbegäranden från ITSM-verktyg till en Azure Log Analytics-arbetsyta.


Du kan börja använda ITSM-anslutningsprogrammet med följande steg:

1.  [Lägg till lösning för ITSM-anslutningen](#adding-the-it-service-management-connector-solution)
2.  Skapa en ITSM-anslutning
3.  [Använd anslutning](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Att lägga till IT Service Management Connector-lösningen

Innan du kan skapa en anslutning, måste du lägga till lösning för ITSM-anslutningen.

1.  I Azure-portalen klickar du på **+ ny** ikon.

    ![Nya Azure-resurs](media/itsmc-overview/azure-add-new-resource.png)

2.  Sök efter **IT Service Management Connector** i Marketplace och på **skapa**.

    ![Lägg till ITSMC lösning](media/itsmc-overview/add-itsmc-solution.png)

3.  I den **OMS-arbetsyta** väljer Azure Log Analytics-arbetsytan där du vill installera lösningen.
   >[!NOTE]
   >Som en del av pågående övergången från Microsoft Operations Management Suite (OMS) till Azure Monitor, är OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.
4.  I den **inställningar för OMS-arbetsyta** väljer du den resursgrupp där du vill skapa resursen lösning.

    ![ITSMC arbetsyta](media/itsmc-overview/itsmc-solution-workspace.png)
    >[!NOTE]
    >Som en del av pågående övergången från Microsoft Operations Management Suite (OMS) till Azure Monitor, är OMS-arbetsytor kallas nu för Log Analytics-arbetsytor.

5.  Klicka på **Skapa**.

När resursen lösningen har distribuerats kan det visas ett meddelande längst upp höger i fönstret.


## <a name="creating-an-itsm--connection"></a>Skapa en ITSM-anslutning

När du har installerat lösningen kan skapa du en anslutning.

För att skapa en anslutning, kommer du behöva Förbered ITSM-verktyg för att kunna ansluta från ITSM-anslutningsprogram lösningen.  

Beroende på ITSM-produkten som du ansluter till, använder du följande steg:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

När du har förberedd ITSM-verktygen, följer du stegen nedan för att skapa en anslutning:

1.  Gå till **alla resurser**, leta efter **ServiceDesk(YourWorkspaceName)**.
2.  Under **DATAKÄLLOR för ARBETSYTA** i den vänstra rutan klickar du på **ITSM-anslutningar**.
    ![ITSM-anslutningar](media/itsmc-overview/itsm-connections.png)

    Den här sidan visar listan över anslutningar.
3.  Klicka på **Lägg till anslutning**.

    ![Lägga till ITSM-anslutningen](media/itsmc-overview/add-new-itsm-connection.png)

4.  Ange anslutningsinställningarna enligt beskrivningen i [konfigurera ITSMC anslutningen med din artikel för ITSM-produkter/tjänster](../../azure-monitor/platform/itsmc-connections.md).

    > [!NOTE]

    > Som standard uppdaterar ITSMC anslutningens configuration-data en gång under var 24: e timme. Om du vill uppdatera din anslutning data direkt den redigeringar eller en mall för programuppdateringar som du gör, klickar du på den **synkronisering** knappen på bladet för din anslutning.

    ![Uppdatera anslutning](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Använda lösningen
   Du kan skapa arbetsuppgifter från Azure-aviseringar, Log Analytics-aviseringar och loggposter för Log Analytics med hjälp av lösningen ITSM-anslutningsprogram.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM-arbetsuppgifter från Azure-aviseringar

När du har skapat ITSM-anslutningen kan du skapa work objekt i dina ITSM-verktyg baserade på Azure-aviseringar med hjälp av den **ITSM-åtgärden** i **åtgärdsgrupper**.

Åtgärdsgrupper är ett modulära och återanvändbara sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärdsgrupper med måttaviseringar, aktivitetslogg aviseringar och Azure Log Analytics-aviseringar i Azure-portalen.

Följ dessa steg:

1. I Azure-portalen klickar du på **övervakaren**.
2. I den vänstra rutan klickar du på **åtgärdsgrupper**. Den **Lägg till åtgärdsgrupp** fönster visas.

    ![Åtgärdsgrupper](media/itsmc-overview/action-groups.png)

3. Ange **namn** och **ShortName** för din åtgärdsgruppen. Välj den **resursgrupp** och **prenumeration** där du vill skapa din åtgärdsgrupp.

    ![Information om åtgärd-grupper](media/itsmc-overview/action-groups-details.png)

4. Välj i listan åtgärder **ITSM** från den nedrullningsbara menyn för **åtgärdstyp**. Ange en **namn** för åtgärden och klicka på **redigera information**.
5. Välj den **prenumeration** där Log Analytics-arbetsytan finns. Välj den **anslutning** namn (ITSM-anslutningsprogram namn) följt av namnet på din arbetsyta. Till exempel ”MyITSMMConnector(MyWorkspace)”.

    ![Information om ITSM-åtgärd](media/itsmc-overview/itsm-action-details.png)

6. Välj **arbetsobjekt** typen från den nedrullningsbara menyn.
   Välja att använda en befintlig mall eller Fyll i fälten som krävs av ITSM-produkten.
7. Klicka på **OK**.

När du skapar/redigerar en Azure varningsregel, använder du en åtgärdsgrupp som har en ITSM-åtgärd. När en avisering utlöses arbetsobjekt som har skapats/uppdaterats i ITSM-verktyget.

>[!NOTE]

> Mer information om priser för ITSM-åtgärden finns i den [prissättningssidan](https://azure.microsoft.com/pricing/details/monitor/) för åtgärdsgrupper.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenten och ändra data för programbegäranden

Utifrån din konfiguration när du konfigurerar en anslutning, kan ITSM-anslutningsprogrammet synkronisera upp till 120 dagar Incident och ändrar data på begäran. Log postschema för dessa data finns i den [nästa avsnitt](#additional-information).

Frågeinformationen incident och ändringsförfråga kan visualiseras med hjälp av instrumentpanelen ITSM-anslutningsprogram i lösningen.

![Log Analytics-skärmen](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrumentpanelen innehåller även information om status för anslutningsprogrammet som kan användas som utgångspunkt för att analysera problem med anslutningar.

Du kan också visualisera de incidenter som har synkroniserats mot de berörda datorerna, i lösningen Tjänstkarta.

Tjänstkarta automatiskt identifierar programkomponenter i Windows och Linux-system och mappar kommunikationen mellan olika tjänster. Det kan du visa dina servrar som du tänker på dem – sammankopplat system som levererar viktiga tjänster. Service Map ser du anslutningarna mellan servrar, processer och portar i alla TCP-anslutna arkitekturer utan konfiguration som behövs installation av en agent. [Läs mer](../../azure-monitor/insights/service-map.md).

Om du använder lösningen Tjänstkarta, kan du visa service desk-objekt som skapats i ITSM-lösningar som du ser i följande exempel:

![Log Analytics-skärmen](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mer information: [Tjänstkarta](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Ytterligare information

### <a name="data-synced-from-itsm-product"></a>Data som synkroniseras från ITSM-produkten
Incidenter och ändringsförfrågningar synkroniseras från ITSM-produkten till Log Analytics-arbetsytan baserat på konfigurationen av den anslutningen.

Informationen nedan visar exempel på data som samlas in av ITSMC:

> [!NOTE]

> Beroende på typ av arbetsobjekt som importeras till Log Analytics, **ServiceDesk_CL** innehåller följande fält:

**Arbetsobjekt:** **Incidenter**  
ServiceDeskWorkItemType_s="Incident"

**Fält**

- ServiceDeskConnectionName
- Service Desk-ID
- Status
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapad av
- Löst av
- Stängts av
- Källa
- Tilldelat till
- Kategori
- Rubrik
- Beskrivning
- Skapad
- Stängningsdatum
- Lösningsdatum
- Ändrades senast datum
- Dator


**Arbetsobjekt:** **Tjänstbegäranden**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fält**
- ServiceDeskConnectionName
- Service Desk-ID
- Skapad av
- Stängts av
- Källa
- Tilldelat till
- Rubrik
- Type
- Kategori
- Status
- Eskalering
- Konflikt Status
- Angelägenhetsgrad
- Prioritet
- Risk
- Påverkan
- Tilldelat till
- Skapad
- Stängningsdatum
- Ändrades senast datum
- Begärda datum
- Planerat startdatum
- Planerat slutdatum
- Startdatum för arbete
- Slutdatum för arbete
- Beskrivning
- Dator

## <a name="output-data-for-a-servicenow-incident"></a>Utdata för en ServiceNow-incident

| Log Analytics-fält | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| IncidentState_s | Status |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnas av |
| ResolvedBy_s | Matchad av|
| ClosedBy_s  | Stängts av |
| Source_s| Kontakttyp |
| AssignedTo_s | Tilldelat till  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Anteckningar |
| CreatedDate_t|  Öppnat |
| ClosedDate_t| Stängd|
| ResolvedDate_t|Löst|
| Dator  | Konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow-ändringsbegäran

| Log Analytics | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| CreatedBy_s | Efterfrågad av |
| ClosedBy_s | Stängts av |
| AssignedTo_s | Tilldelat till  |
| Title_s|  Kort beskrivning |
| Type_s|  Type |
| Category_s|  Kategori |
| CRState_s|  Status|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärt efter datum |
| ClosedDate_t | Stängningsdatum |
| PlannedStartDate_t  |     Planerat startdatum |
| PlannedEndDate_t  |   Planerat slutdatum |
| WorkStartDate_t  | Verkligt startdatum |
| WorkEndDate_t | Verkligt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurationsobjekt |


## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar
1.  Om anslutningen misslyckas från ansluten källa Användargränssnittet med en **fel när anslutning sparades** visas, gör följande:
 - För ServiceNow, Cherwell och Provance anslutningar  
    - Se till att du korrekt angett användarnamn, lösenord, klient-ID och klienthemlighet för var och en av anslutningarna.  
    - Kontrollera om du har tillräcklig behörighet i motsvarande ITSM-produkten för att upprätta anslutningen.  
 - För Service Manager-anslutningar  
    - Kontrollera att webbappen har distribuerats och hybridanslutning har skapats. Kontrollera anslutningen är upprättad med den på plats Service Manager-datorn genom att gå till Webbappens URL som beskrivs i dokumentationen för att göra den [hybridanslutning](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2.  Om du inte komma har synkroniserats data från ServiceNow till Log Analytics, se till att ServiceNow instans inte är i viloläge. ServiceNow Dev instanser går ibland i viloläge när det är inaktivt under en lång period. Annars rapportera problemet.
3.  Om Log Analytics-aviseringar utlöses men fungerar objekt skapas inte i ITSM-produkten eller konfigurationsobjekt är inte skapat/kopplad till arbetsobjekt eller allmän information, se ut på följande platser:
 -  ITSMC: Lösningen visar en sammanfattning av anslutningar/objekt/arbetsdatorer osv. Klicka på panelen visar **Connector-Status**, som tar dig till **Loggsökning** med relevanta fråga. Titta på loggposter med LogType_S som fel för mer information.
 - **Loggsöknings-** sida: Visa fel/relaterade informationen direkt med hjälp av frågan `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution
1.  Vid problem med distribution av webbappar, se till att du har tillräckliga behörigheter i prenumerationen som nämns skapa/distribuera resurser.
2.  Om du får en **”objektreferens har inte anges till en instans av ett objekt”** fel när du kör den [skriptet](itsmc-service-manager-script.md), se till att du har angett giltiga värden under **Användarkonfiguration** avsnittet .
3.  Om du inte skapa service bus relay-namnområde, kontrollerar du att nödvändiga resursprovidern har registrerats i prenumerationen. Om du inte är registrerad måste du manuellt skapa service bus relay-namnområde i Azure Portal. Du kan också skapa den samtidigt [skapade hybridanslutningen](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) från Azure-portalen.


## <a name="contact-us"></a>Kontakta oss

För alla frågor eller feedback om IT Service Management Connector, kontaktar du oss på [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg
[Lägga till ITSM-produkter/tjänster till IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md).
