---
title: Felsökning av fel vid återställning av virtuella datorer i Azure till lokala VMware med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskrivs olika sätt att felsöka vanliga återställning och återaktivera skydd vid återställning till VMware från Azure med hjälp av Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941048"
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Felsöka återställning från Azure till VMware

Den här artikeln beskriver hur du felsöker problem som kan uppstå när du växlar tillbaka Azure virtuella datorer till lokala VMware-infrastrukturen, efter en redundansväxling till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Återställning innebär i stort sett två steg. För det första steget behöver när växling vid fel, du skyddar virtuella datorer i Azure till lokala så att de startar replikering. Det andra steget är att köra en växling vid fel från Azure för att växla tillbaka till den lokala platsen.

## <a name="troubleshoot-reprotection-errors"></a>Felsöka återaktivera skydd

Det här avsnittet beskrivs vanliga återaktivera skydd fel och åtgärda dem.

### <a name="error-code-95226"></a>Felkoden 95226

**Det gick inte att skydda igen eftersom den virtuella Azure-datorn inte kunde nå konfigurationsservern lokalt.**

Det här felet uppstår när:

* Den virtuella Azure-datorn kan inte nå konfigurationsservern lokalt. Den virtuella datorn kan inte identifieras och registrerade på konfigurationsservern.
* InMage Scout application service körs inte på Azure VM efter växling vid fel. Tjänsten krävs för kommunikation med konfigurationsservern lokalt.

Så här löser du problemet:

* Kontrollera att det Virtuella Azure-nätverket tillåter virtuella Azure-datorn kan kommunicera med konfigurationsservern lokalt. Du kan konfigurera en plats-till-plats VPN-anslutning till ditt lokala datacenter, eller så kan du konfigurera en Azure ExpressRoute-anslutning med privat peering på Azure VM virtuella nätverk.
* Om den virtuella datorn kan kommunicera med konfigurationsservern lokalt, kan du logga in på den virtuella datorn. Kontrollera sedan InMage Scout application service. Om du ser att den inte körs kan du starta tjänsten manuellt. Kontrollera att tjänsten starttypen är inställd på **automatisk**.

### <a name="error-code-78052"></a>Felkoden 78052

**Att det gick inte slutföra skyddet för den virtuella datorn.**

Det här problemet kan inträffa om det finns redan en virtuell dator med samma namn på huvudmålservern som du växlar tillbaka.

Så här löser du problemet:

* Välj en annan huvudmålserver på en annan värd så att återaktivera skydd skapar datorn på en annan värd, där namnen inte hamnar i konflikt.
* Du kan också använda vMotion flytta huvudmålservern till en annan värd där namnet kollisionen inte sker. Om den befintliga virtuella datorn är en avvikande dator, byta namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.


### <a name="error-code-78093"></a>Felkoden 78093

**Den virtuella datorn körs inte i låst tillstånd, eller är inte tillgänglig.**

Så här löser du problemet:

Om du vill skydda igen en virtuell dator misslyckades över måste virtuella Azure-datorn köras så att Mobilitetstjänsten registreras med configuration server lokalt och kan starta replikering genom att kommunicera med processervern. Om datorn är på en felaktig nätverks- eller inte körs (avstannat tillstånd eller stänga ned), kan inte konfigurationsservern nå Mobilitetstjänsten på den virtuella datorn ska börja återaktivera skydd.

* Starta om den virtuella datorn så att den kan starta kommunikation tillbaka lokalt.
* Starta om jobbet skydda igen när du startar den virtuella Azure-datorn.

### <a name="error-code-8061"></a>Felkoden 8061

**Databasen är inte tillgänglig från ESXi-värd.**

Kontrollera den [bemästra mål krav och datalager stöds](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsöka återställning efter fel

Det här avsnittet beskrivs vanliga fel som kan uppstå under återställning efter fel.

### <a name="error-code-8038"></a>Felkoden 8038

**Det gick inte att öppna den lokala virtuella datorn på grund av fel.**

Det här problemet inträffar när den lokala virtuella datorn tas på en värd som inte har tillräckligt med minne som har etablerats. 

Så här löser du problemet:

* Etablera mer minne på ESXi-värd.
* Du kan dessutom använda vMotion för att flytta den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
