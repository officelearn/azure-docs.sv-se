---
title: Vanliga frågor och svar om Azure NetApp-filer | Microsoft Docs
description: Svar vanliga frågor och svar om Azure NetApp-filer.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806393"
---
# <a name="faqs-about-azure-netapp-files"></a>Vanliga frågor och svar om Azure NetApp-filer

Den här artikeln får du svar på vanliga frågor och svar (FAQ) om Azure NetApp-filer. 

## <a name="networking-faqs"></a>Nätverk vanliga frågor och svar

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>NFS-datasökväg går via Internet?  

Nej. NFS-datasökväg går inte via Internet. Azure NetApp Files är en Azure intern tjänst som distribueras till Azure Virtual Network (VNet) där tjänsten är tillgänglig. Azure NetApp-filerna använder ett delegerade undernät och etablerar ett nätverksgränssnitt direkt på det virtuella nätverket. 

Se [riktlinjer för Azure NetApp Files network planera](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) mer information.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan jag ansluta ett virtuellt nätverk som jag redan har skapat till Azure NetApp Files-tjänsten?

Ja, kan du ansluta virtuella nätverk som du skapade till tjänsten. 

Se [riktlinjer för Azure NetApp Files network planera](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) mer information.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan jag montera en NFS-volym av Azure NetApp filer med hjälp av DNS-FQDN-namn?

Ja, du kan, om du skapar de nödvändiga DNS-posterna. NetApp-filer som Azure tillhandahåller IP-Adressen för tjänsten för den etablerade volymen. 

> [!NOTE] 
> Azure NetApp-filer kan distribuera ytterligare IP-adresser för tjänsten efter behov.  DNS-poster kan behöva uppdateras regelbundet.

## <a name="security-faqs"></a>Vanliga frågor om Security

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan nätverkstrafik mellan virtuella Azure-datorer och lagring krypteras?

Datatrafik (trafik från NFSv3 eller SMBv3-klient till Azure NetApp Files volymer) krypteras inte. Trafiken från en Azure-dator (som kör en NFS- eller SMB-klient) till Azure NetApp filer är dock lika säkra som andra Azure-VM-till-VM-trafik. Den här trafiken är lokal till Azure Datacenter-nätverk. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan lagring krypteras i vila?

Alla Azure NetApp Files volymer krypteras med FIPS 140-2-standarden. Alla nycklar som hanteras av tjänsten Azure NetApp-filer. 

### <a name="how-are-encryption-keys-managed"></a>Hur hanteras krypteringsnycklar? 

Nyckelhantering för Azure NetApp filer hanteras av tjänsten.  Användare-hanterade nycklar (ta med din egen nycklar) stöds för närvarande inte.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan jag konfigurera principregler för NFS export för att styra åtkomsten till monteringsmålet Azure NetApp Files-tjänsten?


Ja, du kan konfigurera upp till fem regler i en enda NFS exportera princip.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Stöder Azure NetApp Files Nätverkssäkerhetsgrupper?

Nej, för närvarande du kan inte använda Nätverkssäkerhetsgrupper delegerade undernätet för Azure NetApp filer eller nätverksgränssnitt som skapats av tjänsten.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Kan jag använda Azure IAM med Azure NetApp Files?

Ja, stöder Azure NetApp Files RBAC-funktioner med Azure IAM.

## <a name="performance-faqs"></a>Prestanda vanliga frågor och svar

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Vad gör jag för att optimera eller justera prestanda för Azure NetApp Files?

Du kan utföra följande åtgärder per prestandakraven: 
- Se till att den virtuella datorn är lämplig storlek.
- Aktivera Accelererat nätverk för den virtuella datorn.
- Välj önskad servicenivå och storlek för poolen kapacitet.
- Skapa en volym med önskad kvot-storlek för kapacitet och prestanda.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hur konverterar dataflöde-baserad tjänstenivåer av Azure NetApp filer till IOPS?

Du kan konvertera MB/s till IOPS med hjälp av följande formel:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hur ändrar jag servicenivå på en volym

Ändra servicenivån för en volym stöds inte för närvarande.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hur övervakar jag Azure NetApp Files prestanda?

NetApp-filer som Azure tillhandahåller resultatmått för volymen. Du kan också använda Azure Monitor för att övervaka användningsstatistik för Azure NetApp-filer.  Se [mätvärden för Azure NetApp Files](azure-netapp-files-metrics.md) lista över prestandamått för Azure NetApp-filer.

## <a name="nfs-faqs"></a>NFS vanliga frågor och svar

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Jag vill ha en volym monteras automatiskt när en Azure VM är igång eller startas om.  Hur konfigurerar jag min värden för beständiga NFS-volymer?

För en NFS-volymen automatiskt montera på VM start eller starta om datorn, lägger du till en post i `/etc/fstab` fil på värden. 

Exempel: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    IP-adressen för Azure NetApp Files volymen hittades i egenskapsbladet för volym
- $FILEPATH  
    Exportsökvägen volymens Azure NetApp filer
- $MOUNTPOINT  
    Den katalog som skapades på Linux-värden som används för att montera NFS-export

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Varför kommandot DF på NFS-klienten visas inte den installerade volymstorleken?

Volymens storlek rapporteras i DF är den maximala storleken som Azure NetApp Files volymen kan växa till. Storleken på volymen Azure NetApp filer i DF-kommandot är inte reflekterande av kvoten eller volymens storlek.  Du kan få Azure NetApp Files volymstorleken eller kvot via Azure portal eller API: et.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Vilken NFS-version stöder Azure NetApp Files?

Azure NetApp-filer stöder för närvarande NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Hur aktiverar jag rot ihoptryckning?

Rot ihoptryckning stöds för närvarande inte.

## <a name="smb-faqs"></a>SMB vanliga frågor och svar

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Azure NetApp filer som har stöd för Azure Active Directory?

Nej, det stöds inte för närvarande.  Azure NetApp-filer stöder Active Directory Domain Services (ta med dina egna AD), som kan använda befintliga Active Directory-domänkontrollanter med Azure NetApp-filer. Domänkontrollanter kan finnas i Azure som virtuella datorer eller lokalt via ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Är en Active Directory-anslutning som krävs för SMB-åtkomst? 

Ja, måste du skapa en Active Directory-anslutning innan du distribuerar en SMB-volym. Den angivna domänkontrollanter måste vara tillgängliga för delegerade undernätet för Azure NetApp-filer för en lyckad anslutning.  Se [skapa en SMB-volym](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) mer information. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hur många Active Directory-anslutningar stöds?

Azure NetApp-filer stöder för närvarande en Active Directory-anslutning per prenumeration. Dessutom är Active Directory-anslutningen specifik för ett enda konto NetApp; den delas inte mellan konton. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Vilka versioner av Windows AD stöds?

Azure NetApp-filer har stöd för Windows Server 2008r2SP1 2016-versionen av Active Directory Domain Services.

## <a name="capacity-management-faqs"></a>Kapacitetshantering vanliga frågor och svar

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hur övervakar användningen för poolen för kapacitet och mängden Azure NetApp Files? 

Azure NetApp Files tillhandahåller pool och volym kapacitetsanvändningsmåtten. Du kan också använda Azure Monitor för att övervaka förbrukningen för Azure NetApp-filer. Se [mätvärden för Azure NetApp Files](azure-netapp-files-metrics.md) mer information. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan jag hantera NetApp Azure Files via Azure Storage Explorer?

Nej. Azure NetApp-filer stöds inte av Azure Storage Explorer.

## <a name="data-migration-and-protection-faqs"></a>Vanliga frågor migrering och skydd av data

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hur migrerar data till Azure NetApp Files?
Azure NetApp Files tillhandahåller NFS och SMB-volymer.  Du kan använda valfritt filbaserade kopiera verktyg för att migrera data till tjänsten. 

NetApp erbjuder en SaaS-baserad lösning [NetApp Molnsynkronisering](https://cloud.netapp.com/cloud-sync-service).  Den här lösningen kan du replikera NFS eller SMB-data till Azure NetApp filer NFS export eller SMB-filresurser. 

Du kan också använda en mängd olika kostnadsfria verktyg för att kopiera data. För NFS, kan du använda verktyg för arbetsbelastningar som [rsync](https://rsync.samba.org/examples.html) att kopiera och synkronisera källdata till en Azure NetApp Files-volym. Du kan använda arbetsbelastningar för SMB, [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan även replikera filens eller mappens behörigheter. 

Krav för migrering av data från lokalt till Azure NetApp Files är följande: 

- Kontrollera att Azure NetApp filer är tillgängliga i mål-Azure-region.
- Validera nätverksanslutningen mellan käll- och Azure NetApp Files volym IP-måladressen. Överföra data mellan lokalt och tjänsten Azure NetApp filer stöds via ExpressRoute.
- Skapa mål Azure NetApp Files volym.
- Överföra källdata till målvolymen med hjälp av din önskade filkopieringsverktyg.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hur skapar jag en kopia av en Azure NetApp Files-volym i en annan Azure-region?
    
Azure NetApp Files tillhandahåller NFS och SMB-volymer.  Alla baserat-filkopieringsverktyg kan användas för att replikera data mellan Azure-regioner. 

NetApp är en SaaS-baserad lösning, [NetApp Molnsynkronisering](https://cloud.netapp.com/cloud-sync-service).  Den här lösningen kan du replikera NFS eller SMB-data till Azure NetApp filer NFS export eller SMB-filresurser. 

Du kan också använda en mängd olika kostnadsfria verktyg för att kopiera data. För NFS, kan du använda verktyg för arbetsbelastningar som [rsync](https://rsync.samba.org/examples.html) att kopiera och synkronisera källdata till en Azure NetApp Files-volym. Du kan använda arbetsbelastningar för SMB, [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) på samma sätt.  Dessa verktyg kan även replikera filens eller mappens behörigheter. 

Kraven för att replikera en Azure NetApp Files-volym till en annan Azure-region är följande: 
- Kontrollera att Azure NetApp filer är tillgängliga i mål-Azure-region.
- Validera nätverksanslutningen mellan virtuella nätverk i varje region. För närvarande stöds global peering mellan virtuella nätverk inte.  Du kan upprätta en anslutning mellan virtuella nätverk genom att länka med en ExpressRoute-krets eller med hjälp av en S2S VPN-anslutning. 
- Skapa mål Azure NetApp Files volym.
- Överföra källdata till målvolymen med hjälp av din önskade filkopieringsverktyg.

### <a name="is-migration-with-azure-data-box-supported"></a>Är migrering med Azure Data Box stöds?

Nej. Azure Data Box stöder för närvarande inte Azure NetApp-filer. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Är migrering med Azure Import/Export-tjänsten stöds?

Nej. Azure Import/Export-tjänsten stöder för närvarande inte Azure NetApp-filer.

## <a name="next-steps"></a>Nästa steg  

- [Microsoft Azure ExpressRoute vanliga frågor och svar](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure-nätverk vanliga frågor och svar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Skapa en supportbegäran för Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)