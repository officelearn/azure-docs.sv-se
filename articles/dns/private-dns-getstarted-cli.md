---
title: Kom igång med Azure DNS Private Zones med Azure CLI 2.0 | Microsoft Docs
description: Läs om hur du skapar en privat DNS-zon och en DNS-post i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med Azure CLI 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191457"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Komma igång med Azure DNS Private Zones med Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

Den här artikeln visar hur du skapar din första privata DNS-zon och DNS-post med hjälp av plattformsoberoende Azure CLI 2.0, som är tillgängligt för Windows, Mac och Linux. Du kan också utföra de här stegen med Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Vi kallar dessa ”virtuella lösningsnätverk”.  Du kan även ange ett virtuellt nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller förstörs.  Vi kallar detta ett ”virtuellt registreringsnätverk”.

Dessa instruktioner förutsätter att du redan har installerat och loggat in på Azure CLI 2.0 och även installerat det nödvändiga CLI-tillägget som stöder privata zoner. Mer information finns i [Hantera DNS-zoner med hjälp av Azure CLI 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Installera/använda funktionen för Azure DNS Private Zones (allmänt tillgänglig förhandsversion)
Funktionen för privata Azure DNS-zoner släpps i en allmänt tillgänglig förhandsversion via ett tillägg till Azure CLI. Installera dns-tillägget för Azure CLI 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som ska innehålla DNS-zonen innan du skapar DNS-zonen. Nedan visas kommandot.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

Du skapar en privat DNS-zon med kommandot `az network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `az network dns zone create --help`.

I följande exempel skapas en privat DNS-zon med namnet *contoso.local* i resursgruppen *MyResourceGroup* och görs tillgänglig för (länkad till) det virtuella nätverket *MyAzureVnet* med parametern resolution-vnets. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

OBS! I exemplet ovan tillhör det virtuella nätverket ”MyAzureVnet” samma resursgrupp och prenumeration som den privata zonen. Om du behöver länka ett virtuellt nätverk som tillhör en annan resursgrupp eller prenumeration måste du ange fullständigt Azure Resource Manager-ID, istället för enbart det virtuella nätverksnamnet, för parametern --resolution-vnets. 

Om du vill att Azure ska skapa värdnamnsposter i zonen automatiskt använder du parametern *registration-vnets* istället för *resolution-vnets*.  Registrering av virtuella nätverk är automatiskt aktiverad för lösning.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `az network dns record-set [record type] add-record`. Mer information, om exempelvis A-poster, finns i `azure network dns record-set A add-record --help`.

I följande exempel skapas en post med det relativa namnet ip1 i resursgruppen MyResourceGroup i DNS-zonen contoso.local. Postuppsättningens fullständigt kvalificerade namn är ip1.contoso.local. Posttypen är A och IP-adressen är 10.0.0.1.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Information om andra posttyper, postuppsättningar med fler än en post, alternativa TTL-värden och ändring av befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Hämta en privat Azure DNS-zon

Använd `az network dns zone show` om du vill hämta en privat DNS-zon. Om du vill ha hjälp, så gå till `az network dns zone show --help`.

I följande exempel returneras DNS-zonen *contoso.local* och dess associerade data från resursgruppen *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

Följande exempel är svaret.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Observera att DNS-poster inte returneras av `az network dns zone show`. Använd `az network dns record-set list` om du vill lista DNS-poster.


## <a name="list-dns-zones"></a>Lista DNS-zoner

Använd `az network dns zone list` om du vill räkna upp DNS-zoner. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

När du anger resursgrupp så listas bara zonerna i resursgruppen:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Om du utesluter resursgruppen listas alla zoner i prenumerationen:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

Du kan göra ändringar i en DNS-zonresurs med `az network dns zone update`. Om du vill ha hjälp, så gå till `az network dns zone update --help`.

Det här kommandot uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets-cli.md)). Det används endast för att uppdatera zonresursens egenskaper. Du kan uppdatera de virtuella nätverk för registrering eller matchning som är länkade till en zon. 

I följande exempel visar vi hur du kan uppdatera det virtuella matchningsnätverk som är länkat till en privat DNS-zon. Det befintliga länkade virtuella matchningsnätverket ersätts med det nya virtuella nätverk som anges.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Du kan ta bort DNS-zoner med `az network dns zone delete`. Om du vill ha hjälp, så gå till `az network dns zone delete --help`.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Det här kommandot uppmanar dig att bekräfta. Det valfria `--yes`-växeln utelämnar det här meddelandet.

I det här exemplet visas hur du tar bort zonen *contoso.local* från resursgruppen *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Ta bort alla resurser
 
Så här tar du bort alla resurser som skapats i den här artikeln:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure DNS i [Översikt över Azure DNS](dns-overview.md).

Mer information om hur du hanterar DNS-zoner i Azure DNS finns i [Hantera DNS-zoner i Azure DNS med Azure CLI 2.0](dns-operations-dnszones-cli.md).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med Azure CLI 2.0](dns-operations-recordsets-cli.md).
