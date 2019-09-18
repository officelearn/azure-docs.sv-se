---
title: Migrera ett virtuellt Azure-nätverk (klassiskt) från en tillhörighets grupp till en region | Microsoft Docs
description: Lär dig hur du migrerar ett virtuellt nätverk (klassiskt) från en tillhörighets grupp till en region.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058399"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrera ett virtuellt nätverk (klassiskt) från en tillhörighets grupp till en region

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder distributions modellen för Resource Manager.

Tillhörighets grupper säkerställer att resurser som skapats inom samma tillhörighets grupp fysiskt finns på servrar som är nära varandra och som gör att dessa resurser kan kommunicera snabbare. Tidigare var tillhörighets grupper ett krav för att skapa virtuella nätverk (klassisk). Vid detta tillfälle kunde nätverks hanterarens tjänst som hanterade virtuella nätverk (klassisk) bara fungera inom en uppsättning fysiska servrar eller skalnings enheter. Förbättringar av arkitekturen har ökat nätverks hanteringens omfattning till en region.

Som ett resultat av dessa arkitektur förbättringar rekommenderas inte längre tillhörighets grupper, eller krävs för virtuella nätverk (klassisk). Användning av tillhörighets grupper för virtuella nätverk (klassisk) ersätts av regioner. Virtuella nätverk (klassiska) som är associerade med regioner kallas regionala virtuella nätverk.

Vi rekommenderar att du inte använder tillhörighets grupper i allmänhet. Förutom det virtuella nätverks kravet var tillhörighets grupper också viktiga att använda för att säkerställa att resurser, till exempel Compute (klassisk) och lagring (klassisk), var nära varandra. Men med den aktuella Azure-nätverks arkitekturen behövs inte dessa krav för placering längre.

> [!IMPORTANT]
> Även om det fortfarande är tekniskt möjligt att skapa ett virtuellt nätverk som är associerat med en tillhörighets grupp, finns det ingen övertygande anledning att göra det. Många funktioner för virtuella nätverk, till exempel nätverks säkerhets grupper, är bara tillgängliga när du använder ett regionalt virtuellt nätverk och inte är tillgängliga för virtuella nätverk som är associerade med tillhörighets grupper.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Redigera nätverks konfigurations filen

1. Exportera nätverks konfigurations filen. Information om hur du exporterar en nätverks konfigurations fil med PowerShell eller Azure kommando rads gränssnitt (CLI) 1,0 finns i [Konfigurera ett virtuellt nätverk med hjälp av en nätverks konfigurations fil](virtual-networks-using-network-configuration-file.md#export).
2. Redigera nätverks konfigurations filen och Ersätt **AffinityGroup** med **plats**. Du anger en Azure- [region](https://azure.microsoft.com/regions) för **platsen**.
   
   > [!NOTE]
   > **Platsen** är den region som du har angett för tillhörighets gruppen som är associerad med ditt virtuella nätverk (klassisk). Om ditt virtuella nätverk till exempel är associerat med en tillhörighets grupp som ligger i västra USA, måste din **plats** peka till västra USA. 
   > 
   > 
   
    Redigera följande rader i nätverks konfigurations filen och ersätt värdena med dina egna: 
   
    **Gammalt värde:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nytt värde:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Spara ändringarna och [Importera](virtual-networks-using-network-configuration-file.md#import) nätverks konfigurationen till Azure.

> [!NOTE]
> Den här migreringen orsakar inga avbrott i tjänsterna.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Vad du gör om du har en virtuell dator (klassisk) i en tillhörighets grupp
Virtuella datorer (klassiska) som för närvarande finns i en tillhörighets grupp behöver inte tas bort från tillhörighets gruppen. När en virtuell dator har distribuerats distribueras den till en enda skalnings enhet. Tillhörighets grupper kan begränsa uppsättningen tillgängliga VM-storlekar för en ny VM-distribution, men alla befintliga virtuella datorer som distribueras är redan begränsade till uppsättningen med VM-storlekar som är tillgängliga i den skalnings enhet där den virtuella datorn har distribuerats. Eftersom den virtuella datorn redan har distribuerats till en skalnings enhet har den virtuella datorn ingen påverkan på den virtuella datorn.
