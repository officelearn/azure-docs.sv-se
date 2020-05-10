---
title: Filter för IP-anslutning till Azure IoT Hub | Microsoft Docs
description: Hur du använder IP-filtrering för att blockera anslutningar från vissa IP-adresser till Azure IoT Hub. Du kan blockera anslutningar från enskilda eller intervall med IP-adresser.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: b1550254e969e96fbc83c4c344189d414a8fa8d3
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995513"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar baserade på Azure IoT Hub. Ibland måste du uttryckligen ange de IP-adresser som enheter kan ansluta till som en del av din säkerhets konfiguration. Med funktionen *IP-filter* kan du konfigurera regler för att avvisa eller acceptera trafik från vissa IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användnings fall när det är användbart att blockera IoT Hub slut punkter för vissa IP-adresser:

* Din IoT Hub bör endast ta emot trafik från ett visst intervall med IP-adresser och förkasta allt annat. Du använder till exempel din IoT Hub med [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) för att skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av IoT Hub-administratören.

## <a name="how-filter-rules-are-applied"></a>Hur filter regler tillämpas

IP-filter regler tillämpas på IoT Hub service nivå. Därför gäller IP-filter reglerna för alla anslutningar från enheter och backend-appar med valfritt protokoll som stöds.

Alla anslutnings försök från en IP-adress som matchar en IP-regel som avvisar i din IoT-hubb får en otillåten 401 status kod och beskrivning. Svars meddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställning

Som standard är **IP-filter** rutnätet i portalen för en IoT Hub tomt. Den här standardinställningen innebär att navet accepterar anslutningar från alla IP-adresser. Standardvärdet motsvarar en regel som accepterar IP-adressintervallet 0.0.0.0/0.

![IoT Hub inställningar för standard-IP-filter](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en regel för IP-filter

Om du vill lägga till en regel för IP-filter väljer du **+ Lägg till IP-filterlista**.

![Lägga till en IP-filterlista till en IoT-hubb](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

När du har valt **Lägg till IP-filterlista**fyller du i fälten.

![När du har valt Lägg till en filter regel för IP](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Ange ett **namn** för IP-filterlistan. Detta måste vara en unik, SKIFT läges okänslig, alfanumerisk sträng på upp till 128 tecken. Endast ASCII 7-bitars alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` godkänns.

* Ange en enskild IPv4-adress eller ett block med IP-adresser i CIDR-format. I CIDR-notation 192.168.100.0/22 representerar till exempel IPv4-adresserna 1024 från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **blockera** som **åtgärd** för IP-filterlistan.

När du har fyllt i fälten väljer du **Spara** för att spara regeln. En avisering visas som meddelar dig att uppdateringen pågår.

![Meddelande om att spara en regel för IP-filter](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Alternativet **Lägg till** är inaktiverat när du når Max gränsen på 10 IP-filter.

Om du vill redigera en befintlig regel väljer du de data som du vill ändra, gör ändringen och väljer **Spara** för att spara din redigering.

> [!NOTE]
> Att avvisa IP-adresser kan förhindra andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Device Explorer i portalen) från att samverka med IoT Hub.

> [!WARNING]
> Om du använder Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT-hubb där IP-filtrering är aktiverat, använder du det Event Hub-kompatibla namnet och slut punkten för din IoT Hub för att manuellt lägga till en [Event Hubs Stream-indata](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-inputs#stream-data-from-event-hubs) i ASA.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

Om du vill ta bort en IP-filterlista väljer du pappers korgs ikonen på raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

![Ta bort en IoT Hub IP-filterlista](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Hämta och uppdatera IP-filter med Azure CLI

Din IoT Hubs IP-filter kan hämtas och uppdateras via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Om du vill hämta aktuella IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Detta kommer att returnera ett JSON-objekt där dina befintliga IP-filter visas `properties.ipFilterRules` under nyckeln:

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

Om du vill lägga till ett nytt IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Om du vill ta bort ett befintligt IP-filter i IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observera att `<ipFilterIndexToRemove>` måste motsvara ordningen av IP-filter i din IoT Hubs `properties.ipFilterRules`.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Hämta och uppdatera IP-filter med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Din IoT Hubs IP-filter kan hämtas och anges via [Azure PowerShell](/powershell/azure/overview).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Uppdatera IP filter regler med REST

Du kan också hämta och ändra din IoT Hubs IP-filter med hjälp av Azure Resource providers REST-slutpunkt. Se `properties.ipFilterRules` i [createorupdate-metoden](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Regel utvärdering av IP-filter

IP-filter regler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

Om du till exempel vill ta emot adresser i intervallet 192.168.100.0/22 och avvisa allt annat ska den första regeln i rutnätet acceptera adress intervallet 192.168.100.0/22. Nästa regel ska avvisa alla adresser med intervallet 0.0.0.0/0.

Du kan ändra ordningen på dina IP filter-regler i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och använda dra och släpp.

Klicka på **Spara**om du vill spara din nya regel ordning för IP-filter.

![Ändra ordning på IoT Hub IP-filter regler](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)
* [IoT Hub mått](iot-hub-metrics.md)
