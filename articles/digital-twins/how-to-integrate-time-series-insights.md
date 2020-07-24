---
title: Integrera med Time Series Insights
titleSuffix: Azure Digital Twins
description: Se så här ställer du in händelse vägar från Azure Digitals dubbla till Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131607"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integrera digitala dubbla med Azure Time Series Insights

I den här referensen får du lära dig hur du integrerar Azure Digitals dubbla med [Azure Time Series Insights (TSD)](../time-series-insights/overview-what-is-tsi.md). Med den här lösningen kan du samla in och analysera historiska data om din IoT-lösning. Azure digitala multipler är en bra plats för att mata in data i Time Series Insights, eftersom det gör att du kan korrelera flera data strömmar och standardisera din information innan du skickar den till Time Series Insights. 

## <a name="solution-architecture"></a>Lösningsarkitekturen

Du kommer att koppla Time Series Insights till Azure Digitals flätar genom sökvägen nedan.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="En vy över Azure-tjänster i ett scenario från slut punkt till slut punkt, som markerar Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Förutsättningar

* Du behöver en Azure Digitals-instans som du kan uppdatera dubbel information några gånger för att se de data som spåras i Time Series Insights. 
    * Om du inte har någon följer du självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md) för att konfigurera en digital Azure-instans och en virtuell IoT-enhet för att generera dubbla förändringar.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Skapa en väg och filtrera till dubbla uppdaterings meddelanden

Azure Digitals dubbla instanser kan generera [dubbla uppdaterings händelser](how-to-interpret-event-data.md) när en dubbel status uppdateras. Du kommer att skapa en väg som dirigerar dessa uppdaterings händelser till en händelsehubben för vidare bearbetning.

Självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md) genom ett scenario där en termometer används för att uppdatera ett testattribut som är kopplat till rummets dubbla. Det här mönstret använder de dubbla uppdateringarna, i stället för att vidarebefordra telemetri från en IoT-enhet, vilket ger dig flexibilitet att ändra den underliggande data källan utan att behöva uppdatera Time Series Insights logiken.

1. Skapa ett namn område för händelsehubben, som tar emot händelser från din Azure Digital-instansen. Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: skapa en händelsehubben med Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Skapa en händelsehubb.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Skapa en [auktoriseringsregel](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Skapa en slut punkt för att länka ditt event Grid-ämne till Azure Digitals.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Skapa en väg i Azure Digitals flätas för att skicka dubbla uppdaterings händelser till din slut punkt. Filtret i den här vägen tillåter endast att dubbla uppdaterings meddelanden skickas till din slut punkt.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion 

Härnäst ska du skapa en Event Hubs utlöst funktion i en ny function-app, din Function-app från slut punkt till slut punkt ([*självstudie: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md)). Den här funktionen kommer att konvertera dessa uppdateringar från deras ursprungliga form som JSON-korrigeringsfiler till JSON-objekt som bara innehåller uppdaterade och tillagda värden från dina dubbla.

Mer information om hur du använder Event Hubs med Azure Functions finns i [*Azure Event Hubs-utlösare för Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

I din publicerade Function-app ersätter du funktions koden med följande kod.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

Härifrån skickar funktionen de JSON-objekt som skapas till en andra händelsehubben, som du kommer att ansluta till Time Series Insights.

## <a name="send-telemetry-to-an-event-hub"></a>Skicka telemetri till en Event Hub

Nu ska du skapa en andra Event Hub och konfigurera din funktion för att strömma utdata till den händelsehubben. Den här händelsehubben kommer sedan att anslutas till Time Series Insights.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: skapa en händelsehubben med Azure Portal*](../event-hubs/event-hubs-create.md).

1. Förbered din Event Hub-namnrymd och resurs gruppens namn från tidigare 

2. Skapa en händelsehubb
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Skapa en [auktoriseringsregel](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Konfigurera din funktion

Du måste ange en miljö variabel i din Function-app från tidigare, som innehåller din Event Hub-anslutningssträng.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Ange anslutnings strängen för Time Series Insights Event Hub

1. Hämta [anslutnings strängen för Event Hub](../event-hubs/event-hubs-get-connection-string.md) för de auktoriseringsregler som du skapade ovan för Time Series Insights Hub:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. I din Function-app skapar du en app-inställning som innehåller din anslutnings sträng:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Ange anslutnings sträng för dubblar Event Hub

1. Hämta [anslutnings strängen för Event Hub](../event-hubs/event-hubs-get-connection-string.md) för de auktoriseringsregler som du skapade ovan för den dubbla hubben.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. I din Function-app skapar du en app-inställning som innehåller din anslutnings sträng:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Skapa och ansluta en Time Series Insights-instans

Därefter ställer du in en Time Series Insights-instans för att ta emot data från den andra händelsehubben. Mer information om den här processen finns i [ *Självstudier: konfigurera en Azure Time Series Insights Gen2 PAYG-miljö*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. I Azure Portal börjar du skapa en Time Series Insights resurs. 
    1. Välj pris nivå för **PAYG (för hands version)** .
    2. Du måste välja ett Time Series-ID för den här miljön. Tids serie-ID: t kan innehålla upp till tre värden som du kan använda för att söka efter data i Time Series Insights. I den här självstudien kan du använda **$dtId**. Läs mer om att välja ett ID-värde i [*metod tips för att välja ett Time Series-ID*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Skapa Portal-UX för en Time Series Insights miljö. Pris nivån PAYG (för hands version) har valts och värdet för Time Series ID-egenskapen är $dtId":::

2. Välj **Nästa: händelse källa** och välj din Event Hubs information från ovan. Du måste också skapa en ny Event Hubs konsument grupp.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Skapa Portal-UX för en Time Series Insights miljö händelse källa. Du skapar en händelse källa med information om händelsehubben från ovan. Du skapar också en ny konsument grupp.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Börja skicka IoT-data till Azure Digitals, dubbla

För att kunna börja skicka data till Time Series Insights måste du börja uppdatera de digitala dubbla egenskaperna med att ändra data värden. Använd kommandot [AZ DT dubbla Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Om du använder den fullständiga självstudien för att hjälpa dig med miljö installationen kan du börja skicka simulerade IoT-data genom att köra `DeviceSimulator` projektet från Azures digitala direkt [*studie kurs: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md). Anvisningarna finns i avsnittet [*Konfigurera och köra simulering*](tutorial-end-to-end.md#configure-and-run-the-simulation) i självstudien.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisera dina data i Time Series Insights

Nu bör data flöda till Time Series Insights-instansen som är redo att analyseras. Följ stegen nedan för att utforska de data som kommer i.

1. Öppna Time Series Insights-instansen i Azure Portal. Gå till URL: en för Time Series Insights Explorer som visas i översikten.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Klicka på URL: en för Time Series Insights Explorer på fliken Översikt i din Time Series Insights-miljö":::

2. I Utforskaren ser du de tre dubblarna som visas till vänster. Klicka på **thermostat67**, Välj **patch_value**och klicka på **Lägg till**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Klicka på * * thermostat67 * *, Välj * * temperatur * * och klicka på * * Lägg till * *":::

3. Du bör nu se de första temperatur avläsningarna från din termostat, som du ser nedan. Samma temperatur läsning uppdateras för Room21 och floor1, och du kan visualisera dessa data strömmar i tandem.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Inledande temperatur data visas i TSD-Utforskaren. Det är en rad med slumpmässiga värden mellan 68 och 85":::

4. Om du tillåter att simuleringen körs mycket längre, ser visualiseringen ut ungefär så här:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Temperatur data för varje snöre visas i tre parallella rader med olika färger.":::

## <a name="next-steps"></a>Nästa steg

De digitala multiplarna lagras som standard som en platt hierarki i Time Series Insights, men de kan berikas med modell information och en hierarki med flera nivåer för organisationen. Läs mer om den här processen genom att läsa: 

* [*Självstudie: definiera och tillämpa en modell*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med modell-och diagram data som redan finns lagrade i Azure Digitals. Läs mer om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram i följande referenser:

* [*Anvisningar: hantera en digital, dubbel*](./how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](./how-to-query-graph.md)