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
ms.date: 04/20/2018
ms.author: adigan,markgal
ms.openlocfilehash: 905f6b13928d11243202059af0ad255971102da8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Säkerhetskopiera filer och program på Azure-stacken
Du kan använda Azure Backup för att skydda (eller säkerhetskopiera) filer och program på Azure-stacken. Installera Microsoft Azure Backup Server för att säkerhetskopiera filer och program som en virtuell dator som körs på Azure-stacken. När du har installerat Azure Backup-Server, Lägg till Azure-diskar för att öka den lokala lagringen tillgänglig för kortsiktig säkerhetskopiering. Azure Backup-servern använder Azure-lagring för långsiktig kvarhållning.

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
| Systemtillstånd | Systemtillstånd |
| Återställning utan operativsystem (BMR) | BMR, systemtillstånd, filer, mappar | 

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

Skapa för att lagra säkerhetskopierade data i Azure, eller Använd Recovery Services-valvet. När du förbereder att säkerhetskopiera arbetsbelastningen Azure Backup Server, kommer du att [konfigurera Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#recovery-services-vault). När du konfigurerat varje gång en säkerhetskopiering körs skapas en återställningspunkt i valvet. Varje Recovery Services-valvet innehåller upp till 9999 återställningspunkter. Du kan behålla säkerhetskopierade data i många år beroende på antalet återställningspunkter som skapats och hur länge de bevaras. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.
 
### <a name="using-sql-server"></a>Använda SQLServer
Välj endast en Azure-stacken virtuell dator som kör SQL Server om du vill använda en fjärransluten SQL Server för Azure Backup Server-databasen.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestanda
Om delas med andra virtuella datorer, kan lagringsstorleken för kontot och IOPS-gränser påverka prestanda för virtuella datorer i Azure Backup Server. Därför bör du använda ett separat lagringskonto för den virtuella datorn i Azure Backup Server. Azure Backup-agenten körs på Azure Backup-servern behöver tillfällig lagring för:
    - egen användning (en cacheplats)
    - data återställs från molnet (lokalt mellanlagringsområde)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup tillfälliga disklagring
Varje virtuell dator i Azure-stacken medföljer tillfälliga diskutrymme, som är tillgängliga för användaren som volym D:`\`. Det lokala mellanlagringsområdet som krävs av Azure Backup kan konfigureras så att det finns i D:`\`, och cacheplatsen kan placeras på C:`\`. På så sätt behöver ingen lagring hämtas från datadiskar som är kopplade till den virtuella datorn i Azure Backup Server.

### <a name="scaling-deployment"></a>Skalning distribution
Om du vill skala distributionen har följande alternativ:
  - Skala upp – öka storleken på Azure Backup Server virtuell dator från en serie D-serien och öka den lokala lagringen [per Azure Stack virtuella instruktioner](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Omfördela data: skicka äldre data till Azure Backup Server och spara endast senaste data på det lagringsutrymme som är kopplade till Azure Backup-Server.
  - Skala ut – Lägg till fler Azure Backup-servrar för att skydda arbetsbelastningarna.


## <a name="bare-metal-recovery-for-azure-stack-vm"></a>Bare Metal Recovery för Azure-Stack VM

En återställning utan operativsystem (BMR)-säkerhetskopiering skyddar operativsystemfiler och alla kritisk volymdata utom användardata. En säkerhetskopiering av BMR omfattar en säkerhetskopia av systemtillståndet. Följande procedurer beskriver hur du återställer data för BMR. 

### <a name="run-recovery-on-the-azure-backup-server"></a>Köra återställning på Azure Backup-servern 

Öppna konsolen Azure Backup Server.

1. I konsolträdet klickar du på **Recovery**, söka efter den dator du vill återställa och klicka på **Bare Metal Recovery**.
2. Tillgängliga återställningspunkter visas i fetstil i kalendern. Välj datum och tid för återställningspunkten du vill använda.
3. I **Välj typ av återställning**väljer **kopiera till en nätverksmapp**.
4. I **ange mål**, Välj om du vill kopiera data. Kom ihåg den angivna platsen måste ha tillräckligt med utrymme för återställningspunkten. Du rekommenderas du skapar en ny mapp.
5. I **Ange återställningsalternativ**, Välj säkerhetsinställningar ska gälla för och välja att använda SAN-baserade ögonblicksbilder av maskinvara för snabbare återställning.     SAN-baserade ögonblicksbilder av maskinvara är ett alternativ om du har ett SAN-nätverk med den här funktionen aktiveras och möjligheten att skapa och dela en klon så att den blir skrivbar. Även för SAN-baserade ögonblicksbilder av maskinvara ska fungera, måste den skyddade datorn och Azure Backup Server vara ansluten till samma nätverk.
6. Ställ in meddelandealternativ och klickar på **återställa** på den **sammanfattning** sidan.

### <a name="set-up-the-share-location"></a>Ange platsen för resursen
I konsolen Azure Backup Server:
1. Navigera till mappen som innehåller säkerhetskopian i restore-plats.
2. Dela mappen ovanför WindowsImageBackup så att roten för den delade mappen WindowsImageBackup är. Om mappen WindowsImageBackup inte är delade hittar återställningen inte säkerhetskopieringen. Om du vill ansluta med WinRE, behöver du en WinRE-tillgänglig resurs och rätt IP-adress och autentiseringsuppgifter.

### <a name="restore-the-machine"></a>Återställa datorn

1. Öppna en upphöjd kommandotolk och Skriv följande kommandon på den virtuella datorn där du vill återställa BMR. **/bootore** anger att Windows RE startas automatiskt nästa gång datorn startas.
```
Reagent /boottore
shutdown /r /t 0
```

2. Välj språk och nationella inställningar i dialogrutan Öppna. På den **installera** väljer **reparera datorn**.
3. På den **Systemåterställningsalternativ** väljer **återställa datorn med en systemavbildning som du skapade tidigare**.
4. På den **Välj en säkerhetskopierad systemavbildning** väljer **Välj en systemavbildning** > **Avancerat** > **Sök efter en systemavbildning i nätverket**. Om en varning visas väljer du **Ja**. Gå till nätverksresursen, ange autentiseringsuppgifterna och Välj återställningspunkt om du vill välja en bild. Detta söker efter specifika säkerhetskopior som är tillgängliga i denna återställningspunkt. Välj återställningspunkten.
5. I **Välj hur du vill återställa säkerhetskopian**väljer **formatera och partitionera om diskar**. På nästa skärm kontrollerar inställningar och klickar på **Slutför** att starta återställningsjobbet. En omstart som krävs.

## <a name="see-also"></a>Se också
Information om hur du använder Azure Backup Server för att skydda finns andra arbetsbelastningar i följande artiklar:
- [Säkerhetskopiera SharePoint-grupp](backup-azure-backup-sharepoint-mabs.md)
- [Säkerhetskopiera SQLServer](backup-azure-sql-mabs.md)
