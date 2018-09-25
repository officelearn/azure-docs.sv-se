---
title: Hantera DNS-zoner i Azure DNS - Azure-portalen | Microsoft Docs
description: Du kan hantera DNS-zoner med Azure-portalen. Den här artikeln beskriver hur du uppdaterar, ta bort och skapa DNS-zoner i Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: a9325dff84635955600bc78687ec0156495ae893
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954389"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Så här hanterar du DNS-zoner i Azure portal

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar DNS-zoner med hjälp av Azure portal. Du kan också hantera dina DNS-zoner med flera plattformar [Azure CLI](dns-operations-dnszones-cli.md) eller Azure [PowerShell](dns-operations-dnszones.md).

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. På navmenyn och klicka **skapa en resurs > nätverk >** och klicka sedan på **DNS-zon** att öppna bladet skapa DNS-zon.

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

## <a name="list-dns-zones"></a>Lista DNS-zoner

I Azure-portalen går du till **fler tjänster** > **nätverk** > **DNS-zoner**. Varje DNS-zon är är det egna resurs, information, till exempel antalet postuppsättningar och DNS-servrar kan visas från den här vyn. Kolumnen **NAMNSERVRAR** är inte i standardvyn, att lägga till det klickar du på **kolumner**väljer **namnservrar** och klicka på **klar**.

![Visa en lista över DNS-zoner](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Navigera till en DNS-zon i portalen. På den **DNS-zon** bladet klickar du på **ta bort zonen**. Du uppmanas att bekräfta att du vill ta bort DNS-zonen. Tar bort en DNS-zon tas även bort alla poster i zonen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du arbetar med dina DNS-zon och -poster genom att besöka [Kom igång med Azure DNS med Azure-portalen](dns-getstarted-portal.md).