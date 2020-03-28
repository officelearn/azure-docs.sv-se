---
title: Förbereda datorer för migrering med Azure Migrate
description: Lär dig hur du förbereder lokala datorer för migrering med Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78927478"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Förbereda lokala datorer för migrering till Azure

I den hÃ¤r artikeln beskrivs hur du förbereder lokala datorer innan du börjar migrera dem till Azure med [Azure Migrate: Servermigrering](migrate-services-overview.md#azure-migrate-server-migration-tool).

Den här artikeln innehåller följande avsnitt:
> [!div class="checklist"]
> * Verifiera migreringsbegränsningar.
> * Kontrollera operativsystemkrav och supportbegränsningar.
> * Granska URL- och portåtkomst för datorer som du vill migrera.
> * Granska ändringar som du kan behöva göra innan du påbörjar migreringen.
> * Konfigurera inställningar så att enhetsbeteckningar bevaras efter migreringen.
> * Förbered datorer så att du kan ansluta till virtuella Azure-datorer efter migreringen.

## <a name="verify-migration-limitations"></a>Verifiera migreringsbegränsningar

- Du kan bedöma upp till 35 000 virtuella virtuella datorer för VMware/Virtuella datorer med Hyper-V i ett enda Azure Migrate-projekt med hjälp av Azure Migrate Server Migration. Ett projekt kan kombinera virtuella datorer med VMware och virtuella virtuella hyper-virtuella datorer, upp till gränserna för varje.
- Du kan välja upp till 10 virtuella datorer åt gången för migrering. Om du behöver replikera mer replikeras du i grupper om 10.
- För VMware agentless migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="verify-operating-system-requirements"></a>Verifiera operativsystemkrav

- Kontrollera att dina [Windows-operativsystem](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) stöds i Azure.
- Kontrollera att dina [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) stöds i Azure.

## <a name="see-whats-supported"></a>Se vad som stöds

För virtuella datorer med VMware stöder servermigrering [agentlös eller agentbaserad migrering](server-migrate-overview.md).

- **Virtuella datorer med VMware**: Verifiera [migreringskrav och stöd](migrate-support-matrix-vmware-migration.md) för virtuella datorer med VMware.
- **Virtuella datorer med hyper-V:** Verifiera [migreringskrav och stöd](migrate-support-matrix-hyper-v-migration.md) för virtuella hyper-virtuella datorer.
- **Fysiska datorer**: Verifiera [migreringskrav och stöd](migrate-support-matrix-physical-migration.md) för lokala fysiska datorer och andra virtualiserade servrar. 

## <a name="review-url-and-port-access"></a>Granska URL- och portåtkomst

Maskiner kan behöva tillgång till internet under migreringen.

- **Azure Migrera-installation:** Granska [URL:er](migrate-appliance.md#url-access) och [portar](migrate-support-matrix-vmware-migration.md#agentless-ports) som Azure Migrate-enheten måste komma åt under migrering utan agent.
- **VMware VM-agentbaserad migrering:** Granska [URL:er](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som replikeringsinstallationen använder under VMware VM-agentbaserad migrering. 
- **Hyper-V-värdar:** Granska [webbadresser och portar](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) som Hyper-V-värdarna behöver komma åt under migreringen. 
- **Fysiska servrar**: Granska [url:er](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som replikeringsverktyget använder under fysisk servermigrering.

## <a name="verify-required-changes-before-migrating"></a>Verifiera nödvändiga ändringar innan du migrerar

Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för virtuella datorer som kör dessa operativsystem:

- Red Hat Enterprise Linux 7,0 +, 6,5+
- CentOS 7,0+, 6,5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS, 16.04LTS, 14.04LTS
- Debian 8, 7

För andra operativsystem måste du förbereda datorer manuellt före migreringen. 

### <a name="prepare-windows-machines"></a>Förbereda Windows-maskiner

Om du migrerar en Windows-dator gör du följande ändringar före migreringen. Om du migrerar den virtuella datorn innan du gör ändringarna kanske den virtuella datorn inte startar upp i Azure.

1. [Aktivera Azure Serial Console](../virtual-machines/troubleshooting/serial-console-windows.md) för Den virtuella Azure-datorn. Genom att aktivera konsolen kan du felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startar med hjälp av diskavbildningen. Diskavbildningsstarten motsvarar en omstart för den nya virtuella datorn. 
2. Om du migrerar datorer med Windows Server 2003 installerar du Hyper-V Guest Integration Services på operativsystemet VM. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Förbered Linux-maskiner

1. Installera Hyper-V Linux Integration Services. De flesta nya versioner av Linux-distributioner inkluderar Hyper-V Linux Integration Services som standard.
2. Återskapa Linux-init-avbildningen så att den innehåller nödvändiga Hyper-V-drivrutiner. Återskapa init-avbildningen säkerställer att den virtuella datorn startar i Azure (krävs endast på vissa distributioner).
3. [Aktivera Loggning av Azure Serial Console](../virtual-machines/troubleshooting/serial-console-linux.md). Genom att aktivera konsolloggning kan du felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startar med hjälp av diskavbildningen. Diskavbildningsstarten motsvarar en omstart för den nya virtuella datorn.
4. Uppdatera enhetsmappningsfilen med enhetens namn-till-volym-associationer, så att du använder beständiga enhetsidentifierare.
5. Uppdatera poster i fstab-filen om du vill använda beständiga volymidentifierare.
6. Ta bort alla udev-regler som reserverar gränssnittsnamn baserat på MAC-adress och så vidare.
7. Uppdatera nätverksgränssnitt för att ta emot en IP-adress från DHCP.

Läs mer om [stegen för att köra en Virtuell Linux-dator på Azure](../virtual-machines/linux/create-upload-generic.md)och få instruktioner för några av de populära Linux-distributionerna.

## <a name="preserve-drive-letters-after-migration"></a>Bevara enhetsbeteckningar efter migreringen

När du migrerar en lokal dator till Microsoft Azure kan enhetsbeteckningarna för ytterligare datadiskar ändras från sina ursprungliga värden. 

Som standard tilldelas virtuella Azure-datorer enhet D som ska användas som tillfällig lagring. Den här enhetstilldelningen gör att alla andra kopplade lagringsenhetstilldelningar ökas med en bokstav. Om din lokala installation till exempel använder en datadisk som har tilldelats enhet D för programinstallationer, ökar tilldelningen för den här enheten till enhet E när du migrerar den virtuella datorn till Azure. Om du vill förhindra den här automatiska tilldelningen och se till att Azure tilldelar nästa kostnadsfria enhetsbeteckning till dess tillfälliga volym anger du SAN-principen (Storage Area Network) till **OnlineAll:**

1. Öppna en upphöjd kommandotolk på den lokala datorn (inte värdservern).
2. Ange **diskdel**.
3. Ange **SAN**. Om enhetsbeteckningen för gästoperativsystemet inte underhålls returneras **offline alla** eller **offline delas.**
4. Ange **SAN Policy=OnlineAll**i **DISKPART-prompten** . Den här inställningen säkerställer att diskar är online och säkerställer att du kan läsa och skriva till båda diskarna.
5. Under testmigreringen kan du kontrollera att enhetsbeteckningarna bevaras.

## <a name="check-azure-vm-requirements"></a>Kontrollera Azure VM-krav

Lokala datorer som du replikerar till Azure måste uppfylla Azure VM-krav för operativsystemet och arkitekturen, diskarna, nätverksinställningarna och VM-namngivning. Verifiera kraven för [virtuella datorer och fysiska servrar och](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)virtuella datorer med [hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) innan du migrerar.

## <a name="prepare-to-connect-after-migration"></a>Förbered anslutning efter migreringen

Virtuella Azure-datorer skapas under migreringen till Azure. Efter migreringen måste du kunna ansluta till de nya virtuella Azure-datorerna. Flera steg krävs för att ansluta.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Förbereda för att ansluta till virtuella Azure Windows-datorer

På lokala Windows-maskiner:

1. Konfigurera Windows-inställningar. Inställningarna inkluderar att ta bort statiska beständiga vägar eller WinHTTP-proxy.
2. Kontrollera att [nödvändiga tjänster](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.
3. Aktivera fjärrskrivbord (RDP) för att tillåta fjärranslutningar till den lokala datorn. Lär dig hur du [använder PowerShell för att aktivera RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Om du vill komma åt en virtuell Azure-dator via internet efter migreringen tillåter du TCP och UDP i den offentliga profilen i Windows-brandväggen i Windows-brandväggen på den lokala datorn och anger RDP som en tillåten app för alla profiler.
5. Om du vill komma åt en Virtuell Azure-dator via en plats-till-plats-VPN efter migreringen tillåter du rdp för domänen och privata profiler i Windows-brandväggen på den lokala datorn. Lär dig hur du [tillåter RDP-trafik](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Kontrollera att det inte finns några Windows-uppdateringar som väntar på den lokala virtuella datorn när du migrerar. Om det finns kan uppdateringar börja installera på Azure VM efter migreringen, och du kommer inte att kunna logga in på den virtuella datorn förrän uppdateringarna är klara.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Förbered att ansluta med virtuella Linux Azure-datorer

På lokala Linux-maskiner:

1. Kontrollera att tjänsten Secure Shell är inställd på att starta automatiskt vid systemstart.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.

### <a name="configure-azure-vms-after-migration"></a>Konfigurera Virtuella Azure-datorer efter migreringen

Efter migreringen slutför du de här stegen på de virtuella Azure-datorerna som skapas:

1. Om du vill ansluta till den virtuella datorn via internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du måste använda en annan offentlig IP-adress för den virtuella Azure-datorn än du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md).
2. Kontrollera att NSG-regler (Network Security Group) på den virtuella datorn tillåter inkommande anslutningar till RDP- eller SSH-porten.
3. Kontrollera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.

> [!NOTE]
> Azure Bastion-tjänsten erbjuder privat RDP- och SSH-åtkomst till virtuella Azure-datorer. [Läs mer](../bastion/bastion-overview.md) om den här tjänsten.

## <a name="next-steps"></a>Nästa steg

Bestäm vilken metod du vill använda för att migrera virtuella virtuella datorer med [VMware](server-migrate-overview.md) till Azure, eller börja migrera [virtuella virtuella datorer](tutorial-migrate-hyper-v.md) eller fysiska servrar eller [virtualiserade eller virtuella virtuella virtuella datorer eller virtuella datorer](tutorial-migrate-physical-virtual-machines.md)i molnet .
