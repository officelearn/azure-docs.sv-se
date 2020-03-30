---
title: Azure VMware Solution by CloudSimple – Säkerhetskopiera virtuella arbetsbelastningsdatorer i Private Cloud med Veeam
description: Beskriver hur du kan säkerhetskopiera dina virtuella datorer som körs i ett Azure-baserat CloudSimple Private Cloud med Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025137"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Säkerhetskopiera virtuella virtuella arbetsbelastningar på CloudSimple Private Cloud med Veeam B&R

Den här guiden beskriver hur du kan säkerhetskopiera dina virtuella datorer som körs i ett Azure-baserat CloudSimple Private Cloud med veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Om Veeam säkerhetskopiering och återställningslösning

Veeam-lösningen innehåller följande komponenter.

**Säkerhetskopieringsserver**

Säkerhetskopieringsservern är en Windows-server (VM) som fungerar som kontrollcenter för Veeam och utför dessa funktioner: 

* Koordinerar säkerhetskopiering, replikering, återställningsverifiering och återställning av uppgifter
* Styr finplanering och resursallokering
* Gör att du kan ställa in och hantera infrastrukturkomponenter för säkerhetskopiering och ange globala inställningar för infrastruktur för säkerhetskopiering

**Proxyservrar**

Proxyservrar installeras mellan säkerhetskopieringsservern och andra komponenter i infrastrukturen för säkerhetskopiering. De hanterar följande funktioner:

* Hämtning av VM-data från produktionslagringen
* Komprimering
* Deduplicering
* Kryptering
* Överföring av data till databasen för säkerhetskopiering

**Databas för säkerhetskopiering**

Databasen för säkerhetskopiering är lagringsplatsen där Veeam behåller säkerhetskopieringsfiler, VM-kopior och metadata för replikerade virtuella datorer.  Databasen kan vara en Windows- eller Linux-server med lokala diskar (eller monterade NFS/SMB) eller en maskinvarulagringsdedupliceringsinstallation.

### <a name="veeam-deployment-scenarios"></a>Veeam-distributionsscenarier
Du kan använda Azure för att tillhandahålla en lagringsplats för säkerhetskopiering och ett lagringsmål för långsiktig säkerhetskopiering och arkivering. All säkerhetskopiering nätverkstrafik mellan virtuella datorer i det privata molnet och säkerhetskopieringsdatabasen i Azure färdas över en länk med låg bandbredd, låg latens. Replikeringstrafik över regioner färdas över det interna Azure-backplane-nätverket, vilket sänker bandbreddskostnaderna för användarna.

**Grundläggande distribution**

För miljöer med mindre än 30 TB att säkerhetskopiera rekommenderar CloudSimple följande konfiguration:

* Veeam backup server och proxyserver installerad på samma virtuella dator i det privata molnet.
* En Linux-baserad primär säkerhetskopieringslagringsplats i Azure konfigurerad som ett mål för säkerhetskopieringsjobb.
* `azcopy`används för att kopiera data från den primära säkerhetskopian till en Azure-blob-behållare som replikeras till en annan region.

![Grundläggande distributionsscenarier](media/veeam-basicdeployment.png)

**Avancerad distribution**

För miljöer med mer än 30 TB att säkerhetskopiera rekommenderar CloudSimple följande konfiguration:

* En proxyserver per nod i vSAN-klustret, enligt Veeams rekommendation.
* Windows-baserad primär säkerhetskopia i det privata molnet för att cachelagra fem dagars data för snabb återställning.
* Linux-säkerhetskopieringslagringsplats i Azure som ett mål för säkerhetskopieringskopieringsjobb för längre kvarhållning. Den här databasen bör konfigureras som en utskalningsdatabas för säkerhetskopiering.
* `azcopy`används för att kopiera data från den primära säkerhetskopian till en Azure-blob-behållare som replikeras till en annan region.

![Grundläggande distributionsscenarier](media/veeam-advanceddeployment.png)

I föregående bild, observera att säkerhetskopian proxy är en virtuell dator med Hot Add tillgång till arbetsbelastning VM-diskar på vSAN datalager. Veeam använder alternativtransportläge för säkerhetskopiering av virtuella enheter för vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Krav för Veeam-lösning på CloudSimple

Veeam-lösningen kräver att du gör följande:

* Ange dina egna Veeam-licenser.
* Distribuera och hantera Veeam för att säkerhetskopiera arbetsbelastningar som körs i CloudSimple Private Cloud.

Den här lösningen ger dig full kontroll över Veeam backup-verktyget och erbjuder valet att använda det inbyggda Veeam-gränssnittet eller Veeam vCenter-plugin-programmet för att hantera vm-säkerhetskopieringsjobb.

Om du är en befintlig Veeam-användare kan du hoppa över avsnittet om Veeam-lösningskomponenter och direkt gå vidare till [Veeam Deployment Scenarios](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Installera och konfigurera Veeam-säkerhetskopior i ditt CloudSimple Private Cloud

I följande avsnitt beskrivs hur du installerar och konfigurerar en Veeam-säkerhetskopieringslösning för ditt CloudSimple Private Cloud.

Distributionsprocessen består av följande steg:

1. [vCenter UI: Konfigurera infrastrukturtjänster i ditt privata moln](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple portal: Konfigurera privata molnnätverk för Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple-portal: Eskalera privilegier](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure-portal: Anslut ditt virtuella nätverk till det privata molnet](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure-portal: Skapa en databas för säkerhetskopiering i Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure-portal: Konfigurera Azure blob-lagring för långsiktig datalagring](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter UI för privat moln: Installera Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam Console: Konfigurera Veeam Backup & Recovery programvara](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple-portal: Konfigurera Veeam-åtkomst och de-eskalera privilegier](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Innan du börjar

Följande krävs innan du påbörjar Veeam-distributionen:

* En Azure-prenumeration som ägs av dig
* En förskapad Azure-resursgrupp
* Ett virtuellt Azure-nätverk i din prenumeration
* Ett Azure Storage-konto
* Ett [privat moln](create-private-cloud.md) som skapats med CloudSimple-portalen.  

Följande punkter behövs under genomförandefasen:

* VMware-mallar för Windows för att installera Veeam (till exempel Windows Server 2012 R2 - 64-bitarsavbildning)
* En tillgänglig VLAN identifierad för säkerhetskopieringsnätverket
* CIDR för det undernät som ska tilldelas säkerhetskopieringsnätverket
* Veeam 9,5 u3 installerbara media (ISO) laddas upp till vSAN datalager av private cloud

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI: Konfigurera infrastrukturtjänster i ditt privata moln

Konfigurera infrastrukturtjänster i det privata molnet så att det blir enkelt att hantera dina arbetsbelastningar och verktyg.

* Du kan lägga till en extern identitetsprovider enligt beskrivningen i [Konfigurera vCenter-identitetskällor för att använda Active Directory](set-vcenter-identity.md) om något av följande gäller:

  * Du vill identifiera användare från din lokala Active Directory (AD) i ditt privata moln.
  * Du vill skapa en AD i ditt privata moln för alla användare.
  * Du vill använda Azure AD.
* Om du vill tillhandahålla IP-adresssökning, IP-adresshantering och namnmatchningstjänster för dina arbetsbelastningar i det privata molnet konfigurerar du en DHCP- och DNS-server enligt beskrivningen i [Konfigurera DNS- och DHCP-program och arbetsbelastningar i ditt CloudSimple Private Cloud](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Konfigurera privata molnnätverk för Veeam

Öppna CloudSimple-portalen för att konfigurera Private Cloud-nätverk för Veeam-lösningen.

Skapa en VLAN för säkerhetskopieringsnätverket och tilldela det ett undernät CIDR. Instruktioner finns i [Skapa och hantera VLAN/undernät](create-vlan-subnet.md).

Skapa brandväggsregler mellan hanteringsundernätet och säkerhetskopieringsnätverket så att nätverkstrafik tillåts på portar som används av Veeam. Se Veeam-avsnittet [Använda portar](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Instruktioner om hur brandväggsregel ska skapas finns i [Konfigurera brandväggstabeller och regler](firewall.md).

Följande tabell innehåller en portlista.

| Ikon | Beskrivning | Ikon | Beskrivning |
| ------------ | ------------- | ------------ | ------------- |
| Säkerhetskopieringsserver  | vCenter  | HTTPS / TCP  | 443 |
| Säkerhetskopieringsserver <br> *Krävs för att distribuera Veeam Backup & Replikeringskomponenter* | Proxy för säkerhetskopiering  | TCP/UDP  | 135, 137 till 139 och 445 |
    | Säkerhetskopieringsserver   | DNS  | UDP  | 53  | 
    | Säkerhetskopieringsserver   | Meddelandeserver för Veeam-uppdatering  | TCP  | 80  | 
    | Säkerhetskopieringsserver   | Uppdateringsserver för Veeam-licens  | TCP  | 443  | 
    | Proxy för säkerhetskopiering   | vCenter |   |   | 
    | Proxy för säkerhetskopiering  | Databas för säkerhetskopiering av Linux   | TCP  | 22  | 
    | Proxy för säkerhetskopiering  | Windows-säkerhetskopia  | TCP  | 49152 - 65535   | 
    | Databas för säkerhetskopiering  | Proxy för säkerhetskopiering  | TCP  | 2500 -5000  | 
    | Databas för säkerhetskopiering av källa<br> *Används för säkerhetskopieringskopieringsjobb*  | Säkerhetskopieringsdatabas för mål  | TCP  | 2500 - 5000  | 

Skapa brandväggsregler mellan arbetsbelastningsundernätet och säkerhetskopieringsnätverket enligt beskrivningen i [Konfigurera brandväggstabeller och regler](firewall.md).  För att programmedveten säkerhetskopiering och återställning ska vara inaktiverade måste [ytterligare portar](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) öppnas på virtuella arbetsbelastnings-virtuella datorer som är värdar för specifika program.

Som standard tillhandahåller CloudSimple en 1Gbps ExpressRoute-länk. För större miljöstorlekar kan en högre bandbreddslänk krävas. Kontakta Azure-supporten för mer information om länkar med högre bandbredd.

För att fortsätta installationen behöver du auktoriseringsnyckeln och peer circuit URI och åtkomst till din Azure-prenumeration.  Den här informationen är tillgänglig på sidan Anslutning till virtuellt nätverk i CloudSimple-portalen. Instruktioner finns i [Hämta peering-information för virtuella Azure-nätverk till CloudSimple-anslutning](virtual-network-connection.md). Om du har några problem med att få informationen [kontaktar du supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: Eskalera privilegier för cloudowner

Standardanvändaren för molnägare har inte tillräckliga privilegier i Private Cloud vCenter för att installera VEEAM, så användarens vCenter-privilegier måste eskaleras. Mer information finns i [Eskalera privilegier](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure-portal: Anslut ditt virtuella nätverk till det privata molnet

Anslut ditt virtuella nätverk till det privata molnet genom att följa instruktionerna i [Azure Virtual Network Connection med ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure portal: Skapa en vm-dator för säkerhetskopian

1. Skapa en standardD2 v3 VM med (2 vCPUs och 8 GB minne).
2. Välj den CentOS 7.4-baserade bilden.
3. Konfigurera en nätverkssäkerhetsgrupp (NSG) för den virtuella datorn. Kontrollera att den virtuella datorn inte har en offentlig IP-adress och inte kan nås från det offentliga internet.
4. Skapa ett användarnamns- och lösenordsbaserat användarkonto för den nya virtuella datorn. Instruktioner finns [i Skapa en virtuell Linux-dator i Azure-portalen](../virtual-machines/linux/quick-create-portal.md).
5. Skapa 1x512 GiB standard HDD och bifoga den till databasen VM.  Instruktioner finns i [Så här ansluter du en hanterad datadisk till en Windows VM i Azure-portalen](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Skapa en XFS-volym på den hanterade disken](https://www.digitalocean.com/docs/volumes/how-to/). Logga in på den virtuella datorn med de tidigare nämnda autentiseringsuppgifterna. Kör följande skript för att skapa en logisk volym, lägg till disken i den, skapa en XFS-filsystempartition och [montera](https://www.digitalocean.com/docs/volumes/how-to/mount/) partitionen under sökvägen /backup1. [partition](https://www.digitalocean.com/docs/volumes/how-to/partition/)

    Exempelskript:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Exponera /backup1 som en NFS-monteringspunkt till Veeam-säkerhetskopieringsservern som körs i det privata molnet. Instruktioner finns i Digital Ocean-artikeln [Hur du ställer in ett NFS-fäste på CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Använd det här NFS-resursnamnet när du konfigurerar säkerhetskopian i Veeam-säkerhetskopieringsservern.

8. Konfigurera filtreringsregler i NSG för den virtuella säkerhetskopian för att uttryckligen tillåta all nätverkstrafik till och från den virtuella datorn.

> [!NOTE]
> Veeam Backup & Replication använder SSH-protokollet för att kommunicera med Linux-säkerhetskopieringsdatabaser och kräver SCP-verktyget på Linux-databaser. Kontrollera att SSH-demonen är korrekt konfigurerad och att SCP är tillgängligt på Linux-värden.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurera Azure blob-lagring för långsiktig datalagring

1. Skapa ett GPv2-konto (General Purpose Storage Account) av standardtyp och en blob-behållare enligt beskrivningen i Microsoft-videon [Komma igång med Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Skapa en Azure-lagringsbehållare enligt beskrivningen i referensen [Skapa behållare.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Hämta `azcopy` kommandoradsverktyget för Linux från Microsoft. Du kan använda följande kommandon i bash-skalet i CentOS 7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Använd `azcopy` kommandot för att kopiera säkerhetskopior till och från blob-behållaren.  Se [Överföra data med AzCopy på Linux](../storage/common/storage-use-azcopy-linux.md) för detaljerade kommandon.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter-konsolen för privat moln: Installera Veeam B&R

Öppna vCenter från ditt privata moln för att skapa ett Veeam-tjänstkonto, installera Veeam B&R 9.5 och konfigurera Veeam med hjälp av tjänstkontot.

1. Skapa en ny roll med namnet Veeam Backup Role och tilldela den nödvändiga behörigheter som rekommenderas av Veeam. Mer information finns i Veeam-avsnittet [Krävs behörigheter](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Skapa en ny grupp veeam-användargrupp i vCenter och tilldela den "Veeam Backup Role".
3. Skapa en ny Veeam-tjänstkontoanvändare och lägg till det i användargruppen Veeam.

    ![Skapa ett Veeam-tjänstkonto](media/veeam-vcenter01.png)

4. Skapa en distribuerad portgrupp i vCenter med VLAN-säkerhetskopieringsnätverket. Mer information finns i VMware-videon [Skapa en distribuerad portgrupp i vSphere Web Client](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Skapa virtuella datorer för Veeam backup och proxyservrar i vCenter enligt [Veeam systemkrav](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Du kan använda Windows 2012 R2 eller Linux. Mer information finns i [Krav för att använda Linux-databaser för säkerhetskopiering](https://www.veeam.com/kb2216).
6. Montera den installerbara Veeam ISO som en CDROM-enhet i Veeam backup server VM.
7. Installera [Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) i en Windows 2012 R2-dator med hjälp av en RDP-session till Windows 2012 R2-datorn (målet för Veeam-installationen).
8. Hitta den interna IP-adressen för veeam backup server VM och konfigurera IP-adressen så att den är statisk i DHCP-servern. De exakta stegen som krävs för att göra detta beror på DHCP-servern. I netgate-artikelns <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">statiska DHCP-mappningar förklaras</a> till exempel hur du konfigurerar en DHCP-server med hjälp av en pfSense-router.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam konsol: Installera Veeam backup och återvinning programvara

Konfigurera Veeam-säkerhetskopierings- och återställningsprogram med Veeam-konsolen. Mer information finns i [Veeam Backup & Replication v9 - Installation och distribution](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Lägg till VMware vSphere som en hanterad servermiljö. När du uppmanas till det veeam-tjänstkonto som du skapade i början av [vCenter Console of Private Cloud: Install Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Använd standardinställningarna för lastkontroll och avancerade standardinställningar.
    * Ange att monteringsserverns plats ska vara säkerhetskopieringsservern.
    * Ändra platsen för säkerhetskopiering av konfiguration för Veeam-servern till fjärrdatabasen.

2. Lägg till Linux-servern i Azure som säkerhetskopieringsdatabas.

    * Använd standardinställningarna för lastkontroll och för de avancerade inställningarna. 
    * Ange att monteringsserverns plats ska vara säkerhetskopieringsservern.
    * Ändra platsen för säkerhetskopiering av konfiguration för Veeam-servern till fjärrdatabasen.

3. Aktivera kryptering av säkerhetskopiering av konfiguration med **start> inställningar för säkerhetskopiering av konfiguration**.

4. Lägg till en virtuell Windows-server som proxyserver för VMware-miljö. Kryptera säkerhetskopieringsdata över kabeln med hjälp av "Trafikregler" för en proxy.

5. Konfigurera säkerhetskopieringsjobb.
    * Om du vill konfigurera säkerhetskopieringsjobb följer du instruktionerna i [Skapa ett säkerhetskopieringsjobb](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Aktivera kryptering av säkerhetskopieringsfiler under **Avancerade inställningar > lagring**.

6. Konfigurera säkerhetskopieringskopieringsjobb.

    * Om du vill konfigurera säkerhetskopieringskopieringsjobb följer du instruktionerna i videon [Skapa ett säkerhetskopieringskopieringsjobb](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Aktivera kryptering av säkerhetskopieringsfiler under **Avancerade inställningar > lagring**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple-portal: Konfigurera Veeam-åtkomst och de-eskalera privilegier
Skapa en offentlig IP-adress för Veeam-säkerhetskopierings- och återställningsservern. Instruktioner finns i [Tilldela offentliga IP-adresser](public-ips.md).

Skapa en brandväggsregel som använder veeam-säkerhetskopieringsservern för att skapa en utgående anslutning till Veeams webbplats för nedladdning av uppdateringar/korrigeringsfiler på TCP-port 80. Instruktioner finns i [Konfigurera brandväggstabeller och regler](firewall.md).

Om du vill de-eskalera privilegier läser du [De-eskalera privilegier](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Referenser

### <a name="cloudsimple-references"></a>CloudSimple-referenser

* [Skapa ett privat moln](create-private-cloud.md)
* [Skapa och hantera VLAN/undernät](create-vlan-subnet.md)
* [vCenter-identitetskällor](set-vcenter-identity.md)
* [Dns- och DHCP-inställningar för arbetsbelastning](dns-dhcp-setup.md)
* [Eskalera privilegier](escalate-privileges.md)
* [Konfigurera brandväggstabeller och -regler](firewall.md)
* [Privata molnbehörigheter](learn-private-cloud-permissions.md)
* [Allokera offentliga IP-adresser](public-ips.md)

### <a name="veeam-references"></a>Veeam Referenser

* [Använda portar](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Nödvändiga behörigheter](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Systemkrav](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Installera Veeam Backup & Replication](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Obligatoriska moduler och behörigheter för stöd för flr för flera operativsystem och databas för Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9 - Installation och distribution - Video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Skapa en backup jobb - Video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Skapa en backup kopiera jobb - Video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-referenser

* [Konfigurera en virtuell nätverksgateway för ExpressRoute med Azure-portalen](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Anslut ett VNet till en krets - olika abonnemang](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Skapa en virtuell Linux-dator i Azure-portalen](../virtual-machines/linux/quick-create-portal.md)
* [Bifoga en hanterad datadisk till en Windows-virtuell dator i Azure-portalen](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Komma igång med Azure Storage – Video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Skapa container](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Överföra data med AzCopy i Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>VMware referenser

* [Skapa en distribuerad portgrupp i vSphere Web Client - Video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Andra referenser

* [Skapa en XFS-volym på den hanterade disken - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Så här ställer du in ett NFS-fäste på CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurera DHCP-servern - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
