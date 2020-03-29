---
title: Felsök återställning av återställning efter fel i VMware VM-haveriberedskap med Azure Site Recovery
description: I den här artikeln beskrivs olika sätt att felsöka återställnings- och återskyddsproblem under VMware VM-haveriberedskap till Azure med Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498097"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Felsöka återställning efter fel till lokala enheter från Azure

I den här artikeln beskrivs hur du felsöker problem som kan uppstå när du misslyckas med att återställa virtuella Azure-datorer till din lokala VMware-infrastruktur, efter redundans till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Failback innebär i huvudsak två huvudsteg. För det första steget, efter redundans, måste du återrotera virtuella Azure-datorer till lokala så att de börjar replikera. Det andra steget är att köra en redundans från Azure för att växla tillbaka till din lokala webbplats.

## <a name="common-issues"></a>Vanliga problem

- Om du utför en skrivskyddad användare vCenter-identifiering och skyddar virtuella datorer, lyckas skyddet och redundansfungering fungerar. Under återbeskydd misslyckas redundansen eftersom datalagorna inte kan identifieras. Ett symptom är att datalagren inte visas under återbeskydd. LÃ¶s problemet genom att uppdatera vCenter-autentiseringsuppgifterna med ett lämpligt konto som har behörigheter och sedan fÃ¶rsÃ¶k igen fÃ¶rsÃ¶k om tillÃ¤nstÃ¤llning av jobbet.
- När du misslyckas med att återställa en virtuell Linux-dator och köra den lokalt kan du se att Network Manager-paketet har avinstallerats från datorn. Den här avinstallationen beror på att Network Manager-paketet tas bort när den virtuella datorn återställs i Azure.
- När en virtuell Linux-dator konfigureras med en statisk IP-adress och inte kan komma över till Azure, hämtas IP-adressen från DHCP. När du växlar över till lokalt fortsätter den virtuella datorn att använda DHCP för att hämta IP-adressen. Logga in manuellt på datorn och ställ sedan tillbaka IP-adressen till en statisk adress om det behövs. En virtuell Windows-dator kan hämta sin statiska IP-adress igen.
- Om du använder antingen ESXi 5.5 free edition eller vSphere 6 Hypervisor free edition lyckas redundans, men återställningen lyckas inte. Om du vill aktivera återställning efter fel uppgraderar du till något av programmets utvärderingslicens.
- Om du inte kan nå konfigurationsservern från processservern använder du Telnet för att kontrollera anslutningen till konfigurationsservern på port 443. Du kan också försöka pinga konfigurationsservern från processservern. En processserver bör också ha pulsslag när den är ansluten till konfigurationsservern.
- En Windows Server 2008 R2 SP1-server som är skyddad som en fysisk lokal server kan inte skickas tillbaka från Azure till en lokal plats.
- Du kan inte växla tillbaka under följande omständigheter:
    - Du har migrerat datorer till Azure. [Läs mer](migrate-overview.md#what-do-we-mean-by-migration).
    - Du har flyttat en virtuell dator till en annan resursgrupp.
    - Du har tagit bort den virtuella Azure-datorn.
    - Du har inaktiverat skyddet för den virtuella datorn.
    - Du har skapat den virtuella datorn manuellt i Azure. Datorn ska först ha skyddats lokalt och misslyckats med Azure innan du skyddar den.
    - Du kan bara misslyckas med en ESXi-värd. Du kan inte återställa virtuella datorer med tillbakabacka vmware eller fysiska servrar till Hyper-V-värdar, fysiska datorer eller VMware-arbetsstationer.


## <a name="troubleshoot-reprotection-errors"></a>Felsöka omskyddsfel

I det här avsnittet beskrivs vanliga återskyddsfel och hur du korrigerar dem.

### <a name="error-code-95226"></a>Felkod 95226

**Reprotect misslyckades eftersom den virtuella Azure-datorn inte kunde nå den lokala konfigurationsservern.**

Det här felet uppstår när:

* Den virtuella Azure-datorn kan inte nå den lokala konfigurationsservern. Den virtuella datorn kan inte identifieras och registreras på konfigurationsservern.
* Programtjänsten InMage Scout körs inte på den virtuella Azure-datorn efter redundans. Tjänsten behövs för kommunikation med den lokala konfigurationsservern.

Gör så här för att lösa problemet:

* Kontrollera att Azure VM-nätverket tillåter Azure VM att kommunicera med den lokala konfigurationsservern. Du kan antingen konfigurera ett VPN från plats till plats till ditt lokala datacenter eller konfigurera en Azure ExpressRoute-anslutning med privat peering i det virtuella nätverket för Den virtuella virtuella datorn i Azure.
* Om den virtuella datorn kan kommunicera med den lokala konfigurationsservern loggar du in på den virtuella datorn. Kontrollera sedan programtjänsten InMage Scout. Om du ser att den inte körs startar du tjänsten manuellt. Kontrollera att tjänstens starttyp är inställd **på Automatisk**.

### <a name="error-code-78052"></a>Felkod 78052

**Det gick inte att slutföra skyddet för den virtuella datorn.**

Det här problemet kan inträffa om det redan finns en virtuell dator med samma namn på huvudmålservern som du inte kan komma tillbaka till.

Gör så här för att lösa problemet:

* Välj en annan huvudmålserver på en annan värd så att återbeskydd skapar datorn på en annan värd, där namnen inte kolliderar.
* Du kan också använda vMotion för att flytta huvudmålet till en annan värd där namnkollisionen inte kommer att inträffa. Om den befintliga virtuella datorn är en herrelös dator byter du namn på den så att den nya virtuella datorn kan skapas på samma ESXi-värd.


### <a name="error-code-78093"></a>Felkod 78093

**Den virtuella datorn körs inte, i ett hängt tillstånd eller inte tillgänglig.**

Gör så här för att lösa problemet:

För att återställa en fellös virtuell dator måste den virtuella Azure-datorn köras så att Mobilitetstjänsten registrerar sig med konfigurationsservern lokalt och kan börja replikera genom att kommunicera med processservern. Om datorn finns i ett felaktigt nätverk eller inte körs (svarar inte eller stängs av) kan konfigurationsservern inte nå Mobilitetstjänsten på den virtuella datorn för att börja skydda igen.

* Starta om den virtuella datorn så att den kan börja kommunicera tillbaka lokalt.
* Starta om reprotect-jobbet när du har startat den virtuella Azure-datorn.

### <a name="error-code-8061"></a>Felkod 8061

**Datalagret är inte tillgängligt från ESXi-värden.**

Kontrollera [huvudmålförutsättningarna och datalager som stöds](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) för återställning efter fel.


## <a name="troubleshoot-failback-errors"></a>Felsöka fel vid återställning av fel

I det här avsnittet beskrivs vanliga fel som kan uppstå vid återställning efter fel.

### <a name="error-code-8038"></a>Felkod 8038

**Det gick inte att ta upp den lokala virtuella datorn på grund av felet.**

Det här problemet uppstår när den lokala virtuella datorn tas upp på en värd som inte har tillräckligt med minne. 

Gör så här för att lösa problemet:

* Etablera mer minne på ESXi-värden.
* Dessutom kan du använda vMotion för att flytta den virtuella datorn till en annan ESXi-värd som har tillräckligt med minne för att starta den virtuella datorn.
