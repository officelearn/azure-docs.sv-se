---
title: IT Service Management-anslutningstjänsten i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en översikt av IT Service Management koppling (ITSMC) och information om hur du använder den här lösningen centralt övervaka och hantera ITSM arbetsobjekt i Azure Log Analytics och att snabbt lösa eventuella problem.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: v-jysur
ms.openlocfilehash: da81d1455649f1e3f3ab43016df49953ce90e0ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637624"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Anslut Azure ITSM verktyg med IT Service Management-anslutningstjänsten

![IT Service Management-anslutningstjänsten symbol](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management koppling (ITSMC) kan du ansluta Azure och en stöds IT Service Management (ITSM) produkter eller tjänster.

Azure-tjänster som logganalys och Azure-Monitor innehåller verktyg för att identifiera, analysera och felsöka problem med din Azure och Azure-resurser. Arbetsobjekt som är relaterade till ett problem som vanligtvis finns dock i en ITSM produkter eller tjänster. ITSM kopplingen ger en dubbelriktad anslutning mellan Azure och ITSM verktyg som hjälper dig att lösa problem snabbare.

ITSMC stöder anslutningar med följande ITSM verktyg:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Med ITSMC kan du

-  Skapa arbetsobjekt i ITSM verktyg, baserat på dina Azure aviseringar (mått aviseringar, aktivitetsloggen aviseringar och logganalys aviseringar).
-  Du kan också synkronisera din incident och ändra data för begäran från ITSM-verktyget till en Azure logganalys-arbetsyta.


Du kan börja använda ITSM kopplingen med följande steg:

1.  [Lägg till ITSM Connector lösning](#adding-the-it-service-management-connector-solution)
2.  [Skapa en ITSM-anslutning](#creating-an-itsm-connection)
3.  [Använd anslutning](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Lägga till IT-tjänst lösning för hantering av koppling

Du måste lägga till ITSM Connector lösningen innan du kan skapa en anslutning.

1.  I Azure-portalen klickar du på **+ ny** ikon.

    ![Azure ny resurs](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Sök efter **IT Service Management-anslutningstjänsten** i Marketplace och klicka på **skapa**.

    ![Lägg till ITSMC lösning](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  I den **OMS-arbetsytan** väljer Azure logganalys-arbetsyta där du vill installera lösningen.
4.  I den **OMS arbetsyteinställningarna** väljer ResourceGroup där du vill skapa resurs för lösningen.

    ![ITSMC arbetsytan](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Klicka på **Skapa**.

När resursen lösningen har distribuerats, visas ett meddelande längst upp höger i fönstret.


## <a name="creating-an-itsm--connection"></a>Skapa en ITSM-anslutning

När du har installerat lösningen, kan du skapa en anslutning.

För att skapa en anslutning, behöver du Förbered dig ITSM-verktyget så att anslutningen från ITSM Connector-lösning.  

Beroende på hur du ansluter till ITSM produkten, använder du följande steg:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

När du har prepped ITSM-verktyg, följer du stegen nedan för att skapa en anslutning:

1.  Gå till **alla resurser**, leta efter **ServiceDesk(YourWorkspaceName)**.
2.  Under **ARBETSYTAN DATAKÄLLOR** i den vänstra rutan klickar du på **ITSM anslutningar**.
    ![ITSM anslutningar](./media/log-analytics-itsmc/itsm-connections.png)

    Den här sidan visas i listan över anslutningar.
3.  Klicka på **Lägg till anslutning**.

    ![Lägg till ITSM anslutning](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Ange vilka inställningar som beskrivs i [konfigurera ITSMC anslutningen med ITSM produkter och tjänster artikeln](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Som standard uppdaterar ITSMC anslutningens konfigurationsdata en gång i en gång per dygn. Uppdatera din anslutning data direkt eventuella ändringar eller mall för programuppdateringar som du gör, klicka på den **Sync** knappen på bladet för din anslutning.

    ![Uppdatera anslutning](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Använda lösningen
   Du kan skapa arbetsobjekt från Azure aviseringar, logganalys aviseringar och logganalys loggposter med hjälp av ITSM Connector-lösning.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbetsobjekt från Azure-aviseringar

När du har skapat ITSM anslutningen kan du skapa objekt för arbete i din ITSM verktyget baserat på Azure aviseringar med hjälp av den **ITSM åtgärd** i **åtgärdsgrupper**.

Åtgärdsgrupper är en modulära och återanvändbara sätt utlöser åtgärder för din Azure-aviseringar. Du kan använda åtgärdsgrupper med mått aviseringar, aktivitetsloggen aviseringar och Azure logganalys aviseringar i Azure-portalen.

Följ dessa steg:

1. I Azure-portalen klickar du på **övervakaren**.
2. I den vänstra rutan klickar du på **åtgärdsgrupper**. Den **Lägg till grupp** visas.

    ![Åtgärdsgrupper](media/log-analytics-itsmc/action-groups.png)

3. Ange **namn** och **kort filnamn** för din grupp. Välj den **resursgruppen** och **prenumeration** där du vill skapa din grupp.

    ![Åtgärden grupper detaljer](media/log-analytics-itsmc/action-groups-details.png)

4. Välj i listan åtgärder **ITSM** från den nedrullningsbara menyn för **åtgärdstyp**. Ange en **namn** för åtgärd och klickar på **redigera detaljer**.
5. Välj den **prenumeration** där logganalys-arbetsytan finns. Välj den **anslutning** namn (ITSM Connector namn) följt av namnet på din arbetsyta. Till exempel ”MyITSMMConnector(MyWorkspace)”.

    ![ITSM åtgärdsinformation](./media/log-analytics-itsmc/itsm-action-details.png)

6. Välj **arbetsobjekt** typen från den nedrullningsbara menyn.
   Välja att använda en befintlig mall eller Fyll i fälten som krävs av ITSM produkten.
7. Klicka på **OK**.

När du skapar/redigerar en Azure aviseringsregel, använda en grupp, som har en ITSM-åtgärd. När aviseringen utlöser är arbetsobjekt skapat/uppdaterat i verktyget ITSM.

>[!NOTE]

> Information om priser för ITSM åtgärden finns i [sida med priser](https://azure.microsoft.com/pricing/details/monitor/) för åtgärdsgrupper.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenten och ändra data för begäran

Baserat på konfigurationen när du konfigurerar en anslutning, kan ITSM connector synkronisera upp till 120 dagar Incident och ändrar data på begäran. Loggen post schemat för dessa data finns i den [nästa avsnitt](#additional-information).

Begärandedata incident och ändrar visualiseras med hjälp av instrumentpanelen ITSM Connector i lösningen.

![Log Analytics skärmen](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Instrumentpanelen innehåller även information om connector-status som kan användas som utgångspunkt för att analysera problem med anslutningar.

Du kan också visualisera incidenter som har synkroniserats mot datorer som påverkas, i Tjänstkartan-lösning.

Tjänstkarta identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster automatiskt. Det gör att du kan visa dina servrar som du betrakta dem – som sammanlänkade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar över en TCP-ansluten arkitektur med ingen konfiguration krävs för andra än installation av en agent. [Läs mer](../operations-management-suite/operations-management-suite-service-map.md).

Om du använder Tjänstkarta lösning kan du visa tjänsten supportavdelningen objekt som skapats i ITSM lösningar som visas i följande exempel:

![Log Analytics skärmen](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

Mer information: [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>Ytterligare information

### <a name="data-synced-from-itsm-product"></a>Data som synkroniseras från ITSM produkten
Incidenter och ändringsförfrågningar synkroniseras från din ITSM produkt till logganalys-arbetsytan baserat på anslutningens konfiguration.

Följande information visas exempel på data som samlas in av ITSMC:

> [!NOTE]

> Beroende på typ av arbetsobjekt som har importerats till Log Analytics **ServiceDesk_CL** innehåller följande fält:

**Arbetsobjekt:** **incidenter**  
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
- Titel
- Beskrivning
- Skapad
- Stängningsdatum
- Lösningsdatum
- Ändrades senast datum
- Dator


**Arbetsobjekt:** **ändringsbegäranden**

ServiceDeskWorkItemType_s = ”ändra begäran”

**Fält**
- ServiceDeskConnectionName
- Service Desk-ID
- Skapad av
- Stängts av
- Källa
- Tilldelat till
- Titel
- Typ
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
- Begärt datum
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
| Source_s| Kontakta typ |
| AssignedTo_s | Tilldelat till  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Anteckningar |
| CreatedDate_t|  Öppnat |
| ClosedDate_t| Stängd|
| ResolvedDate_t|Löst|
| Dator  | konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow ändringsbegäran

| Log Analytics | ServieNow fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| CreatedBy_s | Efterfrågad av |
| ClosedBy_s | Stängts av |
| AssignedTo_s | Tilldelat till  |
| Title_s|  Kort beskrivning |
| Type_s|  Typ |
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


## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM anslutningar
1.  Om anslutningen misslyckas från anslutna datakällan gränssnitt med en **fel spara anslutningen** visas, gör du följande:
 - För ServiceNow, Cherwell och Provance-anslutningar  
    - Se till att du korrekt angav användarnamn, lösenord, klient-ID och klienthemlighet för anslutning.  
    - Kontrollera att du har tillräckliga privilegier i motsvarande ITSM produkten för att ansluta.  
 - För Service Manager-anslutningar  
    - Kontrollera att webbappen har distribuerats och hybridanslutningen har skapats. För att verifiera anslutningen upprättas har med Service Manager-datorn lokalt, gå Webbappens URL som anges i dokumentationen för att göra den [hybridanslutning](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Om du inte komma har synkroniserats data från ServiceNow till Log Analytics, se till att ServiceNow instansen inte är i viloläge. ServiceNow Dev instanser försättas ibland i viloläge vid inaktivitet under lång tid. Annars rapportera problemet.
3.  Om OMS aviseringar eller men fungerar objekt skapas inte i ITSM produkten eller konfigurationsobjekt inte skapats/kopplad till arbetsobjekt eller allmän information, se på följande platser:
 -  ITSMC: Lösningen innehåller en översikt över anslutningar/artiklar/arbetsdatorer osv. Klicka på panelen visar **Connector-Status**, som tar du **loggen Sök** med relevanta frågan. Titta på loggposter med LogType_S som fel för mer information.
 - **Logga Sök** sida: Visa fel/relaterad information direkt med frågan `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution
1.  Se till att du har tillräckliga behörigheter i prenumerationen som anges för att skapa/distribuera resurser vid eventuella problem med web app-distribution.
2.  Om du får en **”objekt referensen inte anges till instans av ett objekt”** fel när du kör den [skriptet](log-analytics-itsmc-service-manager-script.md), se till att du har angett giltiga värden under **Användarkonfiguration** avsnitt .
3.  Om du inte skapa service bus relay-namnrymd, kontrollera att nödvändiga resursprovidern har registrerats i prenumerationen. Om inte registrerad, måste du manuellt skapa namnområde för service bus relay i Azure Portal. Du kan också skapa den när [hybrid anslutningen](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) från Azure-portalen.


## <a name="contact-us"></a>Kontakta oss

För alla frågor eller feedback om IT Service Management-anslutningstjänsten, kontaktar du oss på [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg
[Lägga till ITSM produkter och tjänster till IT Service Management-anslutningstjänsten](log-analytics-itsmc-connections.md).
