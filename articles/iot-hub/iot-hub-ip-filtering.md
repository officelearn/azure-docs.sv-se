---
title: Azure IoT Hub IP-anslutningsfilter | Microsoft Docs
description: Så här använder IP-filtrering för blockerar anslutningar från specifika IP-adresser för till Azure IoT hub. Du kan blockera anslutningar från enskilda eller intervall av IP-adresser.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: cd382c0daff79b487f4ecae01ad852f6e57f3a25
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734257"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar som baseras på Azure IoT Hub. Ibland måste du uttryckligen ange IP-adresser som innehåller de enheter som kan ansluta som en del av din säkerhetskonfiguration. Den *IP-adressfilter* funktionen kan du konfigurera regler för avvisar eller tar emot trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall när det är bra att blockera IoT Hub-slutpunkter för vissa IP-adresser:

* IoT-hubben ska ta emot trafik från ett angivet intervall med IP-adresser och avvisa allt annat. Exempel: du använder IoT hub med [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av IoT hub-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på tjänstnivå IoT Hub. Därför gäller IP-filterreglerna för alla anslutningar från enheter och backend-appar med alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel i din IoT-hubb tar emot en obehörig 401 statuskoden och en beskrivning. Svarsmeddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portal för en IoT-hubb är tomt. Den här standardinställningen innebär att din hubb tar emot anslutningar från alla IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

![IoT Hub standardinställningarna IP-filter](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägg till eller redigera en IP-filterregeln

När du lägger till en IP-filterregeln uppmanas du att följande värden:

* En **Regelnamn för IP-filter** som måste vara en unik, skiftlägesokänslig, alfanumerisk sträng på högst 128 tecken. Endast de ASCII 7 bitar alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.

* Välj en **avvisa** eller **acceptera** som den **åtgärd** för IP-filterregeln.

* Ange en IPv4-adress eller ett block med IP-adresser i CIDR-notation. Exempel: i CIDR notation 192.168.100.0/22 representerar 1024 IPv4-adresser från 192.168.100.0 till 192.168.103.255.

![Lägg till en IP-filterregeln till en IoT hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

När du har sparat regeln kan se du en avisering om att uppdateringen pågår.

![Meddelande om hur du sparar en IP-filterregeln](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Den **Lägg till** alternativet inaktiveras när du når upp till 10 IP-filterreglerna.

Du kan redigera en befintlig regel genom att dubbelklicka på den rad som innehåller regeln.

> [!NOTE]
> Avvisa IP adresser kan förhindra att andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Device Explorer i portalen) interagera med IoT hub.

> [!WARNING]
> Om du använder Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT-hubb med IP-filtrering, Använd Event Hub-kompatibla namnet och slutpunkten för din IoT-hubb i anslutningssträngen ASA.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

Välj en eller flera regler i rutnätet och klicka på för att ta bort en IP-filterregeln **ta bort**.

![Ta bort en regel för IoT Hub IP-filter](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Hämta och uppdatera IP-filter med Azure CLI

Din IoT-hubb IP-filter kan hämtas och uppdateras genom [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest). 

Om du vill hämta den aktuella IP-filter för IoT-hubben, kör du:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Detta returnerar ett JSON-objekt där din befintliga IP-filter visas under den `properties.ipFilterRules` nyckel:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Om du vill lägga till ett nytt IP-filter för din IoT-hubb, kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Om du vill ta bort ett befintligt IP-filter i IoT Hub, kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observera att `<ipFilterIndexToRemove>` måste motsvara sorteringen av IP-filter i din IoT-hubb `properties.ipFilterRules`.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Hämta och uppdatera IP-filter med Azure PowerShell

Din IoT-hubb IP-filter kan hämtas och anges via [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azps-1.2.0). 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzureRmResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzureRmResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Uppdatera IP-filterreglerna med hjälp av REST

Du kan också hämta och ändra IP-adressfilter för din IoT-hubb med hjälp av Azure resource Provider REST-slutpunkt. Se `properties.ipFilterRules` i [metoden createorupdate](https://docs.microsoft.com/en-us/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

Om du vill acceptera adresserna i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i rutnätet godkänna adressintervallet 192.168.100.0/22. Nästa regel ska avvisa alla adresser med hjälp av adressintervallet 0.0.0.0/0.

Du kan ändra ordning på IP-filterreglerna i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och med dra och släpp.

Om du vill spara den nya IP-filter regel för turordning, klickar du på **spara**.

![Ändra ordning på din IoT Hub IP-filterreglerna](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)
* [IoT Hub-mått](iot-hub-metrics.md)