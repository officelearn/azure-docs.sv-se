---
title: "IT Service Management-anslutningstjänsten i Azure Log Analytics | Microsoft Docs"
description: "Använd IT Service Management-anslutningstjänsten centralt övervaka och hantera ITSM arbetsobjekt i Azure Log Analytics och att snabbt lösa eventuella problem."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: f60586dcd09148d916bafdde21cc038f57ed9287
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Centralt hantera ITSM arbetsobjekt med hjälp av IT Service Management-anslutningstjänsten (förhandsgranskning)

![IT Service Management-anslutningstjänsten symbol](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management-anslutningstjänsten innehåller en dubbelriktad integrering mellan en IT Service Management (ITSM) produkter eller tjänster och logganalys som stöds.  Via den här anslutningen kan du skapa incidenter, aviseringar och händelser i ITSM produkten baserat på Log Analytics aviseringar eller loggposter. Kopplingen också importerar data som incidenter och tjänstbegäranden från produkt-ITSM i OMS logganalys.

Med IT Service Management-anslutningstjänsten kan du:

  - Integrera operativa aviseringar med din praxis för incidenthantering i ITSM verktyg som helst.
    - Skapa arbetsobjekt (till exempel avisering, händelse, incident) i ITSM från OMS-varningar och via loggen sökning.
    - Skapa arbetsobjekt baserat på ditt Azure-aktivitetsloggen aviseringar via ITSM åtgärd i åtgärdsgrupper.

  - Förena övervakning, loggen och tjänsten hantering av data som används inom organisationen.
    - Korrelera incident och ändra data för begäran från din ITSM tooling med relevanta loggdata i logganalys-arbetsytan.   
    - Visa översta instrumentpaneler för en översikt på incidenter, ändringsbegäranden och berörda system.
    - Skriva Log Analytics-frågor för att få insyn i Service Management-data.

## <a name="adding-the-it-service-management-connector-solution"></a>Lägga till IT-tjänst lösning för hantering av koppling

Lägga till IT Service Management-anslutningstjänsten lösningen till arbetsplatsen Log Analytics med hjälp av den process som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).

IT Service Management-anslutningstjänsten panelen som du ser i galleriet lösningar:

![kopplingen sida vid sida](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Efter lyckad dessutom visas IT Service Management-anslutningstjänsten under **OMS** > **inställningar** > **anslutna källor.**

![ITSMC ansluten](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Som standard uppdaterar IT Service Management-anslutningstjänsten anslutningens data en gång i en gång per dygn. Uppdatera din anslutning data direkt för ändringar eller uppdateringar för mallen som du gör, klicka på Uppdatera visas bredvid din anslutning.

 ![ITSMC uppdatering](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>Konfigurera anslutningen med ITSM programvara

IT Service Connector hanteringslösning stöder anslutningar till **System Center Service Manager**, **ServiceNow**, **Provance**, och **Cherwell**. Konfigurera anslutningen med

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Använda lösningen

När du har konfigurerat IT Service Management-anslutningstjänsten med din ITSM programvara information, startar kopplingen samla in data från den anslutna ITSM produkter eller tjänsten. Inledande synkronisering ska utföras på några minuter beroende på antalet incidenter och ändringsförfrågningar i ITSM produkter eller tjänster.

> [!NOTE]
> - Data som importeras från ITSM produkten av IT Service Management-anslutningstjänsten lösning visas i logganalys som poster av typen **ServiceDesk_CL**.
> - Loggposten innehåller ett fält med namnet **ServiceDeskWorkItemType_s**, vilket är incident eller ändringsbegäran begäran, två typer av data som importeras från produkt-ITSM

## <a name="data-synced-from-itsm-product"></a>Data som synkroniseras från ITSM produkten
Incidenter och ändringsförfrågningar synkroniseras från din ITSM produkt till logganalys-arbetsytan.
Följande information visas exempel på data som samlas in av IT Service Management-kopplingen:

> [!NOTE]
> Beroende på typ av arbetsobjekt som har importerats till Log Analytics **ServiceDesk_CL** innehåller följande fält:

**Arbetsobjekt:** **incidenter**  
ServiceDeskWorkItemType_s = ”Incident”

**Fält**

- ServiceDeskConnectionName
- ID för skrivbord
- Status
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapat av
- Löst av
- Stängts av
- Källa
- Tilldelad till
- Kategori
- Rubrik
- Beskrivning
- Skapad datum
- Stängningsdatum
- Lösningsdatum
- Senast ändrad datum
- Dator


**Arbetsobjekt:** **ändringsbegäranden**

ServiceDeskWorkItemType_s = ”ändra begäran”

**Fält**
- ServiceDeskConnectionName
- ID för skrivbord
- Skapat av
- Stängts av
- Källa
- Tilldelad till
- Rubrik
- Typ
- Kategori
- Status
- Eskalering
- Konflikt Status
- Angelägenhetsgrad
- Prioritet
- Risk
- Påverkan
- Tilldelad till
- Skapad datum
- Stängningsdatum
- Senast ändrad datum
- Begärt datum
- Planerat startdatum
- Planerat slutdatum
- Startdatum för arbete
- Slutdatum för arbete
- Beskrivning
- Dator

## <a name="output-data-for-a-servicenow-incident"></a>Utdata för en ServiceNow-incident

| OMS-fält | ITSM fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| IncidentState_s | Status |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnas av |
| ResolvedBy_s | Löst av|
| ClosedBy_s  | Stängts av |
| Source_s| Kontakta typ |
| AssignedTo_s | Tilldelad till  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Anteckningar |
| CreatedDate_t|  Öppna |
| ClosedDate_t| Stängd|
| ResolvedDate_t|Löst|
| Dator  | konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow ändringsbegäran

| OMS-fält | ITSM fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| CreatedBy_s | Begärd av |
| ClosedBy_s | Stängts av |
| AssignedTo_s | Tilldelad till  |
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

**Exempel logganalys skärmen för ITSM data:**

![Log Analytics skärmen](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management-anslutningstjänsten – integrering med andra OMS-lösningar

Integrering med Tjänstkarta-lösningen stöder för närvarande i IT Service Management-anslutningstjänsten.

Tjänstkarta identifierar programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster automatiskt. Det gör att du kan visa dina servrar som du betrakta dem – som sammanlänkade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar över en TCP-ansluten arkitektur med ingen konfiguration krävs för andra än installation av en agent. Mer information: [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md).

Om du också använda Tjänstkarta lösning kan du visa tjänsten supportavdelningen objekt som skapats i ITSM lösningar som visas i följande exempel:

![ServiceMap integrering](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Skapa ITSM arbetsobjekt för OMS-aviseringar

Med ITSM Connector lösning på plats, kan du konfigurera OMS aviseringar för att utlösa skapandet av arbetsobjekt i dina anslutna ITSM-verktyget på följande sätt:

1. Från **loggen Sök** och köra en sökning i loggen om du vill visa data. Frågeresultatet är källan för arbetsobjekt.
2. I **loggen Sök**, klickar du på **avisering** att öppna den **lägga till Varningsregeln** sidan.

    ![Log Analytics skärmen](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. På den **lägga till Varningsregeln** och ange nödvändig information för **namn**, **allvarlighetsgrad**, **sökfråga**, och **Varna kriterier** (fönstret/Tidsmått för mätning).
4. Välj **Ja** för **ITSM åtgärder**.
5. Välj ITSM-anslutning från den **Välj anslutning** lista.
6. Innehåller information som krävs.
7. Om du vill skapa ett separat arbetsobjekt för varje loggpost för den här aviseringen, Välj den **skapa enskilda arbetsobjekt för varje loggpost** kryssrutan.

    Eller

    Låt kryssrutan omarkerade du skapar bara ett arbetsobjekt för valfritt antal loggposter i den här aviseringen.

7. Klicka på **Spara**.

OMS-avisering som du har skapat visas **inställningar**>**aviseringar**. Motsvarande ITSM anslutningens arbetsobjekt skapas när angivna aviseringen är uppfyllt.

## <a name="create-itsm-work-items-from-oms-logs"></a>Skapa ITSM arbetsobjekt från OMS-loggar

Du kan också skapa arbetsobjekt i de anslutna ITSM källorna direkt från en loggpost på följande sätt:

1. Från **loggen Sök**, söka data som krävs, välja informationen och klicka på **skapa arbetsobjekt**.

    Den **skapa ITSM arbetsobjekt** visas:

    ![Log Analytics skärmen](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Lägg till följande information:

  - **Rubrik för arbetsobjekt**: rubrik för arbetsuppgiften.
  - **Beskrivning av arbetsuppgift**: beskrivning för den nya arbetsuppgiften.
  - **Påverkas datorn**: namnet på den dator där den här loggdata hittades.
  - **Välj anslutning**: ITSM anslutning som du vill skapa det här arbetsobjektet.
  - **Arbetsobjektet**: typ av arbetsobjekt.

3. Om du vill använda en befintlig mall för arbetsobjekt för en incident **Ja** under **generera arbetsobjekt utifrån mallen** alternativ och klickar sedan på **skapa**.

    Eller:

    Klicka på **nr** om du vill ge dina anpassade värden.

4. Ange lämpliga värden i den **kontakta typen**, **inverkan**, **angelägenhetsgrad**, **kategori**, och **underkategorin** textrutor och klicka sedan på **skapa**.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbetsobjekt från Azure-varningar
ITSM koppling är nu integrerat med åtgärdsgrupper.

[Åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md) är ett modulära och återanvändbara sätt utlöser åtgärder för din Azure-aviseringar. Åtgärden ITSM i åtgärdsgrupper skapar arbetsuppgifter i ITSM produkten med en befintlig ITSM Connector-lösning.

1. I Azure-portalen klickar du på **övervakaren**.
2. I den vänstra rutan klickar du på **åtgärdsgrupper**.

    ![Åtgärdsgrupper](media/log-analytics-itsmc/ActionGroups.png)

3. Ange **namn** och **kort filnamn** för din grupp. Välj den **resursgruppen** och **prenumeration** där du vill skapa din grupp.

    ![Åtgärden grupper detaljer](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. Välj i listan åtgärder **ITSM** från den nedrullningsbara menyn för **åtgärdstyp**. Ange en **namn** för åtgärd och klickar på **redigera detaljer**.
5. Välj den **prenumeration** där logganalys-arbetsytan finns. Välj den **anslutning** engångsfaktorautentisering namnet för anslutningstjänsten ITSM följt av namnet på din arbetsyta. Till exempel ”MyITSMMConnector(MyWorkspace)”.

    ![ITSM åtgärdsinformation](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. Välj **arbetsobjekt** typen från den nedrullningsbara menyn.
7. Välja att använda en befintlig mall eller Fyll i fälten som krävs av ITSM produkten.
8. Klicka på **OK**

När du skapar/redigerar en Azure aviseringsregel, använda en grupp, som har en ITSM-åtgärd. När aviseringen utlöser skapa arbetsobjekt i verktyget ITSM.

>[!NOTE]
>För närvarande endast aktiviteten loggen aviseringar stöder ITSM-åtgärd. Den här åtgärden är en no-op för andra Azure-aviseringar.
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>Felsöka ITSM anslutningar i OMS
1.  Om anslutningen misslyckas från anslutna datakällan gränssnitt med en **fel spara anslutningen** visas, gör du följande:
 - För ServiceNow, Cherwell och Provance-anslutningar
    - Se till att du korrekt angav användarnamn, lösenord, klient-ID och klienthemlighet för anslutning.
    - Kontrollera att du har tillräckliga privilegier i motsvarande ITSM produkten för att ansluta.
 - För Service Manager-anslutningar
     - Kontrollera att webbappen har distribuerats och hybridanslutningen har skapats. För att verifiera anslutningen upprättas har med Service Manager-datorn lokalt, gå Webbappens URL som anges i dokumentationen för att göra den [hybridanslutning](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

2.  Om du inte komma har synkroniserats data från ServiceNow till Log Analytics, se till att ServiceNow instansen inte är i viloläge. ServiceNow Dev instanser försättas ibland i viloläge vid inaktivitet under lång tid. Annars rapportera problemet.
3.  Om OMS aviseringar eller men fungerar objekt skapas inte i ITSM produkten eller konfigurationsobjekt inte skapats/kopplad till arbetsobjekt eller allmän information, se på följande platser:
 -  **IT Service Management-anslutningstjänsten lösning**: lösningen innehåller en översikt över anslutningar/artiklar/arbetsdatorer osv. Klicka på panelen visar **Connector-Status**, som tar du **loggen Sök** med relevanta frågan. Titta på loggposter med LogType_S som fel för mer information.
 - Eller visa fel/relaterade direkt i den **loggen Sök** sidan med frågan *typ = ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution
1.  Om du att få problem med web app-distribution, se till att du har tillräckliga behörigheter i prenumerationen som anges för att skapa/distribuera resurser.
2.  Om du får en **”objekt referensen inte anges till instans av ett objekt”** fel när du kör den [skriptet](log-analytics-itsmc-service-manager-script.md), se till att du har angett giltiga värden under **Användarkonfiguration** avsnitt .
3.  Om du inte skapa service bus relay-namnrymd, kontrollera att nödvändiga resursprovidern har registrerats i prenumerationen. Om inte registrerad, måste du manuellt skapa namnområde för service bus relay i Azure Portal. Du kan också skapa den när [hybrid anslutningen](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) från Azure-portalen.


## <a name="contact-us"></a>Kontakta oss

För alla frågor eller feedback om IT Service Management-anslutningstjänsten, kontaktar du oss på [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg
[Lägga till ITSM produkter och tjänster till IT Service Management-anslutningstjänsten](log-analytics-itsmc-connections.md).
