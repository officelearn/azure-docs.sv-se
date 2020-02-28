---
title: Anslutningsprogram för hantering av IT-tjänster (ITSM) i Azure Log Analytics | Microsoft Docs
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) och information om hur du använder den här lösningen för att centralt övervaka och hantera ITSM-arbetsobjekten i Azure Log Analytics och lösa eventuella problem snabbt.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672233"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Anslut Azure till ITSM-verktyg med Anslutningsprogram för hantering av IT-tjänster (ITSM)

![Anslutningsprogram för hantering av IT-tjänster (ITSM) symbol](media/itsmc-overview/itsmc-symbol.png)

Med Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) kan du ansluta Azure och en ITSM-produkt (IT Service Management) som stöds.

Azure-tjänster som Log Analytics och Azure Monitor innehåller verktyg för att identifiera, analysera och felsöka problem med dina Azure-och icke-Azure-resurser. Arbets objekt som är relaterade till ett problem finns dock vanligt vis i en ITSM produkt/tjänst. ITSM-anslutningen innehåller en dubbelriktad anslutning mellan Azure-och ITSM-verktyg som hjälper dig att lösa problem snabbare.

ITSMC stöder anslutningar med följande ITSM-verktyg:

-   ServiceNow
-   System Center Service Manager
-   Styrka
-   Cherwell

Med ITSMC kan du:

-  Skapa arbets objekt i ITSM-verktyget baserat på dina Azure-aviseringar (mått aviseringar, aktivitets logg aviseringar och Log Analytics aviseringar).
-  Du kan också synkronisera dina incidenter och ändra begär ande data från ITSM-verktyget till en Azure Log Analytics-arbetsyta.

Läs mer om de [juridiska villkoren och sekretess policyn](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Du kan börja använda ITSM-anslutningsprogram med hjälp av följande steg:

1.  [Lägg till ITSM-anslutningsprogram-lösningen](#adding-the-it-service-management-connector-solution)
2.  Skapa en ITSM-anslutning
3.  [Använd anslutningen](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Lägga till Anslutningsprogram för hantering av IT-tjänster (ITSM)-lösningen

Innan du kan skapa en anslutning måste du lägga till ITSM-anslutningsprogram-lösningen.

1. Klicka på **+ ny** ikon i Azure Portal.

   ![Ny Azure-resurs](media/itsmc-overview/azure-add-new-resource.png)

2. Sök efter **anslutningsprogram för hantering av IT-tjänster (ITSM)** i Marketplace och klicka på **skapa**.

   ![Lägg till ITSMC-lösning](media/itsmc-overview/add-itsmc-solution.png)

3. I avsnittet **OMS-arbetsyta** väljer du den Azure Log Analytics-arbetsyta där du vill installera lösningen.
   >[!NOTE]
   > * Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS-arbetsytor nu Log Analytics arbets ytor.
   > * ITSM-anslutningsprogram kan bara installeras i Log Analytics arbets ytor i följande regioner: östra USA, västra 2; USA, södra centrala USA, västra centrala USA, centrala Kanada, Västeuropa, södra Storbritannien, Sydostasien, Östra Japan, centrala Indien, sydöstra Australien.

4. I avsnittet **Inställningar för OMS-arbetsyta** väljer du den ResourceGroup där du vill skapa lösnings resursen.

   ![ITSMC-arbetsyta](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS-arbetsytor nu Log Analytics arbets ytor.

5. Klicka på **Skapa**.

När lösnings resursen har distribuerats visas ett meddelande längst upp till höger i fönstret.


## <a name="creating-an-itsm--connection"></a>Skapa en ITSM-anslutning

När du har installerat lösningen kan du skapa en anslutning.

För att skapa en anslutning måste du använda ITSM-verktyget för att tillåta anslutningen från ITSM-anslutningsprogram-lösningen.  

Använd följande steg beroende på vilken ITSM-produkt du ansluter till:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Styrka](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

När du har beredd dina ITSM-verktyg följer du stegen nedan för att skapa en anslutning:

1. Gå till **alla resurser**, Sök efter **Servicedesk (YourWorkspaceName)** .
2. Under **arbets ytans data källor** i den vänstra rutan klickar du på **ITSM-anslutningar**.
   ![ITSM-anslutningar](media/itsmc-overview/itsm-connections.png)

   Den här sidan visar listan över anslutningar.
3. Klicka på **Lägg till anslutning**.

   ![Lägg till ITSM-anslutning](media/itsmc-overview/add-new-itsm-connection.png)

4. Ange anslutnings inställningarna enligt beskrivningen i [Konfigurera ITSMC-anslutningen med dina ITSM produkter/tjänster-artikeln](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Som standard uppdaterar ITSMC anslutningens konfigurations data en gång var 24: e timme. Om du vill uppdatera din anslutnings data direkt för eventuella ändringar eller uppdaterings uppdateringar som du gör, klickar du på knappen **Synkronisera** på bladet anslutning.

   ![Anslutnings uppdatering](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Använda lösningen
   Genom att använda ITSM-anslutningsprogram lösning kan du skapa arbets objekt från Azure-aviseringar, Log Analytics aviseringar och Log Analytics logg poster.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Skapa ITSM arbets objekt från Azure-aviseringar

När du har skapat din ITSM-anslutning kan du skapa arbets objekt i ITSM-verktyget baserat på Azure-aviseringar med hjälp av **åtgärden ITSM** i **Åtgärds grupper**.

Med åtgärds grupper får du ett modulärt och återanvändbart sätt att utlösa åtgärder för dina Azure-aviseringar. Du kan använda åtgärds grupper med mått aviseringar, aktivitets logg aviseringar och Azure Log Analytics varningar i Azure Portal.

Följ dessa steg:

1. Klicka på **övervaka**i Azure Portal.
2. I den vänstra rutan klickar du på **Åtgärds grupper**. Fönstret **Lägg till åtgärds grupp** visas.

    ![Åtgärdsgrupper](media/itsmc-overview/action-groups.png)

3. Ange **namn** och **ShortName** för åtgärds gruppen. Välj den **resurs grupp** och **prenumeration** där du vill skapa en åtgärds grupp.

    ![Information om åtgärds grupper](media/itsmc-overview/action-groups-details.png)

4. I listan åtgärder väljer du **ITSM** på den nedrullningsbara menyn för **Åtgärds typ**. Ange ett **namn** för åtgärden och klicka på **Redigera information**.
5. Välj den **prenumeration** där Log Analytics arbets ytan finns. Välj **anslutnings** namnet (ditt ITSM-anslutningsprogram namn) följt av namnet på din arbets yta. Till exempel "MyITSMMConnector (min arbets yta)".

    ![ITSM åtgärds information](media/itsmc-overview/itsm-action-details.png)

6. Välj typ av **arbets objekt** på den nedrullningsbara menyn.
   Välj att använda en befintlig mall eller fyll i fälten som krävs av din ITSM-produkt.
7. Klicka på **OK**

När du skapar/redigerar en regel för Azure-avisering använder du en åtgärds grupp som har en ITSM-åtgärd. När aviseringen utlöses skapas/uppdateras arbets objekt i ITSM-verktyget.

> [!NOTE]
>
> Information om prissättning av ITSM-åtgärder finns på [sidan med priser](https://azure.microsoft.com/pricing/details/monitor/) för åtgärds grupper.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualisera och analysera incidenter och data för ändringsbegäran

Beroende på din konfiguration när du konfigurerar en anslutning kan ITSM Connector synkronisera upp till 120 dagar av incidenten och data för ändringsbegäran. Schemat för logg poster för dessa data finns i [Nästa avsnitt](#additional-information).

Incident-och ändrings begär ande data kan visualiseras med hjälp av ITSM-anslutningsprogram instrument panelen i lösningen.

![Log Analytics skärm](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Instrument panelen innehåller också information om anslutnings status som kan användas som utgångs punkt för att analysera eventuella problem med anslutningarna.

Du kan också visualisera de incidenter som synkroniseras mot de påverkade datorerna i Tjänstkarta-lösningen.

Tjänstkarta identifierar automatiskt program komponenterna i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Det gör att du kan visa dina servrar som du tänker på dem – som sammankopplade system som levererar kritiska tjänster. Tjänstkarta visar anslutningar mellan servrar, processer och portar i alla TCP-anslutna arkitekturer utan att någon konfiguration krävs förutom installation av en agent. [Läs mer](../../azure-monitor/insights/service-map.md).

Om du använder Tjänstkarta-lösningen kan du Visa Service Desk-objekten som skapats i ITSM-lösningarna som visas i följande exempel:

![Log Analytics skärm](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Mer information: [tjänstkarta](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Ytterligare information

### <a name="data-synced-from-itsm-product"></a>Data har synkroniserats från ITSM-produkten
Incidenter och ändrings begär Anden synkroniseras från din ITSM-produkt till din Log Analytics-arbetsyta baserat på anslutningens konfiguration.

Följande information visar exempel på data som samlas in av ITSMC:

> [!NOTE]
>
> Beroende på typen av arbets objekt som importer ATS till Log Analytics, innehåller **ServiceDesk_CL** följande fält:

**Arbets objekt:** **incidenter**  
ServiceDeskWorkItemType_s="Incident"

**Fält**

- ServiceDeskConnectionName
- Service Desk-ID
- Status
- Angelägenhetsgrad
- Påverkan
- Prioritet
- Eskalering
- Skapat av
- Löst av
- Stängd av
- Källa
- Tilldelad till
- Kategori
- Rubrik
- Beskrivning
- Skapad datum
- Stängningsdatum
- Lösningsdatum
- Datum för senaste ändring
- Dator


**Arbets objekt:** **ändrings begär Anden**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fält**
- ServiceDeskConnectionName
- Service Desk-ID
- Skapat av
- Stängd av
- Källa
- Tilldelad till
- Rubrik
- Typ
- Kategori
- Status
- Eskalering
- Konflikt status
- Angelägenhetsgrad
- Prioritet
- Risk
- Påverkan
- Tilldelad till
- Skapad datum
- Stängningsdatum
- Datum för senaste ändring
- Begärt datum
- Planerat start datum
- Planerat slutdatum
- Start datum för arbete
- Slutdatum för arbete
- Beskrivning
- Dator

## <a name="output-data-for-a-servicenow-incident"></a>Utdata för en ServiceNow-incident

| Log Analytics fält | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| IncidentState_s | Status |
| Urgency_s |Angelägenhetsgrad |
| Impact_s |Påverkan|
| Priority_s | Prioritet |
| CreatedBy_s | Öppnad av |
| ResolvedBy_s | Löst av|
| ClosedBy_s  | Stängd av |
| Source_s| Kontakt typ |
| AssignedTo_s | Tilldelad till  |
| Category_s | Kategori |
| Title_s|  Kort beskrivning |
| Description_s|  Anteckningar |
| CreatedDate_t|  Inleddes |
| ClosedDate_t| Stängd|
| ResolvedDate_t|Matchat|
| Dator  | Konfigurationsobjekt |

## <a name="output-data-for-a-servicenow-change-request"></a>Utdata för en ServiceNow-ändringsbegäran

| Log Analytics | ServiceNow-fält |
|:--- |:--- |
| ServiceDeskId_s| Tal |
| CreatedBy_s | Begärd av |
| ClosedBy_s | Stängd av |
| AssignedTo_s | Tilldelad till  |
| Title_s|  Kort beskrivning |
| Type_s|  Typ |
| Category_s|  Kategori |
| CRState_s|  Status|
| Urgency_s|  Angelägenhetsgrad |
| Priority_s| Prioritet|
| Risk_s| Risk|
| Impact_s| Påverkan|
| RequestedDate_t  | Begärd efter datum |
| ClosedDate_t | Stängningsdatum |
| PlannedStartDate_t  |     Planerat start datum |
| PlannedEndDate_t  |   Planerat slutdatum |
| WorkStartDate_t  | Verkligt startdatum |
| WorkEndDate_t | Verkligt slutdatum|
| Description_s | Beskrivning |
| Dator  | Konfigurationsobjekt |


## <a name="troubleshoot-itsm-connections"></a>Felsöka ITSM-anslutningar
1. Om anslutningen Miss lyckas från den anslutna källans användar gränssnitt och ett **fel uppstod när anslutnings meddelandet sparades** , utför du följande steg:
   - För ServiceNow, Cherwell och uppstyrke anslutningar,  
   - kontrol lera att du har angett användar namn, lösen ord, klient-ID och klient hemlighet korrekt för varje anslutning.  
   - kontrol lera att du har tillräcklig behörighet i motsvarande ITSM-produkt för att upprätta anslutningen.  
   - För Service Manager anslutningar  
   - kontrol lera att webbappen har distribuerats och att hybrid anslutningen har skapats. Du kan kontrol lera att anslutningen har upprättats med den lokala Service Manager datorn genom att gå till webbappens URL enligt beskrivningen i dokumentationen för att skapa en [hybrid anslutning](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Om data från ServiceNow inte kommer att synkroniseras till Log Analytics, se till att ServiceNow-instansen inte är i vilo läge. ServiceNow dev-instanser går ibland till ström spar läge när de är inaktiva under en längre tid. Annars kan du rapportera problemet.
3. Om Log Analytics-aviseringar om brand, men arbets objekt inte skapas i ITSM-produkten eller om konfigurations objekt inte skapas/länkas till arbets objekt eller för någon annan allmän information, tittar du på följande platser:
   -  ITSMC: lösningen visar en sammanfattning av anslutningar/arbets objekt/datorer osv. Klicka på panelen som visar **kopplings status**, som gör att du kan **Logga sökningen** med den relevanta frågan. Mer information hittar du i logg posterna med LogType_S som fel.
   - Sidan **loggs ökning** : Visa fel/relaterad information direkt med frågan `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Felsöka Service Manager Web App-distribution
1.  Om det uppstår problem med distributionen av webbappar kontrollerar du att du har tillräcklig behörighet i prenumerationen som nämns skapa/distribuera resurser.
2.  Om du får fel meddelandet **"objekt referens som inte har angetts till en instans av ett objekt"** visas när du kör [skriptet](itsmc-service-manager-script.md), se till att du har angett giltiga värden under **användar konfiguration** .
3.  Om du inte kan skapa Service Bus Relay-namnrymden måste du kontrol lera att den nödvändiga resurs leverantören är registrerad i prenumerationen. Om detta inte är registrerat skapar du Service Bus Relay-namnrymden manuellt från Azure Portal. Du kan också skapa den när du [skapar hybrid anslutningen](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) från Azure Portal.


## <a name="contact-us"></a>Kontakta oss

Kontakta oss på [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)om du har frågor eller feedback på anslutningsprogram för hantering av IT-tjänster (ITSM).

## <a name="next-steps"></a>Nästa steg
[Lägg till ITSM produkter/tjänster i anslutningsprogram för hantering av IT-tjänster (ITSM)](../../azure-monitor/platform/itsmc-connections.md).
