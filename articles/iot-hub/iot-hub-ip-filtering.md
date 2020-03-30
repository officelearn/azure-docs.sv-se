---
title: IP-anslutningsfilter för Azure IoT Hub | Microsoft-dokument
description: Så här använder du IP-filtrering för att blockera anslutningar från specifika IP-adresser till azure IoT-hubben. Du kan blockera anslutningar från enskilda eller intervall av IP-adresser.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68414267"
---
# <a name="use-ip-filters"></a>Använda IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar som baseras på Azure IoT Hub. Ibland måste du uttryckligen ange IP-adresser från vilka enheter kan ansluta som en del av din säkerhetskonfiguration. *Med IP-filterfunktionen* kan du konfigurera regler för att avvisa eller acceptera trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall när det är användbart att blockera IoT Hub-slutpunkterna för vissa IP-adresser:

* Din IoT-hubb bör endast ta emot trafik från ett angivet intervall av IP-adresser och avvisa allt annat. Du använder till exempel din IoT-hubb med [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) för att skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.

* Du måste avvisa trafik från IP-adresser som har identifierats som misstänkta av IoT-hubbadministratören.

## <a name="how-filter-rules-are-applied"></a>Så här tillämpas filterregler

IP-filterreglerna tillämpas på IoT Hub-tjänstnivå. Därför gäller IP-filterreglerna för alla anslutningar från enheter och backend-appar som använder protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en avvisning av IP-regel i IoT-hubben får en obehörig 401-statuskod och beskrivning. Svarsmeddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställning

Som standard är **RUTNÄTET FÖR IP-filter** i portalen för en IoT-hubb tom. Den här standardinställningen innebär att hubben accepterar anslutningar från valfri IP-adress. Den här standardinställningen motsvarar en regel som accepterar IP-adressintervallet 0.0.0/0.

![Standardinställningar för IP-filter för IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägga till eller redigera en IP-filterregel

Om du vill lägga till en IP-filterregel väljer du **+ Lägg till IP-filterregel**.

![Lägga till en IP-filterregel i en IoT-hubb](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

När du har valt **Lägg till IP-filterregel**fyller du i fälten.

![När du har valt Lägg till en IP-filterregel](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Ange ett **namn** för IP-filterregeln. Detta måste vara en unik, skiftlägesokänslig, alfanumerisk sträng upp till 128 tecken lång. Endast ASCII 7-bitars alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.

* Ange en enda IPv4-adress eller ett block med IP-adresser i CIDR-notation. I CIDR-notation 192.168.100.0/22 representerar till exempel 1024 IPv4-adresser från 192.168.100.0 till 192.168.103.255.

* Välj **Tillåt** eller **Blockera** som **åtgärd** för IP-filterregeln.

När du har fyllt i fälten väljer du **Spara** för att spara regeln. Du ser en avisering som meddelar dig att uppdateringen pågår.

![Meddelande om hur du sparar en IP-filterregel](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Alternativet **Lägg till** är inaktiverat när du når maximalt 10 IP-filterregler.

Om du vill redigera en befintlig regel markerar du de data du vill ändra, gör ändringen och väljer sedan **Spara** för att spara redigeringen.

> [!NOTE]
> Att avvisa IP-adresser kan förhindra att andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Enhetsutforskaren i portalen) interagerar med IoT-hubben.

> [!WARNING]
> Om du använder Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT-hubb med IP-filtrering aktiverat använder du namnet och slutpunkten för din IoT-hubb i ASA-anslutningssträngen.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en IP-filterregel

Om du vill ta bort en IP-filterregel markerar du papperskorgen på den raden och väljer sedan **Spara**. Regeln tas bort och ändringen sparas.

![Ta bort en IP-filterregel för IoT Hub](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Hämta och uppdatera IP-filter med Azure CLI

Ip-filtren för IoT Hub kan hämtas och uppdateras via [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Om du vill hämta aktuella IP-filter för din IoT Hub kör du:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Då returneras ett JSON-objekt där dina befintliga `properties.ipFilterRules` IP-filter visas under nyckeln:

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

Om du vill lägga till ett nytt IP-filter för IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Om du vill ta bort ett befintligt IP-filter i IoT Hub kör du:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Observera `<ipFilterIndexToRemove>` att måste motsvara beställningen av IP-filter i `properties.ipFilterRules`IoT Hub.Note that must correspond to the ordering of IP filters in your IoT Hub's .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Hämta och uppdatera IP-filter med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ip-filtren för IoT Hub kan hämtas och ställas in via [Azure PowerShell](/powershell/azure/overview).

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

## <a name="update-ip-filter-rules-using-rest"></a>Uppdatera IP-filterregler med REST

Du kan också hämta och ändra IP-filtret för IoT Hub med hjälp av Azure Resource Provider's REST-slutpunkt. Se `properties.ipFilterRules` i [metoden createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Utvärdering av IP-filterregel

IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen avgör åtgärden acceptera eller avvisa.

Om du till exempel vill acceptera adresser i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i rutnätet acceptera adressintervallet 192.168.100.0/22. Nästa regel bör avvisa alla adresser med hjälp av intervallet 0.0.0.0/0.

Du kan ändra ordningen på IP-filterreglerna i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och använda dra och släpp.

Om du vill spara den nya IP-filterregelordningen klickar du på **Spara**.

![Ändra ordningen på ip-filterreglerna för IoT Hub](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)
* [IoT Hub-mått](iot-hub-metrics.md)
