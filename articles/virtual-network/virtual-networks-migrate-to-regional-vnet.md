---
title: Migrera virtuellt Azure-nätverk (klassiska) från en tillhörighetsgrupp till en region | Microsoft Docs
description: Lär dig hur du migrerar ett virtuellt nätverk (klassiska) från en tillhörighetsgrupp till en region.
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
ms.openlocfilehash: 1fca7f6165998b95254f841638cf8bcbc1fb352d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792162"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrera ett virtuellt nätverk (klassiska) från en tillhörighetsgrupp till en region

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen.

Tillhörighetsgrupper se till att resurser som skapas i samma tillhörighetsgrupp fysiskt finns på servrar som är nära varandra att aktivera dessa resurser kommunicera snabbare. Tidigare var tillhörighetsgrupper ett krav för att skapa virtuella nätverk (klassiskt). Då gick tjänsten network manager som hanteras av virtuellt nätverk (klassiskt) fungerar bara i en uppsättning fysiska servrar eller skalningsenhet. Arkitektur förbättringar har ökat omfånget för nätverkshantering till en region.

På grund av förbättringarna arkitektur finns tillhörighetsgrupper inte längre rekommenderas eller krävs för virtuella nätverk (klassiska). Användningen av tillhörighetsgrupper för virtuella nätverk (klassiskt) ersätts av regioner. Virtuella nätverk (klassiskt) som är associerade med regioner kallas regionala virtuella nätverk.

Vi rekommenderar att du inte använder tillhörighetsgrupper i allmänhet. Utöver det virtuella nätverk kravet tillhörighetsgrupper har också viktigt att använda för att säkerställa resurser, till exempel beräkning (klassisk) och lagring (klassisk), placerades nära varandra. Men med den aktuella Azure nätverksarkitekturen är kraven placering inte längre behövs.

> [!IMPORTANT]
> Även om det fortfarande är tekniskt möjligt att skapa ett virtuellt nätverk som är associerad med en tillhörighetsgrupp, finns det inga tvingande skäl att göra detta. Många funktioner för virtuella nätverk, till exempel nätverkssäkerhetsgrupper, är bara tillgängliga när du använder ett regionalt virtuellt nätverk och är inte tillgängliga för virtuella nätverk som är associerade med tillhörighetsgrupper.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Redigera konfigurationsfilen för nätverk

1. Exportera konfigurationsfilen nätverk. Information om hur du exporterar en konfigurationsfil för nätverk med PowerShell eller Azure-kommandoradsgränssnittet (CLI) 1.0 finns [konfigurera ett virtuellt nätverk med en konfigurationsfil för nätverket](virtual-networks-using-network-configuration-file.md#export).
2. Redigera konfigurationsfilen nätverket ersätter **AffinityGroup** med **plats**. Du anger en Azure [region](https://azure.microsoft.com/regions) för **plats**.
   
   > [!NOTE]
   > Den **plats** är den region som du angav för tillhörighetsgruppen som är kopplad till ditt virtuella nätverk (klassiska). Om ditt virtuella nätverk (klassiska) som är associerad med en tillhörighetsgrupp som finns i västra USA när du migrerar, till exempel din **plats** måste peka på västra USA. 
   > 
   > 
   
    Redigera följande rader i konfigurationsfilen nätverket där du ersätter värdena med dina egna: 
   
    **Gammalt värde:** \<VirtualNetworkSitename = ”VNetUSWest” AffinityGroup = ”VNetDemoAG”\> 
   
    **Nytt värde:** \<VirtualNetworkSitename = ”VNetUSWest” sökväg = ”USA, västra”\>
3. Spara dina ändringar och [importera](virtual-networks-using-network-configuration-file.md#import) nätverkskonfigurationen till Azure.

> [!NOTE]
> Migreringen medför några driftavbrott till dina tjänster.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Vad du gör om du har en virtuell dator (klassisk) i en tillhörighetsgrupp
Virtuella datorer (klassiskt) som används för närvarande i en tillhörighetsgrupp behöver inte tas bort från tillhörighetsgruppen. När en virtuell dator har distribuerats, har den distribuerats till en enda skalningsenhet. Tillhörighetsgrupper kan begränsa antalet tillgängliga VM-storlekar för en ny VM-distribution, men en befintlig virtuell dator som har distribuerats är redan begränsad till uppsättningen med VM-storlekar som är tillgängliga i skalningsenheten där den virtuella datorn distribueras. Eftersom den virtuella datorn redan har distribuerats till en skalningsenhet har tar bort en virtuell dator från en tillhörighetsgrupp ingen effekt på den virtuella datorn.
