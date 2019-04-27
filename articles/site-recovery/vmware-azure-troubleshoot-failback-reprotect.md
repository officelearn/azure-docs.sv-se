---
title: Felsöka växla tillbaka till den lokala under VMware VM-katastrofåterställning till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskrivs olika sätt att felsöka problem med återställning efter fel och återaktiveringen av skyddet under VMware VM-katastrofåterställning till Azure med Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565607"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Felsöka återställning efter fel till den lokala från Azure

Den här artikeln beskriver hur du felsöker problem som kan uppstå när du växlar tillbaka Azure virtuella datorer till din lokala VMware-infrastruktur, efter redundansväxlingen till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Återställning efter fel innebär i stort sett två steg. För det första steget måste efter en redundansväxling, du återaktivera skyddet av virtuella Azure-datorer till den lokala så att de börjar replikera. Det andra steget är att köra en redundans från Azure för att växla tillbaka till din lokala plats.

## <a name="troubleshoot-reprotection-errors"></a>Felsöka återaktivering av skydd

Det här avsnittet beskriver vanliga återaktiveringen av skyddet fel och hur du åtgärdar dem.

### <a name="error-code-95226"></a>Felkod 95226

**Det gick inte att återaktivera skydd eftersom Azure-dator inte kunde komma åt den lokala konfigurationsservern.**

Det här felet uppstår när:

* Azure VM kan inte nå den lokala konfigurationsservern. Den virtuella datorn kan inte identifieras och registrerad på konfigurationsservern.
* InMage Scout application service körs inte på den virtuella Azure-datorn efter redundans. Tjänsten krävs för kommunikation med den lokala konfigurationsservern.

Gör så här för att lösa problemet:

* Kontrollera att det Virtuella Azure-nätverket tillåter virtuella Azure-datorn att kommunicera med den lokala konfigurationsservern. Du kan konfigurera en plats-till-plats VPN-anslutning till ditt lokala datacenter, eller så kan du konfigurera en Azure ExpressRoute-anslutning med privat peering i det virtuella nätverket för den virtuella Azure-datorn.
* Om den virtuella datorn kan kommunicera med den lokala konfigurationsservern kan du logga in på den virtuella datorn. Kontrollera sedan tjänsten InMage Scout. Om du ser att den inte körs kan du starta tjänsten manuellt. Kontrollera att tjänsten startar typ har angetts till **automatisk**.

### <a name="error-code-78052"></a>Felkod 78052

**Att det gick inte slutföra skyddet för den virtuella datorn.**

Det här problemet kan inträffa om det finns redan en virtuell dator med samma namn på huvudmålservern som du växlar tillbaka.

Gör så här för att lösa problemet:

* Välj en annan huvudmålserver på en annan värd så att återaktivering av skydd skapar datorn på en annan värd, där namnen inte står i konflikt.
* Du kan också använda vMotion för att flytta Huvudmålet till en annan värd där namnet kollision inte sker. Om den befintliga virtuella datorn är en avvikande dator, Byt namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.


### <a name="error-code-78093"></a>Felkod 78093

**Den virtuella datorn körs inte i ett låst tillstånd och inte kan nås.**

Gör så här för att lösa problemet:

Om du vill återaktivera skyddet en redundansväxlade virtuella datorn, måste du köra Azure VM så att Mobilitetstjänsten registreras med configuration server lokalt och kan börja replikera genom att kommunicera med processervern. Om datorn är i ett felaktigt nätverk eller inte körs (inte svarar eller Stäng av), kan inte konfigurationsservern nå Mobilitetstjänsten på den virtuella datorn ska börja återaktivera skyddet.

* Starta om den virtuella datorn så att den kan starta kommunicera tillbaka lokalt.
* Starta om jobbet återaktivering av skydd när du startar den virtuella Azure-datorn.

### <a name="error-code-8061"></a>Felkod 8061

**Databasen kan inte nås från ESXi-värden.**

Kontrollera den [master target-krav och datalager som stöds](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsöka för återställning efter fel

Det här avsnittet beskrivs vanliga fel som kan uppstå under återställning efter fel.

### <a name="error-code-8038"></a>Felkod 8038

**Det gick inte att ta fram den lokala virtuella datorn på grund av felet.**

Det här problemet inträffar när en lokal virtuell dator tas på en värd som inte har tillräckligt med minne som har etablerats. 

Gör så här för att lösa problemet:

* Etablera mer minne på ESXi-värden.
* Du kan dessutom använda vMotion för att flytta den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
