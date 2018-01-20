---
title: Samla in Azure aktivitetsloggar till logganalys alla prenumerationer | Microsoft Docs
description: "Använd Händelsehubbar och Logikappar för att samla in data från Azure-aktivitetsloggen och skickar den till en Azure logganalys-arbetsyta i en annan klient."
services: log-analytics, logic-apps, event-hubs
documentationcenter: 
author: richrundmsft
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: richrund; bwren
ms.openlocfilehash: ded0b4cdcbac747d52435023a24b5719f3c58758
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>Samla in Azure aktivitetsloggar till logganalys alla prenumerationer

Den här artikeln steg till en metod för att samla in Azure aktivitetsloggar till en logganalys-arbetsytan med hjälp av Azure Log Analytics datainsamlaren connector för Logic Apps. Använda processen i den här artikeln när du behöver skicka loggar till en arbetsyta i en annan Azure Active Directory. Till exempel om du är en leverantör av hanterade tjänster kanske du vill samla in aktivitetsloggar från en kunds prenumeration och lagrar dem i logganalys-arbetsytan i din egen prenumeration.

Om logganalys-arbetsytan i samma Azure-prenumerationen, eller i en annan prenumeration, men i samma Azure Active Directory använder du stegen i den [Azure aktivitet lösningen](../log-analytics/log-analytics-activity.md) att samla in Azure aktivitetsloggar.

## <a name="overview"></a>Översikt

Den strategi som används i det här scenariot är att Azure-aktivitetsloggen skicka händelser till en [Händelsehubb](../event-hubs/event-hubs-what-is-event-hubs.md) där en [Logikapp](../logic-apps/logic-apps-overview.md) skickar dem till logganalys-arbetsytan. 

![Bild av dataflöde från aktiviteten loggen till logganalys](media/log-analytics-activity-logs-subscriptions/data-flow-overview.png)

Fördelarna med den här metoden är:
- Låg latens eftersom Azure-aktivitetsloggen strömmas till Händelsehubben.  Logikappen utlöses sedan och publicerar data till logganalys. 
- Minimal kod krävs och det finns ingen serverinfrastruktur att distribuera.

Den här artikeln vägleder dig igenom hur du:
1. Skapa en Händelsehubb. 
2. Exportera aktivitetsloggar till en Händelsehubb med hjälp av Azure-aktivitetsloggen Exportera profil.
3. Skapa en Logikapp för att läsa från Event Hub och skicka händelser till logganalys.

## <a name="requirements"></a>Krav
Följande är kraven för Azure-resurser används i det här scenariot.

- Namnområdet Event Hub behöver inte finnas i samma prenumeration som prenumerationen avger loggar. Den användare som konfigurerar inställningen måste ha rätt behörigheter för båda prenumerationer. Om du har flera prenumerationer i samma Azure Active directory kan skicka du aktivitetsloggar för alla prenumerationer till en enda event hub.
- Logikappen kan vara i en annan prenumeration från event hub och behöver inte finnas i samma Azure Active Directory. Logikappen läser från Event Hub med Event Hub delade åtkomstnyckeln.
- Logganalys-arbetsytan kan vara i en annan prenumeration och Azure Active Directory från Logikappen, men vi rekommenderar att de är i samma prenumeration för enkelhetens skull. Logikappen skickar till logganalys med logganalys arbetsyte-ID och nyckel.



## <a name="step-1---create-an-event-hub"></a>Steg 1 – skapar en Händelsehubb

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Välj i Azure-portalen **ny**> **Sakernas Internet** > **Händelsehubbar**.

   ![Ny Marketplace-händelsehubb](media/log-analytics-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Under **skapa namnområdet**, ange ett nytt namnområde eller välj en befintlig. Systemet kontrollerar omedelbart om namnet är tillgängligt.

   ![Bild av skapa event hub dialog](media/log-analytics-activity-logs-subscriptions/create-event-hub1.png)

4. Välj prisnivå (Basic eller Standard), en Azure-prenumeration, resursgrupp och plats för den nya resursen.  Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter för systemet att fullständigt etablera resurser.
6. Klicka på det namnområde som du nyss skapat från listan.
7. Välj **principer för delad åtkomst**, och klicka sedan på **RootManageSharedAccessKey**.

   ![Bild av event hub delade åtkomstprinciper](media/log-analytics-activity-logs-subscriptions/create-event-hub7.png)
   
8. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. 

   ![Bild av event hub delade åtkomstnyckeln](media/log-analytics-activity-logs-subscriptions/create-event-hub8.png)

9. Spara en kopia på en tillfällig plats, till exempel Anteckningar, Event Hub-namn och antingen primär eller sekundär Event Hub anslutningssträngen. Logikappen kräver dessa värden.  Du kan använda för anslutningssträngen Event Hub den **RootManageSharedAccessKey** anslutning string eller skapa en separat.  Anslutningssträngen som du använder måste börja med `Endpoint=sb://` och att för en princip som har den **hantera** princip.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Steg 2 – Export aktivitetsloggar till Händelsehubb

Om du vill aktivera strömning av aktivitetsloggen, Välj en Event Hub Namespace och en princip för delad åtkomst för det namnområdet. En Händelsehubb har skapats i namnutrymmet när den första nya aktivitetsloggen händelsen inträffar. 

Du kan använda en händelse hubb namnområde som inte är i samma prenumeration som prenumerationen avger loggar, men prenumerationerna måste vara i samma Azure Active Directory. Den användare som konfigurerar inställningen måste ha rätt RBAC åtkomst till båda prenumerationer. 

1. Välj i Azure-portalen **övervakaren** > **aktivitetsloggen**.
3. Klicka på den **exportera** längst upp på sidan.

   ![Bild av azure Övervakare i navigeringsfönstret](media/log-analytics-activity-logs-subscriptions/activity-log-blade.png)

4. Välj den **prenumeration** exportera från och klickar sedan på **Markera alla** i den **regioner** listrutan för att välja händelser för resurser i alla regioner. Klicka på den **exportera till en händelsehubb** kryssrutan.
7. Klicka på **Service bus-namnrymd**, och välj sedan den **prenumeration** med händelsehubb, den **event hub namnområde**, och en **principnamnet på händelsehubben**.

    ![Bild av export till event hub sida](media/log-analytics-activity-logs-subscriptions/export-activity-log2.png)

11. Klicka på **OK** och sedan **spara** dessa inställningar ska sparas. Inställningarna tillämpas omedelbart till din prenumeration.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Steg 3 – skapa Logikapp

När aktivitetsloggarna skrivs till händelsehubben, kan du skapa en Logikapp för att samla in loggar från event hub och skriva dem till logganalys.

Logikappen omfattar följande:
- En [Event Hub connector](https://docs.microsoft.com/connectors/eventhubs/) trigger för att läsa från Event Hub.
- En [parsa JSON-åtgärd](../logic-apps/logic-apps-content-type.md) att extrahera JSON-händelser.
- En [utgöra åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) att konvertera JSON till ett objekt.
- En [logganalys skicka dataanslutning](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) att skicka data till logganalys.

   ![bild för att lägga till hubben händelseutlösare i logikappar](media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Krav för logiska appar
Kontrollera att du har följande information från föregående steg innan du skapar din Logikapp:
- Namn på händelsehubb
- Händelsen hubb anslutningssträng (antingen den primära eller sekundära) för Event Hub-namnområdet.
- Log Analytics arbetsyte-ID
- Logganalys delad nyckel

Följ stegen i att få händelsen hubb och anslutningssträngen [Kontrollera Händelsehubbar namnrums-behörigheter och hitta anslutningssträngen](../connectors/connectors-create-api-azure-event-hubs.md#check-event-hubs-namespace-permissions-and-find-the-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Skapa en ny tom Logikapp

1. I Azure portal, väljer **skapar du en resurs** > **Enterprise Integration** > **Logikapp**.

    ![Marketplace ny logikapp](media/log-analytics-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Ange inställningarna i tabellen nedan.

    ![Skapa en logikapp](media/log-analytics-activity-logs-subscriptions/create-logic-app.png)

   |Inställning | Beskrivning  |
   |:---|:---|
   | Namn           | Unikt namn för logikappen. |
   | Prenumeration   | Välj den Azure-prenumeration som innehåller logikappen. |
   | Resursgrupp | Välj en befintlig Azure-resursgrupp eller skapa en ny för logikappen. |
   | Plats       | Välj datacenterregion för att distribuera logikappen. |
   | Log Analytics  | Välj om du vill logga status för varje körning av din logikapp i logganalys.  |

    
3. Välj **Skapa**. När **distributionen lyckades** meddelande visas, klickar du på **finns resurs** att öppna Logikappen.

4. Under **Mallar** väljer du **Tom logikapp**. 

Logic Apps Designer visas nu tillgängliga kopplingar och utlösare, som du använder för att starta arbetsflödet logik app.

<!-- Learn [how to create a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Lägga till Event Hub-utlösare

1. Skriv i sökrutan för logik App Designer *händelsehubbar* för filtret. Välj utlösaren **Händelsehubbar – när händelser är tillgängliga i Event Hub**.

   ![bild för att lägga till hubben händelseutlösare i logikappar](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. När du uppmanas att ange autentiseringsuppgifter ansluta till namnområdet Händelsehubbar. Ange ett namn för din anslutning och anslutningssträngen som du kopierade.  Välj **Skapa**.

   ![Bild av event hub anslutningen läggs till i logikappar](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. När du skapar anslutningen, redigera inställningarna för utlösaren. Starta genom att välja **insikter operativa loggar** från den **Event Hub-namn** listrutan.

   ![När händelser är tillgängliga dialogrutan](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Expandera **visa avancerade alternativ** och ändra **innehållstyp** till *application/json*

   ![Event hub konfigurationsdialogruta](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Lägg till parsa JSON-åtgärd

Utdata från Event Hub innehåller en JSON-nyttolast med en matris med poster. Den [parsa JSON](../logic-apps/logic-apps-content-type.md) åtgärd används för att extrahera bara array av poster för att skicka till logganalys.

1. Klicka på **nytt steg** > **lägga till en åtgärd**
2. I sökrutan skriver *parsa json* för filtret. Välj åtgärden som **dataåtgärder - parsa JSON**.

   ![Att lägga till parsa json-åtgärden i logikappar](media/log-analytics-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Klicka i den **innehåll** fältet och välj sedan *brödtext*.

4. Kopiera och klistra in följande schema i den **schemat** fältet.  Det här schemat matchar utdata från Event Hub-åtgärd.  

   ![Parsa json dialogrutan](media/log-analytics-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Du kan få en exempel-nyttolast genom att klicka på **kör** och titta på den **Raw utdata** från Event Hub.  Du kan sedan använda den här utdata med **Använd exemplet nyttolast för att skapa schema** i den **parsa JSON** aktivitet för att skapa schemat.

### <a name="add-compose-action"></a>Lägg till Compose åtgärd
Den [Compose](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) åtgärden tar JSON-utdata och skapar ett objekt som kan användas för logganalys-åtgärden.

1. Klicka på **nytt steg** > **lägga till en åtgärd**
2. Typen *compose* för filter och välj sedan åtgärden **dataåtgärder - utgöra**.

    ![Lägg till Compose åtgärd](media/log-analytics-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Klicka på den **indata** fältet och välj **brödtext** under den **parsa JSON** aktivitet.


### <a name="add-log-analytics-send-data-action"></a>Lägg till Log Analytics skicka Data åtgärd
Den [Azure Log Analytics datainsamlaren](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) åtgärden tar objekt från åtgärden Skriv och skickar det till logganalys.

1. Klicka på **nytt steg** > **lägga till en åtgärd**
2. Typen *logga analytics* för filter och välj sedan åtgärden **datainsamlaren för Azure Log Analytics - skicka Data**.

   ![Att lägga till logganalys kicka data i logikappar](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Ange ett namn för din anslutning och klistra in den **arbetsyte-ID** och **Arbetsytenyckel** för logganalys-arbetsytan.  Klicka på **Skapa**.

   ![Log analytics-anslutningen läggs till i logikappar](media/log-analytics-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. När du skapar anslutningen, redigera inställningarna i tabellen nedan. 

    ![Konfigurera skicka dataåtgärd](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Inställning        | Värde           | Beskrivning  |
   |---------------|---------------------------|--------------|
   |Brödtext i JSON-begäran  | **Utdata** från den **Compose** åtgärd | Hämtar poster från brödtexten i Skriv-åtgärd. |
   | Anpassat loggnamn | AzureActivity | Namn på anpassad logg-tabellen för att skapa i Log Analytics för att rymma den importerade informationen. |
   | Time-generated-field | time | Markera inte JSON-fältet för **tid** -skriver word-tid. Om du väljer fältet JSON designern placerar den **skicka Data** åtgärd i en *för varje* skapas, vilket inte är vad du vill. |




10. Klicka på **spara** spara ändringarna som du har gjort i din Logikapp.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Steg 4: testa och felsöka Logikappen
Du kan testa i designern för att verifiera att den fungerar utan fel med arbetsflödet slutförts.

I logik App Designer klickar du på **kör** att testa Logikappen. Varje steg i Logikappen visar en statusikon med en grön cirkel som anger lyckad vit markerat.

   ![Testa logikappen](media/log-analytics-activity-logs-subscriptions/test-logic-app.png)

Om du vill se detaljerad information om varje steg, klicka på Stegnamn att expandera den. Klicka på **visar rådata indata** och **visar rådata utdata** vill se mer information om de data som tagits emot och skickats i varje steg.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Steg 5 – visa Azure aktivitetsloggen i logganalys
Det sista steget är att kontrollera logganalys-arbetsytan för att säkerställa att data samlas in som förväntat.

1. Välj i Azure-portalen **logganalys**.
2. Välj din arbetsyta och sedan den **loggen Sök** panelen.
3. Skriv i sökfältet frågan `AzureActivity_CL` och klicka på sökknappen. Om du inte name anpassade loggen *AzureActivity*, skriver du namnet som du har valt och Lägg till `_CL`.

>[!NOTE]
> Första gången en ny anpassad logg skickas till logganalys kan det ta upp till en timme för anpassade loggen som ska sökas.

>[!NOTE]
> Aktivitetsloggarna skrivs till en anpassad tabell och visas inte i den [aktivitetsloggen lösning](./log-analytics-activity.md).


![Testa logikappen](media/log-analytics-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Nästa steg

Du har skapat en logikapp för att läsa Azure aktivitetsloggar från en Händelsehubb och skicka dem till logganalys för analys i den här artikeln. Om du vill lära dig mer om visualisera data i logganalys, inklusive att skapa instrumentpaneler, granska självstudierna för visualisera data.

> [!div class="nextstepaction"]
> [Visualisera loggen Sök data självstudiekursen](./log-analytics-tutorial-dashboards.md)
