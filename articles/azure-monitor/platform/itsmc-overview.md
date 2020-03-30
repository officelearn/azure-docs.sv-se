---
title: IT Service Management Connector i Azure Log Analytics | Microsoft-dokument
description: Den här artikeln innehåller en översikt över ITSMC (IT Service Management Connector) och information om hur du använder den här lösningen centralt för att övervaka och hantera ITSM-arbetsobjekten i Azure Log Analytics och lösa eventuella problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274546"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Ansluta Azure till ITSM-verktyg med IT Service Management Connector

![Symbol för IT Service Management Connector](media/itsmc-overview/itsmc-symbol.png)

Med ITSMC (IT Service Management Connector) kan du ansluta Azure och en ITSM-produkt/tjänst (SOM stöds av IT Service Management).

Azure-tjänster som Log Analytics och Azure Monitor tillhandahåller verktyg för att identifiera, analysera och felsöka problem med dina Azure- och icke-Azure-resurser. De arbetsobjekt som är relaterade till ett problem finns dock vanligtvis i en ITSM-produkt/tjänst. ITSM-anslutningen ger en dubbelriktad anslutning mellan Azure och ITSM-verktyg som hjälper dig att lösa problem snabbare.

ITSMC stöder anslutningar med följande ITSM-verktyg:

-   ServiceNow
-   Servicehanteraren för SystemCenter
-   Provance (bevis)
-   Cherwell

Med ITSMC kan du:

-  Skapa arbetsobjekt i ITSM-verktyget, baserat på dina Azure-aviseringar (måttaviseringar, aktivitetsloggaviseringar och logganalysaviseringar).
-  Du kan också synkronisera dina incident- och ändringsbegärandedata från itsm-verktyget till en Azure Log Analytics-arbetsyta.

Läs mer om [de juridiska villkoren och sekretesspolicyn](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Du kan börja använda ITSM Connector med följande steg:

1.  [Lägg till ITSM-anslutningslösningen](#adding-the-it-service-management-connector-solution)
2.  Skapa en ITSM-anslutning
3.  [Använd anslutningen](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Lägga till lösning för IT-tjänsthanteringsanslutning

Innan du kan skapa en anslutning måste du lägga till ITSM Connector-lösningen.

1. Klicka på **+ Ny** ikon i Azure-portalen.

   ![Ny Azure-resurs](media/itsmc-overview/azure-add-new-resource.png)

2. Sök efter **IT Service Management Connector** på Marketplace och klicka på **Skapa**.

   ![Lägg till ITSMC-lösning](media/itsmc-overview/add-itsmc-solution.png)

3. I avsnittet **OMS-arbetsyta** väljer du arbetsytan Azure Log Analytics där du vill installera lösningen.
   >[!NOTE]
   > * Som en del av den pågående övergången från OmS (Microsoft Operations Management Suite) till Azure Monitor kallas OMS-arbetsytor nu för Log Analytics-arbetsytor.
   > * ITSM Connector kan endast installeras i Log Analytics-arbetsytor i följande regioner: Östra USA, Västra USA2, Södra centrala USA, Västra centrala USA, Centrala Kanada, Västeuropa, Södra Storbritannien, Sydostasien, Östra Japan, Centrala Indien, Sydöstra Australien.

4. I avsnittet **OMS-arbetsyteinställningar** väljer du den ResourceGroup där du vill skapa lösningsresursen.

   ![ITSMC arbetsyta](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Som en del av den pågående övergången från OmS (Microsoft Operations Management Suite) till Azure Monitor kallas OMS-arbetsytor nu för Log Analytics-arbetsytor.

5. Klicka på **Skapa**.

När lösningsresursen distribueras visas ett meddelande längst upp till höger i fönstret.


## <a name="creating-an-itsm--connection"></a>Skapa en ITSM-anslutning

När du har installerat lösningen kan du skapa en anslutning.

För att skapa en anslutning måste du förbereda itsm-verktyget för att tillåta anslutningen från ITSM Connector-lösningen.  

Beroende på vilken ITSM-produkt du ansluter till gör du så här:

- [Servicehanteraren för System Center (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance (bevis)](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

När du har förberett dina ITSM-verktyg följer du stegen nedan för att skapa en anslutning:

1. Gå till **Alla resurser**, leta efter **ServiceDesk(YourWorkspaceName)**.
2. Klicka på **ITSM-anslutningar**under **ARBETSUTRYMMESDATAKÄLLOR** i den vänstra rutan.
   ![ITSM-anslutningar](media/itsmc-overview/itsm-connections.png)

   På den här sidan visas listan över anslutningar.
3. Klicka på **Lägg till anslutning**.

   ![Lägg till ITSM-anslutning](media/itsmc-overview/add-new-itsm-connection.png)

4. Ange de anslutningsinställningar som beskrivs i [Konfigurera ITSMC-anslutningen till din ITSM-produkt/-tjänstartikel](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Som standard uppdaterar ITSMC anslutningens konfigurationsdata en gång var 24:e timme. Om du vill uppdatera anslutningens data direkt för alla redigeringar eller malluppdateringar som du gör klickar du på **knappen Synkronisera** på anslutningens blad.

   ![Uppdatering av anslutning](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Använda lösningen
   Genom att använda ITSM Connector-lösningen kan du skapa arbetsobjekt från Azure-aviseringar, Log Analytics-aviseringar och Logganalysloggposter.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM-arbetsobjekt från Azure-aviseringar

När du har skapat ITSM-anslutningen kan du skapa arbetsobjekt i ITSM-verktyget baserat på Azure-aviseringar genom att använda **ITSM-åtgärden** i **åtgärdsgrupper**.

Åtgärdsgrupper är ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärdsgrupper med måttaviseringar, aktivitetsloggaviseringar och Azure Log Analytics-aviseringar i Azure-portalen.

Följ dessa steg:

1. Klicka på **Övervaka**i Azure-portalen .
2. Klicka på **Åtgärdsgrupper**i den vänstra rutan . Fönstret **Lägg till åtgärdsgrupp** visas.

    ![Åtgärdsgrupper](media/itsmc-overview/action-groups.png)

3. Ange **namn** och **kortnamn** för din åtgärdsgrupp. Välj den **resursgrupp** och **prenumeration** där du vill skapa din åtgärdsgrupp.

    ![Information om åtgärdsgrupper](media/itsmc-overview/action-groups-details.png)

4. I listan Åtgärder väljer du **ITSM** på den nedrullningsbara menyn för **Åtgärdstyp**. Ange ett **namn** för åtgärden och klicka på **Redigera information**.
5. Välj den **prenumeration** där din Log Analytics-arbetsyta finns. Välj **anslutningsnamnet** (ITSM Connector-namnet) följt av namnet på arbetsytan. Till exempel "MyITSMMConnector(MyWorkspace)."

    ![INFORMATION OM ITSM-åtgärder](media/itsmc-overview/itsm-action-details.png)

6. Välj **Typ Av arbetsobjekt** på den nedrullningsbara menyn.
   Välj att använda en befintlig mall eller fylla i de fält som krävs av ITSM-produkten.
7. Klicka på **OK**.

När du skapar/redigerar en Azure-aviseringsregel använder du en åtgärdsgrupp som har en ITSM-åtgärd. När aviseringen utlöses skapas/uppdateras arbetsobjektet i ITSM-verktyget.

> [!NOTE]
>
> Information om prissättning av ITSM-åtgärd finns på [prissidan](https://azure.microsoft.com/pricing/details/monitor/) för åtgärdsgrupper.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera data för incident- och ändringsbegäran

Baserat på din konfiguration när du konfigurerar en anslutning kan ITSM-anslutning synkronisera upp till 120 dagars incident- och ändringsbegärandedata. Loggpostschemat för dessa data finns i [nästa avsnitt](#additional-information).

Incident- och ändringsbegäransdata kan visualiseras med hjälp av ITSM Connector-instrumentpanelen i lösningen.

![Logga Analytics-skärmen](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrumentpanelen ger också information om anslutningsstatus som kan användas som utgångspunkt för att analysera eventuella problem med anslutningarna.

Du kan också visualisera de incidenter som synkroniserats mot de påverkade datorerna i servicemappningslösningen.

Service Map identifierar automatiskt programkomponenterna på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Du kan se dina servrar så som du tänker på dem: ett sammankopplat system som levererar viktiga tjänster. Med Service Map ser du anslutningarna mellan olika servrar, processer och portar i alla typer av TCP-anslutna arkitekturer, och den enda konfiguration som behövs är att installera en agent. [Läs mer](../../azure-monitor/insights/service-map.md).

Om du använder Service Map-lösningen kan du visa de servicedesk-artiklar som skapats i ITSM-lösningarna enligt följande exempel:

![Logga Analytics-skärmen](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mer information: [Service Map](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Ytterligare information

### <a name="data-synced-from-itsm-product"></a>Data synkroniserade från ITSM-produkten
Incidenter och ändringsbegäranden synkroniseras från DIN ITSM-produkt till logganalysarbetsytan baserat på anslutningens konfiguration.

Följande information visar exempel på data som samlats in av ITSMC:

> [!NOTE]
>
> Beroende på vilken arbetsobjekttyp som importeras till Logganalys innehåller **ServiceDesk_CL** följande fält:

**Arbetsuppgift:** **Incidenter**  
ServiceDeskWorkItemType_s="Incident"

**Fält**

- ServiceDeskConnectionName
- Service desk-ID
- Status
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapad av
- Löst av
- Stängt av
- Källa
- Tilldelad till
- Kategori
- Titel
- Beskrivning
- Skapad datum
- Stängningsdatum
- Lösningsdatum
- Senast ändrad datum
- Dator


**Arbetsuppgift:** **Ändra begäranden**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fält**
- ServiceDeskConnectionName
- Service desk-ID
- Skapad av
- Stängt av
- Källa
- Tilldelad till
- Titel
- Typ
- Kategori
- Status
- Eskalering
- Konfliktstatus
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

| Fältet Logganalys | Fältet ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| IncidentState_s | Status |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnas av |
| ResolvedBy_s | Löst av|
| ClosedBy_s  | Stängt av |
| Source_s| Kontakttyp |
| AssignedTo_s | Tilldelad  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Anteckningar |
| CreatedDate_t|  Öppnade |
| ClosedDate_t| stängd|
| ResolvedDate_t|Matchat|
| Dator  | Konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow-ändringsbegäran

| Log Analytics | Fältet ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| CreatedBy_s | Begärs av |
| ClosedBy_s | Stängt av |
| AssignedTo_s | Tilldelad  |
| Title_s|  Kort beskrivning |
| Type_s|  Typ |
| Category_s|  Kategori |
| CRState_s|  Status|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärt av datum |
| ClosedDate_t | Stängt datum |
| PlannedStartDate_t  |     Planerat startdatum |
| PlannedEndDate_t  |   Planerat slutdatum |
| WorkStartDate_t  | Verkligt startdatum |
| WorkEndDate_t | Faktiskt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurationsobjekt |


## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar
1. Om anslutningen misslyckas från den anslutna källans användargränssnitt med ett **fel vid sparande av anslutningsmeddelande** gör du följande:
   - För Anslutningar för ServiceNow, Cherwell och Provance  
   - se till att du korrekt angav användarnamn, lösenord, klient-ID och klienthemlighet för var och en av anslutningarna.  
   - kontrollera om du har tillräckliga privilegier i motsvarande ITSM-produkt för att upprätta anslutningen.  
   - För Service Manager-anslutningar  
   - kontrollera att webbappen har distribuerats och att hybridanslutning skapas. Om du vill kontrollera att anslutningen har upprättats med den lokala Service Manager-datorn besöker du webbapp-URL:en som beskrivs i dokumentationen för att upprätta [hybridanslutningen](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Om data från ServiceNow inte synkroniseras till Log Analytics kontrollerar du att ServiceNow-instansen inte förs i viloläge. ServiceNow Dev-instanser försätts ibland i viloläge när de är inaktiva under en längre tid. Annars, rapportera frågan.
3. Om Log Analytics varnar för brand men arbetsobjekt inte skapas i ITSM-produkt eller konfigurationsobjekt inte skapas/länkas till arbetsobjekt eller för annan allmän information, titta på följande platser:
   -  ITSMC: Lösningen visar en sammanfattning av anslutningar / arbetsobjekt / datorer etc. Klicka på panelen som visar **Anslutningsstatus**, som tar dig till **Logga sökning** med relevant fråga. Titta på loggposter med LogType_S som fel för mer information.
   - **Loggsökningssida:** Visa fel/relaterad information `*`direkt med`*`frågan ServiceDeskLog_CL .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka distribution av Service Manager Web App
1.  Om det finns problem med distribution av webbappar, se till att du har tillräcklig behörighet i prenumerationen som nämns för att skapa/distribuera resurser.
2.  Om du får felmeddelandet **"Objektreferens som inte är inställt på instans av ett objekt"** när du kör [skriptet](itsmc-service-manager-script.md)kontrollerar du att du har angett giltiga värden under avsnittet **Användarkonfiguration.**
3.  Om du inte kan skapa servicebussrelänamnområde kontrollerar du att den nödvändiga resursprovidern är registrerad i prenumerationen. Om det inte är registrerat skapar du manuellt namnområdet för servicebussrelä från Azure-portalen. Du kan också skapa den när [du skapar hybridanslutningen](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) från Azure-portalen.


## <a name="contact-us"></a>Kontakta oss

Kontakta oss på [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg
[Lägg till ITSM-produkter/-tjänster i IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md).
