---
title: Hantera DNS-zoner i Azure DNS - Azure portal | Microsoft-dokument
description: Du kan hantera DNS-zoner med Azure-portalen. I den hÃ¤r artikeln beskrivs hur du uppdaterar, tar bort och skapar DNS-zoner pÃ¥ Azure DNS
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 002f210048c18c6dd99dfb5981bacce8666ee563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936785"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Hantera DNS-zoner i Azure-portalen

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [Powershell](dns-operations-dnszones.md)
> * [Klassisk Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar dina DNS-zoner med hjälp av Azure-portalen. Du kan också hantera dina DNS-zoner med Azure [CLI](dns-operations-dnszones-cli.md) eller Azure [PowerShell.](dns-operations-dnszones.md)

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. På Hub-menyn navigerar du till **Skapa en resurs > nätverks- > DNS-zonen** för att öppna **bladet Skapa DNS-zon.**

    ![DNS-zon](./media/dns-operations-dnszones-portal/openzone650.png)

4. På bladet **Skapa DNS-zon** anger du följande värden och klickar sedan på **Skapa**:


   | **Inställning** | **Värde** | **Detaljer** |
   |---|---|---|
   |**Namn**|contoso.com|Namnet på DNS-zonen|
   |**Prenumeration**|[Din prenumeration]|Välj en prenumeration att skapa DNS-zonen i.|
   |**Resursgrupp**|**Skapa ny:** contosoDNSRG|Skapa en resursgrupp. Resursgruppens namn måste vara unikt inom den prenumeration du valde. Mer information om resursgrupper finns i översiktsartikeln [för Resurshanteraren.](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)|
   |**Location**|USA, västra||

> [!NOTE]
> Resursgruppen refererar till platsen för resursgruppen och har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte.

## <a name="list-dns-zones"></a>Lista DNS-zoner

I Azure-portalen navigerar du till **fler tjänster** > nätverk**DNS-zoner****Networking** > . Varje DNS-zon är sin egen resurs och information som antal postuppsättningar och namnservrar kan visas från den här vyn. Kolumnen **NAMNSERVRAR** finns inte i standardvyn. Om du vill lägga till den klickar du på **Kolumner,** väljer **Namnservrar**och sedan på **Klar**.

![lista DNS-zoner](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

Navigera till en DNS-zon i portalen. Klicka på **Ta bort zon**på **DNS-zonbladet** . Du uppmanas sedan att bekräfta att du vill ta bort DNS-zonen. Om du tar bort en DNS-zon tas också alla poster som finns i zonen bort.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du arbetar med dns-zonen och poster genom att besöka [Kom igång med Azure DNS med Azure-portalen](dns-getstarted-portal.md).