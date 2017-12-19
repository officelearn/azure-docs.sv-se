---
title: Hantera DNS-zoner i Azure DNS - Azure-portalen | Microsoft Docs
description: "Du kan hantera DNS-zoner med Azure-portalen. Den här artikeln beskriver hur du uppdaterar, ta bort och skapa DNS-zoner på Azure DNS"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 69a509612e6204fc93dd42bf2fe69cb165b5777c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Hur du hanterar DNS-zoner i Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar DNS-zoner med hjälp av Azure portal. Du kan också hantera DNS-zoner med flera plattformar [Azure CLI](dns-operations-dnszones-cli.md) eller Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. Klicka på **Nytt > Nätverk >** på navmenyn och klicka sedan på **DNS-zon** för att öppna bladet Skapa DNS-zon.

    ![DNS-zon](./media/dns-operations-dnszones-portal/openzone650.png)

4. På bladet **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:


   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|contoso.com|Namnet på DNS-zonen|
   |**Prenumeration**|[Din prenumeration]|Välj en prenumeration att skapa DNS-zonen i.|
   |**Resursgrupp**|**Skapa ny:** contosoDNSRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups).|
   |**Plats**|Västra USA||

> [!NOTE]
> Resursgruppen refererar till platsen för resursgruppen och har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte.

## <a name="list-dns-zones"></a>Lista över DNS-zoner

I Azure-portalen går du till **fler tjänster** > **nätverk** > **DNS-zoner**. Varje DNS-zon är det egna resurs, information, till exempel antal post och namnservrar kan visas i den här vyn. Kolumnen **NAMNSERVRAR** är inte i standardvyn för att lägga till den Klicka **kolumner**väljer **namnservrar** och på **klar**.

![Visar en lista över DNS-zoner](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Navigera till en DNS-zon i portalen. På den **DNS-zonen** bladet, klickar du på **ta bort zon**. Du uppmanas att bekräfta att du vill ta bort DNS-zonen. En DNS-zon också tar bort alla poster i zonen.

## <a name="next-steps"></a>Nästa steg

Lär dig att arbeta med din DNS-zon och poster genom att besöka [Kom igång med Azure DNS med hjälp av Azure portal](dns-getstarted-portal.md).