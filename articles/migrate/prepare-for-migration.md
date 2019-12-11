---
title: Förbered datorer för migrering med Azure Migrate
description: Lär dig hur du förbereder lokala datorer för migrering med Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f5535a57fae847c8a376b8b39e43955675da739
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974792"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Förbered lokala datorer för migrering till Azure

Den här artikeln beskriver hur du förbereder lokala datorer innan du börjar migrera dem till Azure med [Azure Migrate Server-migrering](migrate-services-overview.md#azure-migrate-server-migration-tool).


Den här artikeln innehåller följande avsnitt:
> [!div class="checklist"]
> * Verifiera begränsningar för migrering.
> * Kontrol lera operativ systemets krav och support begränsningar.
> * Granska URL/port-åtkomst för datorer som du vill migrera.
> * Granska ändringar som du kan behöva göra innan du påbörjar migreringen.
> * Konfigurera inställningar så att enhets beteckningar bevaras efter migreringen.
> * Förbered datorer så att du kan ansluta till de virtuella Azure-datorerna efter migreringen.


## <a name="verify-migration-limitations"></a>Verifiera begränsningar för migrering

- Du kan utvärdera upp till 35 000 virtuella VMware-datorer/Hyper-V-datorer i ett enda Azure Migrate projekt med hjälp av Azure Migrate Server-migrering. Ett projekt kan kombinera både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till gränserna för var och en.
- Du kan välja upp till 10 virtuella datorer på en gång för migrering. Om du behöver replikera mer replikerar du i grupper om 10.
- För VMware-agent lös migrering kan du köra upp till 100 replikeringar samtidigt.

## <a name="verify-operating-system-requirements"></a>Kontrol lera krav för operativ system

- Kontrol lera att dina [Windows-operativsystem](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) stöds i Azure.
- Kontrol lera att [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) stöds i Azure.


## <a name="check-whats-supported"></a>Kontrol lera vad som stöds

- För virtuella VMware-datorer stöder Azure Migrate Server-migreringen utan [agent eller med agent-baserad migrering](server-migrate-overview.md). Kontrol lera krav för virtuella VMware-datorer/stöd för [agentbaserade](migrate-support-matrix-vmware.md#migration---limitations) och [agentbaserade](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) migreringar.
- Kontrol lera [kraven för migrering och stöd](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) för virtuella Hyper-V-datorer.
- Kontrol lera [kraven för migrering och stöd](migrate-support-matrix-physical.md) för lokala fysiska datorer eller andra virtualiserade servrar. 




## <a name="review-urlport-access"></a>Granska URL/port-åtkomst

Datorer kan behöva Internet åtkomst under migreringen.

- Granska URL: er som de virtuella VMware-datorerna behöver ha åtkomst till vid [agentbaserade](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) eller [agentbaserade](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) migreringar.
- Granska webb adresser som Hyper-V-värdar behöver ha åtkomst till under migreringen. Virtuella Hyper-V-datorer behöver inte Internet åtkomst.
- [Granska webb adresser](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) som fysiska datorer eller andra virtualiserade servrar behöver åtkomst under migreringen.
- Vid nyckelbaserad migrering av virtuella VMware-datorer/fysiska servrar behöver mobilitets tjänsten som körs på datorerna åtkomst till Azure Migrate-komponenter. För hantering av replikering kommunicerar tjänsten som körs på datorn med den lokala Azure Migrate-replikeringstjänsten på port HTTPS 443 inkommande. Datorerna skickar replikeringsdata till Azure Migrate processervern på port HTTPS 9443 inkommande. Den här porten kan ändras.


## <a name="verify-required-changes-before-migration"></a>Verifiera nödvändiga ändringar före migrering

Vissa virtuella datorer kan kräva ändringar så att de kan köras i Azure. Azure Migrate gör dessa ändringar automatiskt för virtuella datorer som kör dessa operativ system:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5+, 7.0+
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS
- Debian 7, 8

För andra operativ system måste du förbereda datorerna manuellt innan migreringen. 

### <a name="prepare-windows-machines"></a>Förbereda Windows-datorer

Om du migrerar en Windows-dator ska du göra dessa ändringar innan du migrerar. Om du migrerar den virtuella datorn innan du gör ändringarna kanske den virtuella datorn inte startar i Azure.

1. [Aktivera Azure Serial Access-konsolen](../virtual-machines/troubleshooting/serial-console-windows.md) för den virtuella Azure-datorn. Detta hjälper till med fel sökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn kommer att starta med disk avbildningen. Detta motsvarar en omstart för den nya virtuella datorn. 
2. Om du migrerar datorer som kör Windows Server 2003 installerar du Hyper-V-gäst integrerings tjänster på den virtuella datorns operativ system. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Förbereda Linux-datorer

1. Installera Hyper-V Linux Integration Services. De flesta nya versioner av Linux-distributioner inkluderar detta som standard.
2. Återskapa Linux init-avbildningen så att den innehåller de nödvändiga Hyper-V-drivrutinerna. Detta säkerställer att den virtuella datorn kommer att starta i Azure och krävs endast för vissa distributioner.
3. [Aktivera loggning av Azure-serienummer](../virtual-machines/troubleshooting/serial-console-linux.md). Detta hjälper till med fel sökning. Du behöver inte starta om den virtuella datorn. Den virtuella Azure-datorn kommer att starta med disk avbildningen. Detta motsvarar en omstart för den nya virtuella datorn.
4. Uppdatera enhets mappnings filen med enhets namnet till volym kopplingar så att du kan använda beständiga enhets identifierare.
5. Uppdatera fstab-poster för att använda beständiga volym identifierare.
6. Ta bort alla udev-regler som reserverar gränssnitts namn baserat på MAC-adress osv.
7. Uppdatera nätverks gränssnitt för att ta emot en IP-adress från DHCP.
8. [Lär dig mer](../virtual-machines/linux/create-upload-generic.md) om de steg som krävs för att köra en virtuell Linux-dator på Azure och få instruktioner för några av de populära Linux-distributionerna.

## <a name="preserve-drive-letters-after-migration"></a>Bevara enhets beteckningar efter migrering

När du migrerar en lokal dator till Microsoft Azure kan enhets beteckningarna för ytterligare data diskar ändras från sina tidigare värden. Som standard tilldelas virtuella Azure-datorer enhet D för användning som temporär lagring. Den här enhets tilldelningen gör att alla andra anslutna lagrings enhets tilldelningar ökar med en bokstav.

Om din lokala installation t. ex. använder en datadisk som är tilldelad enhet D för programinstallationer, ökar tilldelningen för den här enheten till E efter det att du har migrerat den virtuella datorn till Azure. För att förhindra den här automatiska tilldelningen och för att säkerställa att Azure tilldelar nästa lediga enhets beteckning till dess tillfälliga volym, ställer du in storage area network (SAN)-principen på OnlineAll enligt följande:

1. Öppna en upphöjd kommando tolk på den lokala datorn (inte värd servern).
2. Skriv **DiskPart**.
3. Skriv **San**. Om enhets beteckningen för gäst operativ systemet inte underhålls, returneras **offline all** eller **offline delad** .
4. Skriv **San-princip = OnlineAll**i **DiskPart** -prompten. Den här inställningen säkerställer att diskarna är online och både läsbara och skrivbara.
5. Under testmigreringen kan du kontrol lera att enhets beteckningarna bevaras.


## <a name="check-azure-vm-requirements"></a>Kontrol lera kraven för virtuella Azure-datorer

Lokala datorer som du replikerar till Azure måste uppfylla kraven för virtuella Azure-datorer för operativ system och arkitektur, diskar, nätverks inställningar och namngivning av virtuella datorer. Kontrol lera kraven för [virtuella VMware-datorer/fysiska servrar](migrate-support-matrix-vmware.md#azure-vm-requirements)och [virtuella Hyper-V-datorer](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) innan migreringen.


## <a name="prepare-to-connect-after-migration"></a>Förbered för att ansluta efter migreringen

Virtuella Azure-datorer skapas under migreringen till Azure. Efter migreringen måste du kunna ansluta till de nya virtuella Azure-datorerna. Det finns ett antal steg som krävs för att ansluta.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Förbered för att ansluta till virtuella Windows Azure-datorer

Gör följande på lokala Windows-datorer:

1. Konfigurera Windows-inställningar. Detta inkluderar att ta bort alla statiska beständiga vägar eller WinHTTP-proxy.
2. Kontrol lera att [dessa tjänster](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) körs.
3. Aktivera fjärr skrivbord (RDP) om du vill tillåta fjärr anslutningar till den lokala datorn. [Lär dig hur](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) du aktiverar RDP med PowerShell.
4. För att få åtkomst till en virtuell Azure-dator via Internet efter migreringen, i Windows-brandväggen på den lokala datorn, Tillåt TCP och UDP i den offentliga profilen och ange RDP som en tillåten app för alla profiler.
5. Om du vill få åtkomst till en virtuell Azure-dator via en plats-till-plats-VPN efter migreringen, kan du i Windows-brandväggen på den lokala datorn tillåta RDP för domänen och privata profiler. [Lär dig](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) hur du tillåter RDP-trafik. 
6. Se till att inga Windows-uppdateringar väntar på den lokala virtuella datorn när du migrerar. Om så är fallet kan uppdateringar börja installeras på den virtuella Azure-datorn efter migreringen och du kan inte logga in på den virtuella datorn förrän uppdateringarna har slutförts.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Förbered för att ansluta till virtuella Linux Azure-datorer

Gör följande på lokala Linux-datorer:

1. Kontrol lera att Secure Shell-tjänsten är inställd på att starta automatiskt vid system start.
2. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.

### <a name="configure-azure-vms-after-migration"></a>Konfigurera virtuella Azure-datorer efter migrering

Efter migreringen gör du följande på de virtuella Azure-datorer som skapas.

1. Om du vill ansluta till den virtuella datorn via Internet tilldelar du en offentlig IP-adress till den virtuella datorn. Du kan inte använda samma offentliga IP-adress för den virtuella Azure-dator som du använde för din lokala dator. [Läs mer](../virtual-network/virtual-network-public-ip-address.md).
2. Kontrol lera att reglerna för nätverks säkerhets gruppen (NSG) på den virtuella datorn tillåter inkommande anslutningar till RDP-eller SSH-porten.
3. Kontrol lera [startdiagnostik](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) för att visa den virtuella datorn.

> [!NOTE]
> Azure skydds-tjänsten erbjuder privat RDP och SSH-åtkomst till virtuella Azure-datorer. [Läs mer](../bastion/bastion-overview.md) om den här tjänsten.



## <a name="next-steps"></a>Nästa steg

Bestäm vilken metod du vill använda för att [migrera virtuella VMware-datorer](server-migrate-overview.md) till Azure, eller starta migrering av virtuella [Hyper-V-datorer](tutorial-migrate-hyper-v.md) eller [fysiska servrar eller virtuella datorer eller virtuella datorer i molnet](tutorial-migrate-physical-virtual-machines.md).
