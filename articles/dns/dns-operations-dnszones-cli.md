---
title: Manage DNS zones in Azure DNS - Azure CLI | Microsoft Docs
description: You can manage DNS zones using Azure CLI. This article shows how to update, delete and create DNS zones on Azure DNS.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: allensu
ms.openlocfilehash: e1a3c401de32beb9757011ac306443334da8b867
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211927"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>How to manage DNS Zones in Azure DNS using the Azure CLI

> [!div class="op_single_selector"]
> * [Portalen](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


This guide shows how to manage your DNS zones by using the cross-platform Azure CLI, which is available for Windows, Mac and Linux. You can also manage your DNS zones using [Azure PowerShell](dns-operations-dnszones.md) or the Azure portal.

This guide specifically deals with Public DNS zones. For information on using Azure CLI to manage Private Zones in Azure DNS, see [Get started with Azure DNS Private Zones using Azure CLI](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Introduktion

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Konfigurera Azure CLI för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Installera den senaste versionen av Azure CLI. Den finns tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna ett konsolfönster och autentisera med dina autentiseringsuppgifter. Mer information finns i [Logga in i Azure från Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

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

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Optional: To install/use Azure DNS Private Zones feature
The Azure DNS Private Zone feature is available via an extension to the Azure CLI. Installera dns-tillägget för Azure CLI 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper definierar en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Få hjälp

All Azure CLI commands relating to Azure DNS start with `az network dns`. Help is available for each command using the `--help` option (short form `-h`).  Exempel:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill ha hjälp, så gå till `az network dns zone create -h`.

The following example creates a DNS zone called *contoso.com* in the resource group called *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>To create a DNS zone with tags

The following example shows how to create a DNS zone with two [Azure Resource Manager tags](dns-zones-records.md#tags), *project = demo* and *env = test*, by using the `--tags` parameter (short form `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Get a DNS zone

To retrieve a DNS zone, use `az network dns zone show`. Om du vill ha hjälp, så gå till `az network dns zone show --help`.

The following example returns the DNS zone *contoso.com* and its associated data from resource group *MyResourceGroup*. 

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

Det här kommandot uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets-cli.md)). Det används endast för att uppdatera zonresursens egenskaper. These properties are currently limited to the [Azure Resource Manager 'tags'](dns-zones-records.md#tags) for the zone resource.

The following example shows how to update the tags on a DNS zone. The existing tags are replaced by the value specified.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Du kan ta bort DNS-zoner med `az network dns zone delete`. Om du vill ha hjälp, så gå till `az network dns zone delete --help`.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Det här kommandot uppmanar dig att bekräfta. Det valfria `--yes`-växeln utelämnar det här meddelandet.

The following example shows how to delete the zone *contoso.com* from resource group *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Nästa steg

Learn how to [manage record sets and records](dns-getstarted-create-recordset-cli.md) in your DNS zone.

Learn how to [delegate your domain to Azure DNS](dns-domain-delegation.md).

