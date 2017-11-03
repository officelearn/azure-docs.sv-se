---
title: Hantera DNS-zoner i Azure DNS - Azure CLI 2.0 | Microsoft Docs
description: "Du kan hantera DNS-zoner som använder Azure CLI 2.0. Den här artikeln visar hur du uppdatera, ta bort och skapa DNS-zoner på Azure DNS."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
ms.openlocfilehash: 1414baf9e51d648cc3a46c4f8635040b4d276910
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Hur du hanterar DNS-zoner i Azure DNS använder Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)


Den här guiden visar hur du hanterar DNS-zoner med hjälp av plattformsoberoende Azure CLI, som är tillgänglig för Windows, Mac och Linux. Du kan också hantera DNS-zoner med [Azure PowerShell](dns-operations-dnszones.md) eller Azure-portalen.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering.
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering.

## <a name="introduction"></a>Introduktion

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Konfigurera Azure CLI 2.0 för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Installera den senaste versionen av Azure CLI 2.0, tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna ett konsolfönster och autentisera med dina autentiseringsuppgifter. Mer information finns i Logga in i Azure från Azure CLI

```
az login
```

### <a name="select-the-subscription"></a>Välja prenumerationen

Kontrollera prenumerationerna för kontot.

```
az account list
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Få hjälp

Alla CLI 2.0-kommandon som är relaterade till Azure DNS börja med `az network dns`. Hjälp är tillgänglig för varje kommando med hjälp av den `--help` alternativet (kort form `-h`).  Exempel:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill ha hjälp, så gå till `az network dns zone create -h`.

I följande exempel skapas en DNS-zon som kallas *contoso.com* i resursgruppen kallas *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Skapa en DNS-zon med taggar

I följande exempel visas hur du skapar en DNS-zon med två [Azure Resource Manager taggar](dns-zones-records.md#tags), *projekt = demo* och *env = test*, med hjälp av den `--tags` parameter (kort form `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Hämta en DNS-zon

Använd för att hämta en DNS-zon `az network dns zone show`. Om du vill ha hjälp, så gå till `az network dns zone show --help`.

I följande exempel returneras DNS-zonen *contoso.com* och dess associerade data från resursgruppen *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Följande exempel är svaret.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Observera att DNS-poster inte returneras av `az network dns zone show`. Använd om du vill visa en lista med DNS-poster `az network dns record-set list`.


## <a name="list-dns-zones"></a>Lista över DNS-zoner

För att räkna upp DNS-zoner, Använd `az network dns zone list`. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

Anger resursgruppens namn visas endast de zonerna i resursgruppen:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Om du utesluter resursgruppen visar en lista över alla zoner i prenumerationen:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

En DNS-zon resurs kan ändras med hjälp av `az network dns zone update`. Om du vill ha hjälp, så gå till `az network dns zone update --help`.

Det här kommandot uppdaterar inte någon DNS-postuppsättningar i zonen (se [hur du hanterar DNS-poster](dns-operations-recordsets-cli.md)). Den används endast för att uppdatera egenskaper för resursen zonen. Dessa egenskaper är för tillfället begränsad till den [Azure Resource Manager ”-taggar'](dns-zones-records.md#tags) för zonen resursen.

I följande exempel visas hur du uppdaterar taggarna i en DNS-zon. De befintliga taggarna ersättas med det angivna värdet.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

DNS-zoner kan tas bort med `az network dns zone delete`. Om du vill ha hjälp, så gå till `az network dns zone delete --help`.

> [!NOTE]
> En DNS-zon också tar du bort DNS-poster i zonen. Den här åtgärden kan inte ångras. Om DNS-zonen misslyckas tjänster med hjälp av zonen när zonen tas bort.
>
>För att skydda mot oavsiktlig zonen borttagning finns [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Det här kommandot uppmanas att bekräfta. Det valfria `--yes` växeln förhindrar det här meddelandet.

I följande exempel visas hur du tar bort zonen *contoso.com* från resursgruppen *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hantera uppsättningar av poster och poster](dns-getstarted-create-recordset-cli.md) i DNS-zonen.

Lär dig hur du [Delegera din domän till Azure DNS](dns-domain-delegation.md).

