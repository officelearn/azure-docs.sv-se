---
title: Stöd för Hyper-V-migrering i Azure Migrate
description: Läs mer om stöd för Hyper-V-migrering med Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245829"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Stödmatris för Hyper-V-migrering

Den här artikeln sammanfattar supportinställningar och begränsningar för migrering av virtuella hyper-virtuella datorer med [Azure Migrate: Servermigrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du letar efter information om hur du bedömer virtuella hyper-virtuella datorer för migrering till Azure läser du [matrisen för utvärderingsstöd](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Migreringsbegränsningar

Du kan välja upp till 10 virtuella datorer samtidigt för replikering. Om du vill migrera fler datorer replikeras i grupper om 10.


## <a name="hyper-v-hosts"></a>Hyper-V-värdar

| **Support**                | **Detaljer**               
| :-------------------       | :------------------- |
| **Distribution**       | Hyper-V-värden kan vara fristående eller distribuerad i ett kluster. <br/>Azure Migrate replikeringsprogramvara (Hyper-V Replication-provider) måste installeras på Hyper-V-värdarna.|
| **Behörigheter**           | Du behöver administratörsbehörigheter för Hyper-V-värden. |
| **Värdoperativsystem** | Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2. |
| **URL-åtkomst** | Replikeringsproviderprogramvaran på Hyper-V-värdarna behöver åtkomst till dessa webbadresser:<br/><br/> - login.microsoftonline.com: Åtkomstkontroll och identitetshantering med Active Directory.<br/><br/> - *.backup.windowsazure.com: Överföring och samordning av replikeringsdata. Migrera tjänstadresser.<br/><br/> - *.blob.core.windows.net: Ladda upp data till lagringskonton.<br/><br/> - dc.services.visualstudio.com: Ladda upp apploggar som används för intern övervakning.<br/><br/> - time.windows.com: Verifierar tidssynkronisering mellan system och global tid.
| **Tillträde till port** |  Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata.

## <a name="hyper-v-vms"></a>Hyper-V:s virtuella datorer

| **Support**                  | **Detaljer**               
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) och [Linux-operativsystem](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) som stöds av Azure. |
| **Nödvändiga ändringar för Azure** | Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Du måste göra justeringar manuellt före migreringen. De relevanta artiklarna innehåller instruktioner om hur du gör detta. |
| **Linux-start**                 | Om /boot finns på en dedikerad partition bör den finnas på OS-disken och inte spridas över flera diskar.<br/> Om /boot är en del av rotpartitionen (/) ska /-partitionen finnas på OS-disken och inte sträcka sig över andra diskar. |
| **UEFI-start**                  | Den migrerade virtuella datorn i Azure konverteras automatiskt till en VIRTUELL BIOS-start. Den virtuella datorn bör köra Windows Server 2012 och senare endast. OS-disken bör ha upp till fem partitioner eller färre och storleken på OS-disken bör vara mindre än 300 GB.
  |
| **Diskstorlek**                  | 2 TB för OS-disken, 4 TB för datadiskar.
| **Disknummer** | Högst 16 diskar per virtuell dator.
| **Krypterade diskar/volymer**    | Stöds inte för migrering. |
| **RDM/vidaregenomströmningsdiskar**      | Stöds inte för migrering. |
| **Delad disk** | Virtuella datorer med delade diskar stöds inte för migrering.
| **NFS**                        | NFS-volymer monterade som volymer på de virtuella datorerna replikeras inte. |
| **Iscsi**                      | Virtuella datorer med iSCSI-mål stöds inte för migrering.
| **Måldisk**                | Du kan migrera till virtuella Azure-datorer med hanterade diskar. |
| **IPv6** | Stöds inte.
| **NIC-teaming** | Stöds inte.
| **Återställning av Azure-webbplatser** | Du kan inte replikera med Azure Migrate Server Migration om den virtuella datorn är aktiverad för replikering med Azure Site Recovery.
| **Portar** | Utgående anslutningar på HTTPS-port 443 för att skicka VM-replikeringsdata.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla azure vm-kraven som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen misslyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen misslyckas om den inte stöds.
Antal datadiskar | 16 eller mindre. | Kontrollen misslyckas om den inte stöds.
Storlek på datadisk | Upp till 4 095 GB | Kontrollen misslyckas om den inte stöds.
Nätverkskort | Flera kort stöds. |
Delad VHD | Stöds inte. | Kontrollen misslyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen misslyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator.
VM-namn | Från 1 till 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Maskinnamnet måste börja och sluta med en bokstav eller ett nummer. |  Uppdatera värdet i datoregenskaperna i Site Recovery.
Ansluta efter migrering-Windows | Så här ansluter du till virtuella Azure-datorer som kör Windows efter migreringen:<br/> - Innan migrering aktiverar RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För VPN-åtkomst på plats till plats aktiverar du RDP och tillåter RDP i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för **domännätverk och privata** nätverk. Kontrollera dessutom att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migrering-Linux | Så här ansluter du till virtuella Azure-datorer efter migreringen med SSH:<br/> Kontrollera att tjänsten Secure Shell är inställd på Start på den lokala datorn före migreringen och att brandväggsregler tillåter en SSH-anslutning.<br/> Efter redundans, på den virtuella Azure-datorn, tillåt inkommande anslutningar till SSH-porten för nätverkssäkerhetsgruppsreglerna på den misslyckade över den virtuella datorn och för Det Azure-undernät som det är anslutet till. Lägg dessutom till en offentlig IP-adress för den virtuella datorn. |  

## <a name="next-steps"></a>Nästa steg

[Migrera virtuella hyper-virtuella datorer](tutorial-migrate-hyper-v.md) för migrering.
