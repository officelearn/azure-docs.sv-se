---
title: Förbereda virtuella virtuella datorer med VMware för återbeskyddande och återställning av återställning av återställning av webbplatser med Azure Site Recovery
description: Förbered återställning av virtuella datorer med VMware efter redundans med Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257191"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Förbered för återskydd och återställning av virtuella datorer med VMware

Efter [redundans](site-recovery-failover.md) av lokala virtuella datorer med vmware eller fysiska servrar till Azure, återroterar du de virtuella Azure-datorer som skapats efter redundans, så att de replikeras tillbaka till den lokala platsen. Med replikering från Azure till lokalt på plats kan du sedan växla tillbaka genom att köra en redundans från Azure till lokalt när du är redo.

Innan du fortsätter får du en snabb översikt med det här videoklippet om hur du växlar tillbaka från Azure till en lokal webbplats.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Återbeskydda/återställningskomponenter

Du behöver ett antal komponenter och inställningar på plats innan du kan reprotera och växla tillbaka från Azure.

**Komponent**| **Detaljer**
--- | ---
**Lokal konfigurationsserver** | Den lokala konfigurationsservern måste köras och anslutas till Azure.<br/><br/> Den virtuella datorn som du inte kan återgå till måste finnas i konfigurationsserverdatabasen. Om en katastrof påverkar konfigurationsservern återställer du den med samma IP-adress för att säkerställa att återställningen av återställningen fungerar.<br/><br/>  Om IP-adresser för replikerade datorer behålls vid redundans, bör anslutning mellan azure-datorer och konfigurationsserverns nic-nätverkskort för återställning. För behållna IP-adresser behöver konfigurationsservern två nätverkskort - en för källdatoranslutning och en för Azure-återställningsanslutning. Detta undviker överlappning av undernätsadressintervall för källan och misslyckades över virtuella datorer.
**Bearbeta server i Azure** | Du behöver en processserver i Azure innan du kan växla tillbaka till din lokala webbplats.<br/><br/> Processservern tar emot data från den skyddade Azure VM och skickar den till den lokala platsen.<br/><br/> Du behöver ett nätverk med låg latens mellan processservern och den skyddade virtuella datorn, så vi rekommenderar att du distribuerar processservern i Azure för högre replikeringsprestanda.<br/><br/> För proof-of-concept kan du använda den lokala processservern och ExpressRoute med privat peering.<br/><br/> Processservern ska finnas i Azure-nätverket där den misslyckade över den virtuella datorn finns. Processservern måste också kunna kommunicera med den lokala konfigurationsservern och huvudmålservern.
**Separat målserver för huvudmål** | Huvudmålservern tar emot återställningsdata för återställning efter fel och som standard körs en Målserver för Windows-huvud på den lokala konfigurationsservern.<br/><br/> En huvudmålserver kan ha upp till 60 diskar anslutna till sig. Virtuella datorer som misslyckades tillbaka har mer än en kollektiv summa på 60 diskar, eller om du misslyckas tillbaka stora trafikvolymer, skapa en separat huvudmålserver för återställning efter fel.<br/><br/> Om datorer samlas in i en replikeringsgrupp för konsekvens för flera virtuella datorer måste de virtuella datorerna alla vara Windows eller måste alla vara Linux. Varför det? Eftersom alla virtuella datorer i en replikeringsgrupp måste använda samma huvudmålserver och huvudmålservern måste ha samma operativsystem (med samma eller en högre version) än de replikerade datorernas.<br/><br/> Huvudmålservern bör inte ha några ögonblicksbilder på sina diskar, annars fungerar inte återbeskydd och återställning av återställning.<br/><br/> Huvudmålet kan inte ha en Paravirtual SCSI-styrenhet. Styrenheten kan bara vara en LSI Logic-styrenhet. Utan en LSI Logic-styrenhet misslyckas återbeskydd.
**Replikeringsprincip för återställning** | Om du vill replikera tillbaka till den lokala platsen behöver du en princip för återställning efter fel. Den här principen skapas automatiskt när du skapar en replikeringsprincip till Azure.<br/><br/> Principen associeras automatiskt med konfigurationsservern. Den är inställd på en RPO-tröskel på 15 minuter, återställningspunkt kvarhållning av 24 timmar och appkonsekvent ögonblicksbildfrekvens är 60 minuter. Det går inte att redigera principen. 
**Plats-till-plats VPN/ExpressRoute privat peering** | Reprotection och failback behöver en plats-till-plats VPN-anslutning, eller ExpressRoute privat peering för att replikera data. 


## <a name="ports-for-reprotectionfailback"></a>Portar för återskydd/återställning

Ett antal portar måste vara öppna för återskydd/återställning. Följande bild illustrerar portarna och reprotect/failback-flödet.

![Portar för redundans och redundans](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Distribuera en processserver i Azure

1. [Konfigurera en processserver](vmware-azure-set-up-process-server-azure.md) i Azure för återställning efter fel.
2. Se till att virtuella Azure-datorer kan nå processservern. 
3. Kontrollera att VPN-anslutningen för plats till plats eller ExpressRoute-privata peering-nätverk har tillräckligt med bandbredd för att skicka data från processservern till den lokala platsen.

## <a name="deploy-a-separate-master-target-server"></a>Distribuera en separat huvudmålserver

1. Observera huvudmålserverns [krav och begränsningar](#reprotectionfailback-components).
2. Skapa en [Målserver](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) för Windows eller [Linux-huvud,](vmware-azure-install-linux-master-target.md) för att matcha operativsystemet för de virtuella datorer som du vill reprotect och misslyckas tillbaka.
3. Kontrollera att du inte använder Storage vMotion för huvudmålservern, eller att återställningen misslyckas. Den virtuella datorn kan inte starta eftersom diskarna inte är tillgängliga för den.
    - Undvik detta genom att utesluta huvudmålservern från vMotion-listan.
    - Om ett huvudmål genomgår en Storage vMotion-uppgift efter återbeskyddning migrerar de skyddade VM-diskarna som är kopplade till huvudmålservern till målet för vMotion-aktiviteten. Om du försöker återställa problemet igen efter detta misslyckas diskavlossningen eftersom diskarna inte hittas. Det är då svårt att hitta diskarna i dina lagringskonton. Om detta inträffar, leta reda på dem manuellt och bifoga dem till den virtuella datorn. Därefter kan den lokala virtuella datorn startas.

4. Lägg till en kvarhållningsenhet på den befintliga Målservern för Windows-huvud. Lägg till en ny disk och formatera enheten. Kvarhållningsenheten används för att stoppa de tidpunkter då den virtuella datorn replikeras tillbaka till den lokala platsen. Notera dessa kriterier. Om de inte uppfylls visas inte enheten för huvudmålservern:
    - Volymen används inte för något annat ändamål, till exempel ett replikeringsmål, och den är inte i låsläge.
    - Volymen är inte en cachevolym. Den anpassade installationsvolymen för processservern och huvudmålet är inte berättigad till en kvarhållningsvolym. När processservern och huvudmålet är installerade på en volym är volymen en cachevolym för huvudmålet.
    - Volymens filsystemtyp är inte FAT eller FAT32.
    - Volymkapaciteten är icke noll.
    - Standardkvarhållningsvolymen för Windows är R-volymen.
    - Standardlagringsvolymen för Linux är /mnt/retention.

5. Lägg till en enhet om du använder en befintlig processserver. Den nya enheten måste uppfylla kraven i det sista steget. Om kvarhållningsenheten inte finns visas den inte i markeringsrutan på portalen. När du har lagt till en enhet i det lokala huvudmålet tar det upp till 15 minuter innan enheten visas i markeringen på portalen. Du kan uppdatera konfigurationsservern om enheten inte visas efter 15 minuter.
6. Installera VMware-verktyg eller öppna vm-verktyg på huvudmålservern. Utan verktygen kan datalagret på huvudmålets ESXi-värd inte identifieras.
7. Ställ in disken. EnableUUID=true-inställningen i konfigurationsparametrarna för huvudmålet VM i VMware. Om den här raden inte finns lägger du till den. Den här inställningen krävs för att tillhandahålla ett konsekvent UUID till VMDK så att den monteras korrekt.
8. Kontrollera åtkomstkraven för vCenter Server:
    - Om den virtuella datorn som du misslyckas med är på en ESXi-värd som hanteras av VMware vCenter Server, behöver huvudmålservern åtkomst till den lokala VMDK-filen (Virtual Machine Disk) för att kunna skriva replikerade data till den virtuella datorns diskar. Kontrollera att den lokala VM-databutiken är monterad på huvudmålvärden med läs-/skrivåtkomst.
    - Om den virtuella datorn inte finns på en ESXi-värd som hanteras av en VMware vCenter Server, skapar Site Recovery en ny virtuell dator under återbeskyddning. Den här virtuella datorn skapas på den ESXi-värd där du skapar huvudmålservern VM. Välj ESXi-värden noggrant för att skapa den virtuella datorn på den värd du vill ha. Hårddisken på den virtuella datorn måste finnas i ett datalager som kan nås av den värd där huvudmålservern körs.
    - Ett annat alternativ, om den lokala virtuella datorn redan finns för återställning efter fel, är att ta bort den innan du gör en återställning efter fel. Återställning efter fel skapar sedan en ny virtuell dator på samma värd som huvudmålet ESXi-värden. När du växlar tillbaka till en alternativ plats återställs data till samma datalager och samma ESXi-värd som den som används av den lokala huvudmålservern.
9. För fysiska datorer som inte går tillbaka till virtuella datorer med VMware bör du slutföra identifieringen av värden där huvudmålservern körs, innan du kan rotera om datorn.
10. Kontrollera att den ESXi-värd som huvudmålet VMFS-datalager har kopplat till. Om inga VMFS-datacenter är anslutna är datalagerindatan i återskyddsinställningarna tom och du kan inte fortsätta.


## <a name="next-steps"></a>Nästa steg

[Reprotect](vmware-azure-reprotect.md) en virtuell dator.
