---
title: Vanliga frågor om Azure NetApp-filer | Microsoft-dokument
description: Svar vanliga frågor om Azure NetApp-filer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: b-juche
ms.openlocfilehash: 59453dbb9617f5a2ddbdca08709747b11a688560
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547092"
---
# <a name="faqs-about-azure-netapp-files"></a>Vanliga frågor om Azure NetApp-filer

Den här artikeln besvarar vanliga frågor och svar om Vanliga frågor och svar om Azure NetApp-filer. 

## <a name="networking-faqs"></a>Vanliga frågor om nätverk

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Går NFS-datasökvägen över Internet?  

Nej. NFS-datasökvägen går inte över Internet. Azure NetApp Files är en inbyggd Azure-tjänst som distribueras till Azure Virtual Network (VNet) där tjänsten är tillgänglig. Azure NetApp Files använder ett delegerat undernät och etablerar ett nätverksgränssnitt direkt på det virtuella nätverket. 

Mer information [finns i Riktlinjer för nätverksplanering för Azure NetApp-filer.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan jag ansluta ett virtuella nätverk som jag redan har skapat till Azure NetApp Files-tjänsten?

Ja, du kan ansluta virtuella nätverk som du har skapat till tjänsten. 

Mer information [finns i Riktlinjer för nätverksplanering för Azure NetApp-filer.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan jag montera en NFS-volym med Azure NetApp-filer med DNS FQDN-namn?

Ja, du kan, om du skapar de dns-poster som krävs. Azure NetApp Files tillhandahåller tjänst-IP för den etablerade volymen. 

> [!NOTE] 
> Azure NetApp-filer kan distribuera ytterligare IPs för tjänsten efter behov.  DNS-poster kan behöva uppdateras med jämna mellanrum.

## <a name="security-faqs"></a>Vanliga frågor om säkerhet

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan nätverkstrafiken mellan den virtuella Azure-datorn och lagringen krypteras?

Datatrafik (trafik från NFSv3-, NFSv4.1- eller SMBv3-klienten till Azure NetApp Files-volymer) är inte krypterad. Trafiken från en Virtuell Azure-dator (som kör en NFS- eller SMB-klient) till Azure NetApp-filer är dock lika säker som alla andra Azure-VM-till-VM-trafik. Den här trafiken är lokal för Azure-datacenternätverket. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan lagringen krypteras i vila?

Alla Azure NetApp-filers volymer krypteras med FIPS 140-2-standarden. Alla nycklar hanteras av Azure NetApp Files-tjänsten. 

### <a name="how-are-encryption-keys-managed"></a>Hur hanteras krypteringsnycklar? 

Nyckelhantering för Azure NetApp-filer hanteras av tjänsten. En unik XTS-AES-256-datakrypteringsnyckel genereras för varje volym. En krypteringsnyckelhierarki används för att kryptera och skydda alla volymnycklar. Dessa krypteringsnycklar visas aldrig eller rapporteras i okrypterat format. Krypteringsnycklar tas bort direkt när en volym tas bort.

Stöd för användarhanterade nycklar (Bring Your Own Keys) med Azure Dedicated HSM är tillgängligt på kontrollerad basis i regionerna USA East, US West2 och US South Central.  Du kan begära **anffeedback@microsoft.com**åtkomst på . Eftersom kapacitet är tillgänglig godkänns begäranden.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan jag konfigurera NFS-exportprincipreglerna för att styra åtkomsten till Azure NetApp Files-tjänstens monteringsmål?


Ja, du kan konfigurera upp till fem regler i en enda NFS-exportprincip.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Stöder Azure NetApp Files nätverkssäkerhetsgrupper?

Nej, för närvarande kan du inte använda nätverkssäkerhetsgrupper i det delegerade undernätet i Azure NetApp Files eller de nätverksgränssnitt som skapats av tjänsten.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Kan jag använda Azure IAM med Azure NetApp-filer?

Ja, Azure NetApp Files stöder RBAC-funktioner med Azure IAM.

## <a name="performance-faqs"></a>Vanliga frågor om prestanda

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Vad ska jag göra för att optimera eller justera Azure NetApp Files-prestanda?

Du kan vidta följande åtgärder enligt prestandakraven: 
- Kontrollera att den virtuella datorn är korrekt dimensionerad.
- Aktivera accelererat nätverk för den virtuella datorn.
- Välj önskad servicenivå och storlek för kapacitetspoolen.
- Skapa en volym med önskad kvotstorlek för kapacitet och prestanda.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hur konverterar jag dataflödesbaserade tjänstnivåer för Azure NetApp-filer till IOPS?

Du kan konvertera MB/s till IOPS med hjälp av följande formel:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hur ändrar jag servicenivån för en volym?

Det går inte att ändra servicenivån för en volym.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hur övervakar jag Azure NetApp Files-prestanda?

Azure NetApp Files tillhandahåller mätvärden för volymprestanda. Du kan också använda Azure Monitor för att övervaka användningsmått för Azure NetApp-filer.  Se [Mått för Azure NetApp-filer](azure-netapp-files-metrics.md) för listan över prestandamått för Azure NetApp-filer.

## <a name="nfs-faqs"></a>Vanliga frågor och frågor om NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Jag vill ha en volym monterad automatiskt när en Azure VM startas eller startas om.  Hur konfigurerar jag värden för beständiga NFS-volymer?

Lägg till en post i `/etc/fstab` filen på värden om en NFS-volym automatiskt ska monteras vid vm-start eller omstart. 

Mer information finns i [Montera eller avmontera en volym för virtuella Windows- eller Linux-datorer.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Varför visar inte DF-kommandot på NFS-klienten den etablerade volymstorleken?

Volymstorleken som rapporteras i DF är den maximala storleken som Azure NetApp Files-volymen kan växa till. Storleken på Azure NetApp Files-volymen i DF-kommandot återspeglar inte kvoten eller storleken på volymen.  Du kan hämta storleken eller kvoten för Azure NetApp Files via Azure-portalen eller API:et.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Vilken NFS-version stöder Azure NetApp Files?

Azure NetApp Files stöder NFSv3 och NFSv4.1. Du kan [skapa en volym](azure-netapp-files-create-volumes.md) med någon av NFS-versionerna. 

### <a name="how-do-i-enable-root-squashing"></a>Hur aktiverar jag root squashing?

Root squash-blandning stöds för närvarande inte.

## <a name="smb-faqs"></a>Vanliga frågor om SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Krävs en Active Directory-anslutning för SMB-åtkomst? 

Ja, du måste skapa en Active Directory-anslutning innan du distribuerar en SMB-volym. De angivna domänkontrollanterna måste vara tillgängliga via det delegerade undernätet i Azure NetApp-filer för en lyckad anslutning.  Mer information finns i [Skapa en SMB-volym.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 

### <a name="how-many-active-directory-connections-are-supported"></a>Hur många Active Directory-anslutningar stöds?

Azure NetApp-filer stöder inte flera ACTIVE Directory-anslutningar (AD) i en enda *region*, även om AD-anslutningarna finns i olika NetApp-konton. Du kan dock ha flera AD-anslutningar i en enda *prenumeration*, så länge AD-anslutningarna finns i olika regioner. Om du behöver flera AD-anslutningar i en enda region kan du använda separata prenumerationer för att göra det. 

En AD-anslutning konfigureras per NetApp-konto. AD-anslutningen visas endast via det NetApp-konto som den skapas i.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Stöder Azure NetApp Files Azure Active Directory? 

Både [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) och Active Directory Domain Services [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) stöds. Du kan använda befintliga Active Directory-domänkontrollanter med Azure NetApp-filer. Domänkontrollanter kan finnas i Azure som virtuella datorer eller lokalt via ExpressRoute eller S2S VPN. Azure NetApp-filer stöder inte AD-anslutning för [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) just nu.

Om du använder Azure NetApp-filer med Azure Active Directory `OU=AADDC Computers` Domain Services är sökvägen till organisationsenheten när du konfigurerar Active Directory för ditt NetApp-konto.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Vilka versioner av Active Directory för Windows Server stöds?

Azure NetApp Files stöder Windows Server 2008r2SP1-2019-versioner av Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Varför visar inte det tillgängliga utrymmet på min SMB-klient den etablerade storleken?

Volymstorleken som rapporteras av SMB-klienten är den maximala storleken som Azure NetApp Files-volymen kan växa till. Storleken på Azure NetApp Files-volymen som visas på SMB-klienten återspeglar inte kvoten eller storleken på volymen. Du kan hämta storleken eller kvoten för Azure NetApp Files via Azure-portalen eller API:et.

### <a name="does-azure-netapp-files-support-kerberos-encryption"></a>Stöder Azure NetApp Files Kerberos-kryptering?

Ja, som standard stöder Azure NetApp Files både AES-128- och AES-256-kryptering för trafik mellan tjänsten och de riktade Active Directory-domänkontrollanterna. Se [Skapa en SMB-volym för Azure NetApp-filer](azure-netapp-files-create-volumes-smb.md) för krav. 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Vanliga frågor om kapacitetshantering

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hur övervakar jag användningen för kapacitetspool och volymen för Azure NetApp-filer? 

Azure NetApp Files tillhandahåller kapacitetspool och volymanvändningsmått. Du kan också använda Azure Monitor för att övervaka användningen av Azure NetApp-filer. Mer information finns [i Mått för Azure NetApp-filer.](azure-netapp-files-metrics.md) 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan jag hantera Azure NetApp-filer via Azure Storage Explorer?

Nej. Azure NetApp-filer stöds inte av Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hur avgör jag om en katalog närmar sig gränsstorleken?

Du kan `stat` använda kommandot från en klient för att se om en katalog närmar sig den maximala storleksgränsen (320 MB).

För en 320 MB katalog är antalet block 655360, där varje blockstorlek är 512 byte.  (Det vill vara, 320x1024x1024/512.)  

Exempel:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Vanliga frågor om datamigrering och dataskydd

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hur migrerar jag data till Azure NetApp-filer?
Azure NetApp Files tillhandahåller NFS- och SMB-volymer.  Du kan använda valfritt filbaserat kopieringsverktyg för att migrera data till tjänsten. 

NetApp erbjuder en SaaS-baserad lösning, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Lösningen gör att du kan replikera NFS- eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda ett brett utbud av kostnadsfria verktyg för att kopiera data. För NFS kan du använda arbetsbelastningsverktyg som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera källdata till en Azure NetApp Files-volym. För SMB kan du använda arbetsbelastningar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil- eller mappbehörigheter. 

Kraven för datamigrering från lokalt till Azure NetApp-filer är följande: 

- Se till att Azure NetApp-filer är tillgängliga i Azure-regionen för målet.
- Validera nätverksanslutningen mellan källan och Azure NetApp Files målvolym IP-adress. Dataöverföring mellan lokalt och Azure NetApp Files-tjänsten stöds via ExpressRoute.
- Skapa volymen azure netapp-filer för målet.
- Överför källdata till målvolymen med hjälp av önskat filkopieringsverktyg.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hur skapar jag en kopia av en Azure NetApp-filvolym i en annan Azure-region?
    
Azure NetApp Files tillhandahåller NFS- och SMB-volymer.  Alla filbaserade kopieringsverktyg kan användas för att replikera data mellan Azure-regioner. 

NetApp erbjuder en SaaS-baserad lösning, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Lösningen gör att du kan replikera NFS- eller SMB-data till Azure NetApp Files NFS-exporter eller SMB-resurser. 

Du kan också använda ett brett utbud av kostnadsfria verktyg för att kopiera data. För NFS kan du använda arbetsbelastningsverktyg som [rsync](https://rsync.samba.org/examples.html) för att kopiera och synkronisera källdata till en Azure NetApp Files-volym. För SMB kan du använda arbetsbelastningar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan också replikera fil- eller mappbehörigheter. 

Kraven för att replikera en Azure NetApp-filvolym till en annan Azure-region är följande: 
- Se till att Azure NetApp-filer är tillgängliga i Azure-regionen för målet.
- Verifiera nätverksanslutningen mellan virtuella nätverk i varje region. För närvarande stöds inte global peering mellan virtuella nätverk.  Du kan upprätta anslutning mellan virtuella nätverk genom att länka till en ExpressRoute-krets eller använda en S2S VPN-anslutning. 
- Skapa volymen azure netapp-filer för målet.
- Överför källdata till målvolymen med hjälp av önskat filkopieringsverktyg.

### <a name="is-migration-with-azure-data-box-supported"></a>Stöds migreringen med Azure Data Box?

Nej. Azure Data Box stöder inte Azure NetApp-filer för närvarande. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Stöds migreringen med Azure Import/Export-tjänsten?

Nej. Azure Import/Export-tjänsten stöder inte Azure NetApp-filer för närvarande.

## <a name="next-steps"></a>Nästa steg  

- [Vanliga frågor om Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Vanliga frågor och svar om Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Skapa en supportbegäran för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Vanliga frågor om SMB-prestanda för Azure NetApp-filer](azure-netapp-files-smb-performance.md)
