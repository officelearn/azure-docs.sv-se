---
title: Migrera ett Azure-nätverk (klassisk) från en tillhörighetsgrupp till en region | Microsoft Docs
description: Lär dig hur du migrerar ett virtuellt nätverk (klassisk) från en tillhörighetsgrupp till en region.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648643"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrera ett virtuellt nätverk (klassisk) från en tillhörighetsgrupp till en region

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen.

Tillhörighetsgrupper se till att resurser som skapats i samma tillhörighetsgrupp fysiskt finns på servrar som ligger nära varandra så att dessa resurser kan kommunicera snabbare. Tidigare var tillhörighetsgrupper ett krav för att skapa virtuella nätverk (klassisk). Vid den tidpunkten, kan network manager-tjänsten som hanteras av virtuella nätverk (klassiska) fungerar endast i en uppsättning fysiska servrar eller skalningsenheten. Arkitektoniska förbättringar har ökat omfånget för nätverkshantering till en region.

Till följd av förbättringarna arkitektoniska är tillhörighetsgrupper inte längre rekommenderas eller krävs för virtuella nätverk (klassisk). Användningen av tillhörighetsgrupper för virtuella nätverk (klassisk) ersätts av regioner. Virtuella nätverk (klassisk) som är associerade med regioner kallas regionala virtuella nätverk.

Vi rekommenderar att du inte använder tillhörighetsgrupper i allmänhet. Bortsett från kraven på virtuellt nätverk, tillhörighetsgrupper har också viktigt att du använder för att säkerställa att resurser, till exempel beräkning (klassisk) och lagring (klassisk), placerades nära varandra. Men med den aktuella Azure-nätverk-arkitekturen behövs kraven placering inte längre.

> [!IMPORTANT]
> Även om det är fortfarande är tekniskt möjligt att skapa ett virtuellt nätverk som är associerad med en tillhörighetsgrupp, finns det ingen bra anledning till att göra detta. Många funktioner för virtuellt nätverk, till exempel nätverkssäkerhetsgrupperna, är bara tillgängliga när du använder ett regionalt virtuellt nätverk och är inte tillgängliga för virtuella nätverk som är associerade med tillhörighetsgrupper.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Redigera nätverkskonfigurationsfilen

1. Exportera nätverkskonfigurationsfilen. Läs hur du exporterar en nätverkskonfigurationsfil med PowerShell eller kommandoradsgränssnittet (CLI) 1.0 i [konfigurera ett virtuellt nätverk med en nätverkskonfigurationsfil](virtual-networks-using-network-configuration-file.md#export).
2. Redigera nätverkskonfigurationsfilen, ersätta **AffinityGroup** med **plats**. Du anger en Azure [region](https://azure.microsoft.com/regions) för **plats**.
   
   > [!NOTE]
   > Den **plats** är den region som du angav tillhörighetsgruppens som är associerad med ditt virtuella nätverk (klassisk). Exempel: om ditt virtuella nätverk (klassisk) som är associerad med en tillhörighetsgrupp som finns i västra USA, när du migrerar din **plats** måste peka på USA, västra. 
   > 
   > 
   
    Redigera följande rader i din nätverkskonfigurationsfilen, där du ersätter värdena med dina egna: 
   
    **Gammalt värde:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nytt värde:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Spara dina ändringar och [importera](virtual-networks-using-network-configuration-file.md#import) nätverkskonfigurationen till Azure.

> [!NOTE]
> Den här migreringen medför driftavbrott till dina tjänster.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Vad du gör om du har en virtuell dator (klassisk) i en tillhörighetsgrupp
Virtuella datorer (klassisk) som finns för närvarande i en tillhörighetsgrupp behöver inte tas bort från tillhörighetsgruppen. När en virtuell dator distribueras kan distribueras den till en enda skalningsenhet. Tillhörighetsgrupper kan minska antalet tillgängliga VM-storlekar för en ny VM-distribution, men alla befintliga virtuella datorer som har distribuerats är redan begränsad till uppsättningen med VM-storlekar som är tillgängliga i skalningsenheten där den virtuella datorn har distribuerats. Eftersom den virtuella datorn har redan distribuerats till en skalningsenhet, har ta bort en virtuell dator från en tillhörighetsgrupp ingen effekt på den virtuella datorn.
