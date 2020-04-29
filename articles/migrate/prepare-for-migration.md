---
title: Förbered datorer för migrering med Azure Migrate
description: Lär dig hur du förbereder lokala datorer för migrering med Azure Migrate.
ms.topic: tutorial
ms.date: 02/17/2020
ms.custom: MVC
ms.openlocfilehash: eba177a254606bb847e0866ae48281a889c53f9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78927478"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Förbered lokala datorer för migrering till Azure

Den här artikeln beskriver hur du förbereder lokala datorer innan du börjar migrera dem till Azure med hjälp av [Azure Migrate: Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool).

Den här artikeln innehåller följande avsnitt:
> [!div class="checklist"]
> * Verifiera begränsningar för migrering.
> * Kontrol lera operativ systemets krav och support begränsningar.
> * Granska URL och port åtkomst för datorer som du vill migrera.
> * Granska ändringar som du kan behöva göra innan du påbörjar migreringen.
> * Konfigurera inställningar så att enhets beteckningar bevaras efter migreringen.
> * Förbered datorer så att du kan ansluta till de virtuella Azure-datorerna efter migreringen.

## <a name="verify-migration-limitations"></a>Verifiera begränsningar för migrering

- Du kan utvärdera upp till 35 000 virtuella VMware-datorer/Hyper-V-datorer i ett enda Azure Migrate projekt med hjälp av Azure Migrate Server-migrering. Ett projekt kan kombinera virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till gränserna för var och en.
- Du kan välja upp till 10 virtuella datorer i taget för migrering. Om du behöver replikera mer replikerar du i grupper om 10.
- För VMware-agent lös migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="verify-operating-system-requirements"></a>Kontrol lera krav för operativ system

- Kontrol lera att dina [Windows-operativsystem](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) stöds i Azure.
- Kontrol lera att [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) stöds i Azure.

## <a name="see-whats-supported"></a>Se vad som stöds

För virtuella VMware-datorer stöder Server migreringen utan [agent eller med agent-baserad migrering](server-migrate-overview.md).

- **Virtuella VMware-datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-vmware-migration.md) för virtuella VMware-datorer.
- **Virtuella Hyper-v-datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-hyper-v-migration.md) för virtuella Hyper-v-datorer.
- **Fysiska datorer**: kontrol lera [kraven för migrering och stöd](migrate-support-matrix-physical-migration.md) för lokala fysiska datorer och andra virtualiserade servrar. 

## <a name="review-url-and-port-access"></a>Granska URL-och port åtkomst

Datorer kan behöva Internet åtkomst under migreringen.

- **Azure Migrate**-installation: granska [URL: er](migrate-appliance.md#url-access) och [portar](migrate-support-matrix-vmware-migration.md#agentless-ports) som Azure Migrate-installationen behöver för att få åtkomst under en agent lös migrering.
- **VM-baserad VM-baserad migrering**: granska [webb adresser](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som Replication-enheten använder vid VM-baserad VMware-migrering. 
- **Hyper-V-värdar**: granska [webb adresser och portar](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) som Hyper-v-värdarna måste ha åtkomst till under migreringen. 
- **Fysiska servrar**: granska [webb adresser](migrate-replication-appliance.md#url-access) och [portar](migrate-replication-appliance.md#port-access) som Replication-enheten använder vid migrering av fysiska servrar.

## <a name="verify-required-changes-before-migrating"></a>Verifiera nödvändiga ändringar innan du migrerar

Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för virtuella datorer som kör dessa operativ system:

- Red Hat Enterprise Linux 7.0 +, 6.5 +
- CentOS 7.0 +, 6.5 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 18.04 LTS, 16.04 LTS, 14.04 LTS
- Debian 8, 7

För andra operativ system måste du förbereda datorer manuellt innan du migrerar. 

### <a name="prepare-windows-machines"></a>Förbereda Windows-datorer

Om du migrerar en Windows-dator gör du följande ändringar innan du migrerar. Om du migrerar den virtuella datorn innan du gör ändringarna kanske den virtuella datorn inte startar i Azure.

1. [Aktivera Azures serie konsol](../virtual-machines/troubleshooting/serial-console-windows.md) för den virtuella Azure-datorn. Genom att aktivera-konsolen kan du felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen. Disk avbildningens start motsvarar en omstart för den nya virtuella datorn. 
2. Om du migrerar datorer som kör Windows Server 2003 installerar du Hyper-V-gäst integrerings tjänster på den virtuella datorns operativ system. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Förbereda Linux-datorer

1. Installera Hyper-V Linux Integration Services. I de flesta nya versioner av Linux-distributioner ingår Hyper-V Linux Integration Services som standard.
2. Återskapa Linux init-avbildningen så att den innehåller de nödvändiga Hyper-V-drivrutinerna. Genom att återskapa init-avbildningen ser du till att den virtuella datorn startar i Azure (krävs endast för vissa distributioner).
3. [Aktivera loggning av Azure-serienummer](../virtual-machines/troubleshooting/serial-console-linux.md). Genom att aktivera konsol loggning kan du felsöka. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn startas med disk avbildningen. Disk avbildningens start motsvarar en omstart för den nya virtuella datorn.
4. Uppdatera enhets mappnings filen med enhets namn-till-volym-associationerna, så att du kan använda beständiga enhets identifierare.
5. Uppdatera poster i fstab-filen för att använda beständiga volym identifierare.
6. Ta bort alla udev-regler som reserverar gränssnitts namn baserat på MAC-adress och så vidare.
7. Uppdatera nätverks gränssnitt för att ta emot en IP-adress från DHCP.

Lär dig mer om [stegen för att köra en virtuell Linux-dator på Azure](../virtual-machines/linux/create-upload-generic.md)och få instruktioner för några av de populära Linux-distributionerna.

## <a name="preserve-drive-letters-after-migration"></a>Bevara enhets beteckningar efter migrering

När du migrerar en lokal dator till Microsoft Azure kan enhets beteckningarna för ytterligare data diskar ändras från sina ursprungliga värden. 

Som standard tilldelas virtuella Azure-datorer enhet D som används som tillfällig lagring. Den här enhets tilldelningen gör att alla andra anslutna lagrings enhets tilldelningar ökar med en bokstav. Om din lokala installation t. ex. använder en datadisk som är tilldelad enhet D för programinstallationer, ökar tilldelningen för den här enheten till E efter det att du har migrerat den virtuella datorn till Azure. För att förhindra denna automatiska tilldelning och för att säkerställa att Azure tilldelar nästa lediga enhets beteckning till dess tillfälliga volym, ställer du in storage area network (SAN)-principen på **OnlineAll**:

1. På den lokala datorn (inte värd servern) öppnar du en upphöjd kommando tolk.
2. Ange **DiskPart**.
3. Ange **San**. Om enhets beteckningen för gäst operativ systemet inte underhålls, returneras **offline all** eller **offline delad** .
4. Ange **San-princip = OnlineAll**i **DiskPart** -prompten. Den här inställningen säkerställer att diskarna är online och att du kan läsa och skriva till båda diskarna.
5. Under testmigreringen kan du kontrol lera att enhets beteckningarna bevaras.

## <a name="check-azure-vm-requirements"></a>Kontrol lera kraven för virtuella Azure-datorer

Lokala datorer som du replikerar till Azure måste uppfylla kraven på virtuella Azure-datorer för operativ system och arkitektur, diskar, nätverks inställningar och namngivning av virtuella datorer. Kontrol lera kraven för [virtuella VMware-datorer och fysiska servrar](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)och [virtuella Hyper-V-datorer](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) innan du migrerar.

## <a name="prepare-to-connect-after-migration"></a>Förbered för att ansluta efter migreringen

Virtuella Azure-datorer skapas under migreringen till Azure. Efter migreringen måste du kunna ansluta till de nya virtuella Azure-datorerna. Flera steg krävs för att kunna ansluta.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Förbereda för att ansluta till virtuella Azure Windows-datorer

På lokala Windows-datorer:

1. Konfigurera Windows-inställningar. Inställningarna inkluderar borttagning av alla statiska beständiga vägar eller WinHTTP-proxy.
2. Kontrol lera att de [nödvändiga tjänsterna](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.
3. Aktivera fjärr skrivbord (RDP) om du vill tillåta fjärr anslutningar till den lokala datorn. Lär dig hur du [använder PowerShell för att aktivera RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. För att få åtkomst till en virtuell Azure-dator via Internet efter migreringen, i Windows-brandväggen på den lokala datorn, Tillåt TCP och UDP i den offentliga profilen och ange RDP som en tillåten app för alla profiler.
5. Om du vill få åtkomst till en virtuell Azure-dator via en plats-till-plats-VPN efter migreringen, kan du i Windows-brandväggen på den lokala datorn tillåta RDP för domänen och privata profiler. Lär dig hur du [tillåter RDP-trafik](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Se till att inga Windows-uppdateringar väntar på den lokala virtuella datorn när du migrerar. Om så är fallet kan uppdateringar börja installeras på den virtuella Azure-datorn efter migreringen och du kan inte logga in på den virtuella datorn förrän uppdateringarna har slutförts.

### <a name="prepare-to-connect-with-linux-azure-vms"></a>Förbered för att ansluta till virtuella Linux Azure-datorer

På lokala Linux-datorer:

1. Kontrol lera att Secure Shell-tjänsten är inställd på att starta automatiskt vid system start.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.

### <a name="configure-azure-vms-after-migration"></a>Konfigurera virtuella Azure-datorer efter migrering

Efter migreringen slutför du de här stegen på de virtuella Azure-datorer som skapas:

1. Om du vill ansluta till den virtuella datorn via Internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du måste använda en annan offentlig IP-adress för den virtuella Azure-datorn än du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md).
2. Kontrol lera att reglerna för nätverks säkerhets gruppen (NSG) på den virtuella datorn tillåter inkommande anslutningar till RDP-eller SSH-porten.
3. Kontrol lera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.

> [!NOTE]
> Azure skydds-tjänsten erbjuder privat RDP och SSH-åtkomst till virtuella Azure-datorer. [Läs mer](../bastion/bastion-overview.md) om den här tjänsten.

## <a name="next-steps"></a>Nästa steg

Bestäm vilken metod du vill använda för att [migrera virtuella VMware-datorer](server-migrate-overview.md) till Azure, eller börja migrera virtuella [Hyper-V-datorer](tutorial-migrate-hyper-v.md) eller [fysiska servrar eller virtuella datorer eller virtuella datorer i molnet](tutorial-migrate-physical-virtual-machines.md).
