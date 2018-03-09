---
title: "Felsökning av fel vid återställning av virtuella datorer i Azure till lokala VMware med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskrivs olika sätt att felsöka vanliga återställning och återaktivera skydd vid återställning till VMware från Azure med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2017
ms.author: rajanaki
ms.openlocfilehash: 1c54ae96273880caede1f50f3a0705c41f15f26e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Felsöka återställning från Azure till VMware

Den här artikeln beskriver hur du felsöker problem som kan uppstå när du växlar tillbaka Azure virtuella datorer till lokala VMware-infrastrukturen, efter en redundansväxling till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Återställning innebär i stort sett två steg. När växling vid fel behöver du skyddar virtuella datorer i Azure till lokala, så att de startar replikering. Det andra steget är att köra en växling vid fel från Azure till växla tillbaka till den lokala platsen. 

## <a name="troubleshoot-reprotection-errors"></a>Felsöka återaktivera skydd

Det här avsnittet beskrivs vanliga återaktivera skydd fel och åtgärda dem.

### <a name="error-code-95226"></a>Felkoden 95226

**Det gick inte att skydda igen eftersom den virtuella Azure-datorn inte kunde nå konfigurationsservern lokalt.**

Det här felet uppstår när:

1. Den virtuella Azure-datorn kan inte nå konfigurationsservern lokalt. Den virtuella datorn kan identifieras och registrerade på konfigurationsservern. 
2. InMage Scout Application service körs inte på Azure VM efter växling vid fel. Tjänsten krävs för kommunikation med konfigurationsservern lokalt.

Så här löser du problemet:

1. Kontrollera att det Virtuella Azure-nätverket tillåter virtuella Azure-datorn kan kommunicera med konfigurationsservern lokalt. Anger en plats-till-plats-VPN till ditt lokala datacenter för att göra detta, eller konfigurera en ExpressRoute-anslutning med privat peering på Azure VM virtuella nätverk. 
2. Logga sedan in på den virtuella datorn och kontrollera 'InMage Scout Application Service, om den virtuella datorn kan kommunicera med konfigurationsservern lokalt. Om du ser att den inte körs starta tjänsten manuellt och kontrollera att starttypen för tjänsten är inställd på automatisk.

### <a name="error-code-78052"></a>Felkoden 78052

***Att det gick inte slutföra skyddet för den virtuella datorn.**

Detta kan inträffa om det finns redan en virtuell dator med samma namn på huvudmålservern som du växlar tillbaka.

Så här löser detta problem genom att göra följande:
1. Välj en annan huvudmålserver på en annan värd, så att återaktivera skydd skapar datorn på en annan värd, där namnen inte hamnar i konflikt. 
2. Du kan också vMotion huvudmålservern till en annan värd där namnet kollisionen inte sker. Om den befintliga virtuella datorn är en avvikande dator, byta namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.

### <a name="error-code-78093"></a>Felkoden 78093

**Den virtuella datorn körs inte i låst tillstånd, eller är inte tillgänglig.**

Om du vill skydda igen en misslyckad över VM, måste du köra Azure VM. Detta är så att mobilitetstjänsten registrerar med den konfiguration av server lokalt, och börja replikering genom att kommunicera med processervern. Om datorn är i ett felaktigt nätverk, eller inte körs (låst tillstånd eller avstängning), kan inte konfigurationsservern nå mobilitetstjänsten på den virtuella datorn ska börja återaktivera skydd. 

1. Starta om den virtuella datorn så att den kan starta kommunikation tillbaka lokalt.
2. Starta om jobbet skydda igen när du har startat den virtuella Azure-datorn

### <a name="error-code-8061"></a>Felkoden 8061

**Databasen är inte tillgänglig från ESXi-värd.**
 
Kontrollera den [bemästra mål krav och datalager stöds](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsöka återställning efter fel

Det här avsnittet beskrivs vanliga fel som kan uppstå under återställning efter fel.

### <a name="error-code-8038"></a>Felkoden 8038

**Det gick inte att öppna den lokala virtuella datorn på grund av fel**

Detta händer när den lokala virtuella datorn tas på en värd som inte har tillräckligt med minne som har etablerats. Så här löser du problemet:

1. Etablera mer minne på ESXi-värd.
2. Du kan dessutom vMotion den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
