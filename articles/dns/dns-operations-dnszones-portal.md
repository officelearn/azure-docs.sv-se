---
title: Manage DNS zones in Azure DNS - Azure portal | Microsoft Docs
description: You can manage DNS zones using the Azure portal. This article describes how to update, delete and create DNS zones on Azure DNS
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: allensu
ms.openlocfilehash: 5d4cc57c4cb5db7f04d604c8ccbc408df1a3e707
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211899"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>How to manage DNS Zones in the Azure portal

> [!div class="op_single_selector"]
> * [Portalen](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

This article shows you how to manage your DNS zones by using the Azure portal. You can also manage your DNS zones using the cross-platform [Azure CLI](dns-operations-dnszones-cli.md) or the Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. On the Hub menu, navigate to **Create a resource > Networking > DNS zone** to open the **Create DNS zone** blade.

    ![DNS-zon](./media/dns-operations-dnszones-portal/openzone650.png)

4. På bladet **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:


   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|contoso.com|Namnet på DNS-zonen|
   |**Prenumeration**|[Din prenumeration]|Välj en prenumeration att skapa DNS-zonen i.|
   |**Resursgrupp**|**Skapa ny:** contosoDNSRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Plats**|USA, västra||

> [!NOTE]
> Resursgruppen refererar till platsen för resursgruppen och har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte.

## <a name="list-dns-zones"></a>Lista DNS-zoner

In the Azure portal, navigate to **More services** > **Networking** > **DNS zones**. Each DNS zone is its own resource, and information such as number of record-sets and name servers are viewable from this view. The column **NAME SERVERS** is not in the default view. To add it, click **Columns**, select **Name servers**, and then click **Done**.

![listing DNS zones](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Navigate to a DNS zone in the portal. On the **DNS zone** blade, click **Delete zone**. You are then prompted to confirm you are wanting to delete the DNS zone. Deleting a DNS zone also deletes all records that are contained in the zone.

## <a name="next-steps"></a>Nästa steg

Learn how to work with your DNS Zone and records by visiting [Get started with Azure DNS using the Azure portal](dns-getstarted-portal.md).