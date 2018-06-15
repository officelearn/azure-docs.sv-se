---
title: Felsöka fel vid återställning från Azure till lokala och skydda till Azure igen efteråt | Microsoft Docs
description: Den här artikeln beskriver hur du felsöker vanliga fel i misslyckas tillbaka till lokala från Azure och under skydda igen
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677415"
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Felsök fel rapporterades under återställning efter fel
Återställning innebär i stort sett två steg. En är att skydda virtuella datorer från Azure till lokala, det andra är att faktiskt utföra återställningen från Azure till lokala.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Felsök fel när en virtuell dator till lokalt, skydda efter växling vid fel
Får du ett av följande fel när du utför skyddar för en virtuell dator till Azure. Felsök genom att använda beskrivs stegen för respektive feltillstånd.


### <a name="error-code-95226"></a>Felkoden 95226

*Det gick inte att skydda igen eftersom den virtuella Azure-datorn inte kunde nå konfigurationsservern lokalt.*

Detta händer när 
1. Den virtuella Azure-datorn kunde kunde inte nå konfigurationsservern lokalt och därför inte identifieras och registrerade på konfigurationsservern. 
2. InMage Scout Application service på Azure-dator som ska köras för att kommunicera på lokala konfigurationsservern kanske inte körs efter växling vid fel.

Lös problemet
1. Du måste kontrollera att nätverket för den virtuella Azure-datorn är konfigurerad så att den virtuella datorn kan kommunicera med konfigurationsservern lokalt. Om du vill göra det, konfigurera en plats till plats-VPN tillbaka till ditt lokala datacenter eller konfigurera en ExpressRoute-anslutning med privat peering på det virtuella nätverket på Azure-datorn. 
2. Om du redan har ett datornätverk så att den virtuella Azure-datorn kan kommunicera med konfigurationsservern lokalt sedan logga in på den virtuella datorn och kontrollera 'InMage Scout Application Service ”. Om du märker att InMage Scout Application Service inte körs starta tjänsten manuellt och kontrollera att starttypen för tjänsten är inställd på automatisk.

### <a name="error-code-78052"></a>Felkoden 78052
Skapa nytt misslyckas med felmeddelandet: *gick inte att slutföra skyddet för den virtuella datorn.*

Detta kan inträffa på grund av två skäl
1. Den virtuella datorn du skydda är en Windows Server 2016. För närvarande det här operativsystemet stöds inte för återställning efter fel, men kommer snart att stödjas.
2. Det finns redan en virtuell dator med samma namn i bakgrunden du växla tillbaka till målservern.

Du kan välja en annan huvudmålserver på en annan värd, så att skydda igen skapar datorn på en annan värd, där namnen inte hamnar i konflikt för att lösa problemet. Du kan också vMotion huvudmålservern till en annan värd där namnet kollisionen inte sker. Om den befintliga virtuella datorn är en dator med avvikande, du kan bara byta namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.

### <a name="error-code-78093"></a>Felkoden 78093

*Den virtuella datorn körs inte i låst tillstånd, eller är inte tillgänglig.*

För att du skyddar en misslyckad över virtuella datorn tillbaka till lokala måste den virtuella Azure-datorn kör. Detta är så att mobilitetstjänsten registrerar med konfigurationsservern lokalt och börja replikering genom att kommunicera med processervern. Om datorn är i ett felaktigt nätverk eller inte körs (låst tillstånd eller avstängning), kan inte konfigurationsservern nå mobilitetstjänsten på den virtuella datorn ska börja skydda igen. Du kan starta om den virtuella datorn så att den kan starta kommunikation tillbaka lokalt. Starta om jobbet skydda igen när du har startat den virtuella Azure-datorn

### <a name="error-code-8061"></a>Felkoden 8061

*Databasen är inte tillgänglig från ESXi-värd.*

Referera till den [master mål förutsättningar](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) och [stöder datastores](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) för återställning efter fel


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Felsöka när du utför en återställning efter fel för en virtuell Azure-dator till lokalt
Får du ett av följande fel när du utför återställning efter fel för en virtuell Azure-dator till lokalt. Felsök genom att använda beskrivs stegen för respektive feltillstånd.

### <a name="error-code-8038"></a>Felkoden 8038

*Det gick inte att öppna den lokala virtuella datorn på grund av fel*

Detta händer när den lokala virtuella datorn tas på en värd som inte har tillräckligt med minne som har etablerats.

Lös problemet

1. Du kan etablera mer minne på ESXi-värd.
1. vMotion den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.