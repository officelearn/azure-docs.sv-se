---
title: Samla in Azure-aktivitetsloggar till Log Analytics i alla prenumerationer | Microsoft Docs
description: Använd Event Hubs och Logic Apps till att samla in data från Azure-aktivitetsloggen och skicka den till en Azure Log Analytics-arbetsyta i en annan klient.
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e4ec1ecbde04cbf843f99bdcdf36977a856c3b03
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52684658"
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>Samla in Azures aktivitetsloggar till Log Analytics i alla prenumerationer

Den här artikeln visar en metod för att samla in Azure-aktivitetsloggar till en Log Analytics-arbetsyta med hjälp av Azure Log Analytics datainsamlare för Logic Apps. Använd processen i den här artikeln när du behöver skicka loggar till en arbetsyta i en annan Azure Active Directory. Om du till exempel är en leverantör av hanterade tjänster kanske du vill samla in aktivitetsloggar från en kunds prenumeration och lagra dem i Log Analytics-arbetsytan i din egen prenumeration.

Om Log Analytics-arbetsytan finns i samma Azure-prenumeration, eller i en annan prenumeration men i samma Azure Active Directory, använder du stegen i [lösningen för Azure-aktivitetsloggar](collect-activity-logs.md) till att samla in Azures aktivitetsloggar.

## <a name="overview"></a>Översikt

Den strategi som används i det här scenariot är att Azure-aktivitetsloggen skickar händelser till en [händelsehubb](../../event-hubs/event-hubs-about.md) där en [logikapp](../../logic-apps/logic-apps-overview.md) skickar dem till Log Analytics-arbetsytan. 

![bild av dataflöde från aktivitetslogg till log analytics](media/collect-activity-logs-subscriptions/data-flow-overview.png)

Fördelarna med den här metoden är:
- Kort svarstid eftersom Azure-aktivitetsloggen strömmas till händelsehubben.  Logikappen utlöses sedan och publicerar datan till Log Analytics. 
- Minimal kod krävs och det finns ingen serverinfrastruktur att distribuera.

Den här artikeln visar hur du:
1. Skapar en händelsehubb. 
2. Exporterar aktivitetsloggar till en händelsehubb med hjälp av Azure-aktivitetsloggens exportprofil.
3. Skapar en logikapp som kan läsa från händelsehubben och skicka händelser till Log Analytics.

## <a name="requirements"></a>Krav
Nedan visas kraven för de Azure-resurser som används i det här scenariot.

- Namnområdet för händelsehubben behöver inte finnas i samma prenumeration som prenumerationen för loggarna. Den användare som konfigurerar inställningen måste ha rätt behörighet för båda prenumerationerna. Om du har flera prenumerationer i samma Azure Active Directory kan du skicka aktivitetsloggar för alla prenumerationer till en enda händelsehubb.
- Logikappen kan finnas i en annan prenumeration från händelsehubben och behöver inte finnas i samma Azure Active Directory. Logikappen läser från händelsehubben med händelsehubbens delade åtkomstnyckel.
- Log Analytics-arbetsytan kan finnas i en annan prenumeration och Azure Active Directory i logikappen, men vi rekommenderar att de finns i samma prenumeration för enkelhetens skull. Logikappen skickar till Log Analytics med Log Analytics arbetsyte-ID och nyckel.



## <a name="step-1---create-an-event-hub"></a>Steg 1 – Skapa en händelsehubb

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. I Azure-portalen väljer du **Skapa en resurs** > **Sakernas Internet** > **Event Hubs**.

   ![Ny händelsehubb i Microsoft Azure Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. Under **Skapa namnområde** anger du antingen ett nytt namnområde eller så väljer du ett befintligt. Systemet kontrollerar omedelbart om namnet är tillgängligt.

   ![bild av dialogrutan skapa händelsehubb](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Välj prisnivå (Basic eller Standard), en Azure-prenumeration, en resursgrupp och en plats för den nya resursen.  Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter på att systemet ska bli klart med att etablera resurserna.
6. Välj det namnområde som du nyss skapade från listan.
7. Välj **Principer för delad åtkomst** och klicka sedan på **RootManageSharedAccessKey**.

   ![bild av händelsehubb med delade åtkomstprinciper](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. 

   ![bild av händelsehubb med delad åtkomstnyckel](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. Spara en kopia av händelsehubbnamnet och antingen en primär eller sekundär anslutningssträng för händelsehubben på en tillfällig plats, exempelvis Anteckningar. Logikappen måste ha tillgång till dessa värden.  Som anslutningssträng för händelsehubben kan du använda **RootManageSharedAccessKey** eller skapa en separat.  Anslutningssträngen som du använder måste börja med `Endpoint=sb://` och finnas i en princip med åtkomstprincipen **Hantera**.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Steg 2 – Exportera aktivitetsloggar till händelsehubb

Om du vill aktivera strömning av aktivitetsloggen, väljer du ett händelsehubbnamnområde och en princip för delad åtkomst för det namnområdet. En händelsehubb skapas i namnområdet när den första nya aktivitetslogghändelsen inträffar. 

Du kan använda ett händelsehubbnamnområde som inte finns i samma prenumeration som den prenumeration som genererar loggar, men prenumerationerna måste finnas i samma Azure Active Directory. Den användare som konfigurerar inställningen måste ha rätt RBAC för båda prenumerationerna. 

1. I Azure-portalen väljer du **Övervaka** > **Aktivitetslogg**.
3. Klicka på knappen **Exportera** högst upp på sidan.

   ![bild av azures övervakare i navigeringen](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Välj den **Prenumeration** som exporten ska göras från och klicka sedan på **Markera alla** i listrutan **Regioner** för att välja händelser för resurserna i alla regioner. Klicka i kryssrutan **Exportera till en händelsehubb**.
7. Klicka på **Service Bus-namnområde** och välj sedan **Prenumeration** med händelsehubben, **händelsehubbnamnområde** och ett **namn på händelsehubbsprincipen**.

    ![bild av export till sidan händelsehubb](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Klicka på **OK** och sedan på **Spara** för att spara inställningarna. Inställningarna tillämpas omedelbart på din prenumeration.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Steg 3 – Skapa en logikapp

När aktivitetsloggarna skrivs till händelsehubben kan du skapa en logikapp som samlar in loggarna från händelsehubben och skriver dem till Log Analytics.

Logikappen innehåller följande:
- En utlösare för [händelsehubbanslutningen](https://docs.microsoft.com/connectors/eventhubs/) som läser från händelsehubben.
- En [åtgärd för att parsa JSON](../../logic-apps/logic-apps-content-type.md) som extraherar JSON-händelser.
- En [skrivåtgärd](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) som konverterar JSON till ett objekt.
- En [Log Analytics-anslutning för att skicka data](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) som skickar datan till Log Analytics.

   ![bild av att lägga till en utlösare för händelsehubben i logikappar](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Krav för logikappar
Kontrollera att du har följande information från föregående steg innan du skapar din logikapp:
- Namn på händelsehubb
- Anslutningssträng för händelsehubben (antingen den primära eller den sekundära) för händelsehubbsnamnområdet.
- Arbetsyte-ID för Log Analytics
- Delad nyckel för Log Analytics

Om du vill hämta händelsehubbens namn och anslutningssträng följer du stegen i [Kontrollera namnområdesbehörigheter i Event Hubs och hitta anslutningssträngen](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Skapa en ny tom logikapp

1. Välj **Skapa en resurs** > **Enterprise-integration** > **Logikapp** i Azure-portalen.

    ![Ny logikapp för Microsoft Azure Marketplace](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Ange inställningarna i tabellen nedan.

    ![Skapa en logikapp](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Inställning | Beskrivning  |
   |:---|:---|
   | Namn           | Unikt namn för logikappen. |
   | Prenumeration   | Välj den Azure-prenumeration som ska innehålla logikappen. |
   | Resursgrupp | Välj en befintlig Azure-resursgrupp eller skapa en ny för logikappen. |
   | Plats       | Välj datacenterregion för att distribuera logikappen. |
   | Log Analytics  | Välj om du vill logga statusen för varje körning av logikappen i Log Analytics.  |

    
3. Välj **Skapa**. När meddelandet **Distributionen lyckades** visas klickar du på **Gå till resurs** för att öppna logikappen.

4. Under **Mallar** väljer du **Tom logikapp**. 

Logic Apps-designern visar dig nu tillgängliga anslutningsappar och deras utlösare, som du använder för att starta logikappens arbetsflöde.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Lägga till händelsehubbutlösare

1. I sökrutan för logikappdesignern skriver du *event hubs* som filter. Välj utlösaren **Event Hubs – När händelser är tillgängliga i händelsehubben**.

   ![bild av att lägga till en utlösare för händelsehubben i logikappar](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. När du uppmanas att ange autentiseringsuppgifter ansluter du till ditt Event Hubs-namnområde. Ange ett namn på din anslutning och sedan anslutningssträngen som du kopierade.  Välj **Skapa**.

   ![bild av att lägga till en anslutning för händelsehubben i logikappar](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. När du har skapat anslutningen, redigerar du inställningarna för utlösaren. Starta genom att välja **insights-operational-logs** i listrutan **Händelsehubbnamn**.

   ![Dialogrutan När händelser är tillgängliga](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Visa **Visa avancerade alternativ** och ändra **Innehållstyp** till *application/json*

   ![Dialogrutan Konfiguration av händelsehubb](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Lägg till åtgärd för att parsa JSON

Utdatan från händelsehubben innehåller en JSON-nyttolast med en matris med poster. Åtgärden [Parsa JSON](../../logic-apps/logic-apps-content-type.md) används för att enbart extrahera matrisen med poster som ska skickas till Log Analytics.

1. Klicka på **Nytt steg** > **Lägg till en åtgärd**
2. I sökrutan skriver du *parse json* som filter. Välj åtgärden **Dataåtgärder – Parsa JSON**.

   ![Lägga till åtgärden Parsa JSON i logikappar](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Klicka i fältet **Innehåll** och välj sedan *Brödtext*.

4. Kopiera och klistra in följande schema i fältet **Schema**.  Det här schemat matchar utdatan från händelsehubbåtgärden.  

   ![Dialogrutan Parsa JSON](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

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
> Du kan hämta ett exempel på en nyttolast genom att klicka på **Kör** och granska dina **råutdata** från händelsehubben.  Du kan sedan använda denna utdata med **Generera schemat genom att använda en exempelnyttolast** i aktiviteten **Parsa JSON** för att skapa schemat.

### <a name="add-compose-action"></a>Lägg till åtgärden Skriv
Åtgärden [Skriv](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) tar JSON-utdatan och skapar ett objekt som kan användas av Log Analytics-åtgärden.

1. Klicka på **Nytt steg** > **Lägg till en åtgärd**
2. Skriv *compose* som filter och välj sedan åtgärden **Dataåtgärder – Skriv**.

    ![Lägga till åtgärden Skriv](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Klicka i fältet **Indata** och välj **Brödtext** under aktiviteten **Parsa JSON**.


### <a name="add-log-analytics-send-data-action"></a>Lägga till åtgärden Skicka Log Analytics
Åtgärden [Datainsamlare för Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) tar objektet från åtgärden Skriv och skickar det till Log Analytics.

1. Klicka på **Nytt steg** > **Lägg till en åtgärd**
2. Skriv *log analytics* som filter och välj sedan åtgärden **Datainsamlare för Azure Log Analytics – Skicka data**.

   ![Lägga till åtgärden Skicka Log Analytics-data i logikappar](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Ange ett namn på anslutningen och klistra in **Arbetsyte-ID** och **Arbetsytenyckel** för Log Analytics-arbetsytan.  Klicka på **Skapa**.

   ![Lägga till Log Analytics-anslutning i logikappar](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. När du har skapat anslutningen, redigerar du inställningarna i tabellen nedan. 

    ![Konfigurera åtgärden Skicka data](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Inställning        | Värde           | Beskrivning  |
   |---------------|---------------------------|--------------|
   |Brödtext i JSON-begäran  | **Utdata** från åtgärden **Skriv** | Hämtar posterna från brödtexten i åtgärden Skriv. |
   | Anpassat loggnamn | AzureActivity | Namn på den anpassade loggtabellen som skapas i Log Analytics för importerad data. |
   | Time-generated-field | time | Markera inte JSON-fältet för **time**, skriv bara ordet time. Om du väljer JSON-fältet placerar designern åtgärden **Skicka data** i loopen *For each*, vilket du inte vill. |




10. Klicka på **Spara** för att spara ändringarna som du har gjort i din logikapp.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Steg 4 – Testa och felsöka logikappen
När arbetsflödet är klart kan du göra ett test i designern för att kontrollera att den fungerar felfritt.

I Logikapp designer klickar du på **Kör** för att testa logikappen. Varje steg i logikappen visar en statusikon, där en vit bock i en grön cirkel visar att det lyckades.

   ![Testa logikapp](media/collect-activity-logs-subscriptions/test-logic-app.png)

Om du vill se detaljerad information om varje steg klickar du på stegets namn för att visa det. Klicka på **Visa råindata** och **Visa råutdata** för att se mer information om de data som tagits emot och skickats i varje steg.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Steg 5 – Visa Azure-aktivitetsloggen i Log Analytics
Det sista steget är att kontrollera Log Analytics-arbetsytan för att säkerställa att data samlas in som förväntat.

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj din arbetsyta i listan med Log Analytics-arbetsytor.
3.  Klicka på panelen **Loggsökning** och i fönsterrutan Loggsökning skriver du sedan `AzureActivity_CL` i frågefältet och trycker på retur eller klickar på sökknappen till höger om fältet. Om du inte har gett den anpassade loggen namnet *AzureActivity*, skriver du namnet som du har valt och lägger till `_CL`.

>[!NOTE]
> Första gången en ny anpassad logg skickas till Log Analytics kan det ta upp till en timme innan den blir sökbar.

>[!NOTE]
> Aktivitetsloggarna skrivs till en anpassad tabell och visas inte i [aktivitetslogglösningen](./collect-activity-logs.md).


![Testa logikapp](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Nästa steg

I denna artikel har du skapat en logikapp som läser Azure-aktivitetsloggar från en händelsehubb och skickar dem till Log Analytics för analys. Mer om att visualisera data i Log Analytics, inklusive att skapa instrumentpaneler, finns i självstudien Visualisera data.

> [!div class="nextstepaction"]
> [Självstudien Visualisera loggsökningsdata](./../../log-analytics/log-analytics-tutorial-dashboards.md)
