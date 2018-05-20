---
title: Säkerhetskopiera Azure Stack-filer och program | Microsoft Docs
description: Använda Azure Backup för att säkerhetskopiera och återställa Azure Stack-filer och program till Azure Stack-miljön.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Säkerhetskopiera filer och program på Azure-stacken
Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure-stacken. Installera Microsoft Azure Backup Server för att säkerhetskopiera filer och program som en virtuell dator som körs på Azure-stacken. Du kan skydda alla program som körs på någon Azure Stack-server i samma virtuella nätverk. När du har installerat Azure Backup-Server, Lägg till Azure-diskar för att öka den lokala lagringen tillgänglig för kortsiktig säkerhetskopiering. Azure Backup-servern använder Azure-lagring för långsiktig kvarhållning.

> [!NOTE]
> Även om Azure Backup Server och System Center Data Protection Manager (DPM) är liknande, finns DPM inte stöd för användning med Azure-stacken.
>


## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server
Azure Backup-Server skyddar följande Azure Stack virtuella arbetsbelastningar.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Windows Server semikolon årligt kanal - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2016 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2012 R2 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2012 - Datacenter-Entprise-Standard | Volymer, filer, mappar |
| Windows Server 2008 R2 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2013 | Servergrupp, databas, klientdel, webbserver |
| SharePoint 2010 | Servergrupp, databas, klientdel, webbserver |


## <a name="install-azure-backup-server"></a>Installera Azure Backup-Server
Om du vill installera Azure Backup Server på en virtuell dator i Azure-stacken, finns i artikeln [förbereder för att säkerhetskopiera arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md). Innan du installerar och konfigurerar Azure Backup Server, Tänk på följande:

### <a name="determining-size-of-virtual-machine"></a>Bestämma storleken på virtuella datorn
Kör Azure Backup Server på en virtuell dator i Azure-stacken med storlek A2 eller större. Hjälp med att välja en storlek på virtuell dator, hämtar den [Kalkylatorn för storlek på Azure-stacken VM](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på Azure-stacken virtuella datorer
Alla virtuella datorer som används i en arbetsbelastning i Azure-stacken måste tillhöra samma virtuella Azure-nätverket och Azure-prenumeration.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhetskopierade data på lokal disk och i Azure
Azure Backup-servern lagrar säkerhetskopierade data på Azure diskar som är anslutna till den virtuella datorn för operativa återställning. Diskarna och lagringsutrymmet är kopplade till den virtuella datorn hanterar Azure Backup Server lagring för dig. Hur mycket lagringsutrymme för säkerhetskopierade data beror på antalet och storleken på diskar som är anslutna till varje [virtuella Azure-stacken](../azure-stack/user/azure-stack-storage-overview.md). Varje storleken på stacken virtuella Azure-datorn har ett maximalt antal diskar som kan kopplas till den virtuella datorn. A2 är till exempel fyra diskar. A3 är åtta diskar. A4 är 16 diskar. Igen, storlek och antalet diskar avgör den totala lagringspoolen för säkerhetskopiering.

> [!IMPORTANT]
> Du bör **inte** behålla operativa återställningsdata (säkerhetskopiering) på Azure Backup-Server-anslutna diskar för mer än fem dagar.
>

Lagra säkerhetskopierade data i Azure minskar infrastrukturen för säkerhetskopiering på Azure-stacken. Om data är mer än fem dagar gammal, bör det lagras i Azure.

Skapa för att lagra säkerhetskopierade data i Azure, eller Använd Recovery Services-valvet. När du förbereder att säkerhetskopiera arbetsbelastningen Azure Backup Server du [konfigurera Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). När du konfigurerat varje gång en säkerhetskopiering körs skapas en återställningspunkt i valvet. Varje Recovery Services-valvet innehåller upp till 9999 återställningspunkter. Du kan behålla säkerhetskopierade data i många år beroende på antalet återställningspunkter som skapats och hur länge de bevaras. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.
 
### <a name="using-sql-server"></a>Använda SQLServer
Välj endast en Azure-stacken virtuell dator som kör SQL Server om du vill använda en fjärransluten SQL Server för Azure Backup Server-databasen.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestanda
Om delas med andra virtuella datorer, kan lagringsstorleken för kontot och IOPS-gränser påverka prestanda för virtuella datorer i Azure Backup Server. Därför bör du använda ett separat lagringskonto för den virtuella datorn i Azure Backup Server. Azure Backup-agenten körs på Azure Backup-servern behöver tillfällig lagring för:
    - egen användning (en cacheplats)
    - data återställs från molnet (lokalt mellanlagringsområde)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup tillfälliga disklagring
Varje virtuell dator i Azure-stacken medföljer tillfälliga diskutrymme, som är tillgängliga för användaren som volym `D:\`. Det lokala mellanlagringsområdet som krävs av Azure Backup kan konfigureras så att det finns i `D:\`, och cacheplatsen kan placeras på `C:\`. På så sätt behöver ingen lagring hämtas från datadiskar som är kopplade till den virtuella datorn i Azure Backup Server.

### <a name="scaling-deployment"></a>Skalning distribution
Om du vill skala distributionen har följande alternativ:
  - Skala upp – öka storleken på Azure Backup Server virtuell dator från en serie D-serien och öka den lokala lagringen [per Azure Stack virtuella instruktioner](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Omfördela data: skicka äldre data till Azure Backup Server och spara endast senaste data på det lagringsutrymme som är kopplade till Azure Backup-Server.
  - Skala ut – Lägg till fler Azure Backup-servrar för att skydda arbetsbelastningarna.

## <a name="see-also"></a>Se också
Information om hur du använder Azure Backup Server för att skydda finns andra arbetsbelastningar i följande artiklar:
- [Säkerhetskopiera SharePoint-grupp](backup-azure-backup-sharepoint-mabs.md)
- [Säkerhetskopiera SQLServer](backup-azure-sql-mabs.md)
