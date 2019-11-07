---
title: Felsök återställning efter fel i haveri beredskap för virtuella VMware-datorer med Azure Site Recovery
description: I den här artikeln beskrivs olika sätt att felsöka problem med återställning efter fel och skydd under haveri beredskap för virtuella VMware-datorer till Azure med Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693147"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Felsöka återställning efter fel till lokala enheter från Azure

Den här artikeln beskriver hur du felsöker problem som kan uppstå när du återställer virtuella Azure-datorer till din lokala VMware-infrastruktur efter redundansväxlingen till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Återställning efter fel omfattar huvudsakligen två huvud steg. För det första steget efter redundansväxlingen måste du skydda virtuella Azure-datorer till lokalt så att de börjar replikera. Det andra steget är att köra en redundansväxling från Azure för att växla tillbaka till din lokala plats.

## <a name="troubleshoot-reprotection-errors"></a>Felsöka återskydds fel

Det här avsnittet innehåller information om vanliga återskydds fel och hur du korrigerar dem.

### <a name="error-code-95226"></a>Felkod 95226

**Det gick inte att återaktivera skydd eftersom den virtuella Azure-datorn inte kunde komma åt den lokala konfigurations servern.**

Felet uppstår när:

* Den virtuella Azure-datorn kan inte ansluta till den lokala konfigurations servern. Den virtuella datorn kan inte identifieras och registreras på konfigurations servern.
* InMage Scout Application Service körs inte på den virtuella Azure-datorn efter redundansväxlingen. Tjänsten krävs för kommunikation med den lokala konfigurations servern.

Gör så här för att lösa problemet:

* Kontrol lera att Azure VM Network gör det möjligt för den virtuella Azure-datorn att kommunicera med den lokala konfigurations servern. Du kan antingen konfigurera en plats-till-plats-VPN-anslutning till ditt lokala data Center eller konfigurera en Azure ExpressRoute-anslutning med privat peering på det virtuella Azure-DATORns virtuella nätverk.
* Om den virtuella datorn kan kommunicera med den lokala konfigurations servern loggar du in på den virtuella datorn. Kontrol lera sedan tjänsten InMage Scout Application. Om du ser att den inte körs startar du tjänsten manuellt. Kontrol lera att tjänstens start typ är inställd på **Automatisk**.

### <a name="error-code-78052"></a>Felkod 78052

**Det gick inte att slutföra skyddet för den virtuella datorn.**

Det här problemet kan inträffa om det redan finns en virtuell dator med samma namn på huvud mål servern som du växlar tillbaka till.

Gör så här för att lösa problemet:

* Välj en annan huvud mål server på en annan värd så att skyddet skapar datorn på en annan värd, där namnen inte kolliderar.
* Du kan också använda vMotion för att flytta huvud målet till en annan värd där namn kollisionen inte inträffar. Om den befintliga virtuella datorn är en lösa dator byter du namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.


### <a name="error-code-78093"></a>Felkod 78093

**Den virtuella datorn körs inte, i ett låst tillstånd eller är inte tillgänglig.**

Gör så här för att lösa problemet:

För att skydda en misslyckad virtuell dator måste den virtuella Azure-datorn köras så att mobilitets tjänsten registrerar sig för den lokala konfigurations servern och kan börja replikeras genom att kommunicera med processervern. Om datorn är i ett felaktigt nätverk eller inte körs (svarar inte eller stängs) kan konfigurations servern inte komma åt mobilitets tjänsten på den virtuella datorn för att börja återaktivering.

* Starta om den virtuella datorn så att den kan börja kommunicera igen lokalt.
* Starta om skydds jobbet när du har startat den virtuella Azure-datorn.

### <a name="error-code-8061"></a>Felkod 8061

**Det går inte att komma åt data lagret från ESXi-värden.**

Kontrol lera [kraven för huvud mål och data lager som stöds](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsök failback-fel

I det här avsnittet beskrivs vanliga fel som kan uppstå under återställning efter fel.

### <a name="error-code-8038"></a>Felkod 8038

**Det gick inte att öppna den lokala virtuella datorn på grund av felet.**

Det här problemet uppstår när den lokala virtuella datorn tas upp på en värd som inte har tillräckligt med minne. 

Gör så här för att lösa problemet:

* Etablera mer minne på ESXi-värden.
* Dessutom kan du använda vMotion för att flytta den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
