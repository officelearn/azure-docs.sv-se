---
title: Azure Backup stöd matrix för virtuell Azure-säkerhetskopiering
description: Innehåller en sammanfattning av inställningar för stöd och begränsningar när du säkerhetskopierar virtuella Azure-datorer med Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 2bd499c3afc423047dda9ff3ad657d110dab282a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673655"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Stöd matrix för virtuell Azure-säkerhetskopiering
Du kan använda den [Azure Backup-tjänsten](backup-overview.md) för säkerhetskopiering av lokala datorer och arbetsbelastningar och virtuella Azure-datorer. Den här artikeln sammanfattas support inställningar och begränsningar när du säkerhetskopierar virtuella Azure-datorer (VM) med Azure Backup.

Andra matriser för support:

- [Allmänt stöd matrix](backup-support-matrix.md) för Azure Backkup
- [Stödmatris](backup-support-matrix-mabs-dpm.md) för Microsoft Azure Backup server/DOM-säkerhetskopiering
- [Stödmatris](backup-support-matrix-mars-agent.md) för säkerhetskopiering med MARS-agenten



## <a name="supported-scenarios"></a>Scenarier som stöds

Här är hur du kan säkerhetskopiera och återställa virtuella Azure-datorer med Azure Backup-tjänsten.


**Scenario** | **Säkerhetskopiering** | **Agent** |**Återställ** 
--- | --- | --- | ---
**Direkt säkerhetskopiering av virtuella Azure-datorer** | Säkerhetskopiera en hel virtuell dator  | Ingen agent krävs för virtuella Azure-datorn. Azure Backup installerar och använder ett tillägg till den [Virtuella Azure-datoragenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) som körs på den virtuella datorn. | Återställa på följande sätt:<br/><br/> - **Skapa en grundläggande virtuell dator**. Detta är användbart om den virtuella datorn har ingen särskild konfiguration, till exempel med flera IP-adresser.<br/><br/> - **Återställa den Virtuella datordisken**. Återställ disken. Sedan kan du koppla den till en befintlig virtuell dator eller skapa en ny virtuell dator från disken med hjälp av PowerShell.<br/><br/> - **Ersätt virtuell disk**. Om en virtuell dator finns och den använder hanterade diskar (okrypterat), kan du återställa en disk och använda den för att ersätta en befintlig disk på den virtuella datorn.<br/><br/> - **Återställa specifika filer/mappar**. Du kan återställa filer/mappar från en virtuell dator i stället för en hel virtuell dator.  
**Direkt säkerhetskopiering av virtuella Azure-datorer (endast Windows)** | Säkerhetskopiera specifika filer/mappar/volym | Installera den [Microsoft Azure Recovery Services MARS-agenten](backup-azure-file-folder-backup-faq.md).<br/><br/> Du kan köra MARS-agenten tillsammans med säkerhetskopieringstillägget för Azure VM-agenten för säkerhetskopiering av den virtuella datorn på filen/mappen nivå. | Återställa specifika mappar/filer.
**Säkerhetskopiera virtuella Azure-datorer till säkerhetskopieringsserver** |  Säkerhetskopiera filer/mappar/volymer. Systemtillstånd/utan operativsystem systemfilerna. App-data till System Center DPM eller Microsoft Azure Backup Server (MAB-servern).<br/><br/> DPM/MABS sedan säkerhetskopierar till säkerhetskopieringsvalvet | Installera MABS/DPM-skyddsagenten på den virtuella datorn. MARS-agenten är installerad på DPM/MABS.| Återställa filer/mappar/volymer. Systemtillstånd/utan operativsystem systemfilerna. appdata. 

Läs mer om backup [med hjälp av en reservserver](backup-architecture.md#architecture-back-up-to-dpmmabs), och [supportkrav](backup-support-matrix-mabs-dpm.md).


## <a name="supported-backup-actions"></a>Åtgärder som stöds

**Åtgärd** | **Support**
--- | ---
Aktivera säkerhetskopiering när du skapar en Windows Azure VM | Stöd för:  Windows Server 2016 (Datacenter/Datacenter kärnor); Windows Server 2012 R2 DataCenter; Windows Server 2008 R2 SP1
Aktivera säkerhetskopiering när du skapar en Linux VM | Stöd för:<br/><br/> - Ubuntu Server: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> - Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> - Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Säkerhetskopiera en virtuell dator som är avstängning/offline/söker VM | Stöds.<br/><br/> Ögonblicksbilden är kraschkonsekventa, men inte appkonsekventa.
Säkerhetskopiera diskar när du migrerar till hanterade diskar. | Stöds.<br/><br/> Backup fortsätter att fungera. Ingen åtgärd krävs.
Säkerhetskopiera hanterade diskar när du har aktiverat resurslås för grupp | Stöds ej.<br/><br/> Backup kan inte ta bort den äldre resource pekar och säkerhetskopior börjar misslyckas när den maximala gränsen på återställningspunkter har nåtts.
Ändra princip för säkerhetskopiering för en virtuell dator | Stöds.<br/><br/> Den virtuella datorn kommer att säkerhetskopieras med schema och kvarhållning inställningarna i den nya principen. Om inställningarna för datakvarhållning är utökat, markeras befintliga återställningspunkter och förvaras. Om de är minskar kommer befintliga återställningspunkter att rensas under nästa rensningsjobb och slutligen bort.
Avbryt ett säkerhetskopieringsjobb | Stöds under processen för ögonblicksbild.<br/><br/> Stöds inte när ögonblicksbilden överförs till valvet. 
Säkerhetskopiera den virtuella datorn till en annan region eller prenumeration |  Stöds ej.
Säkerhetskopieringar per dag (via Azure VM-tillägg) | En schemalagd säkerhetskopiering per dag.<br/><br/> Du kan ta upp till fyra säkerhetskopieringar på begäran per dag.
Säkerhetskopieringar per dag (via MARS-agenten) | Tre schemalagda säkerhetskopieringar per dag. 
Säkerhetskopieringar per dag (via DPM/MABS) | Två schemalagda säkerhetskopieringar per dag.
Månad/år säkerhetskopiering   | Stöds inte när du säkerhetskopierar med Azure VM-tillägg. Endast stöds dagliga och veckovisa.<br/><br/> Du kan ställa in principen till Kvarhåll säkerhetskopior varje dag/vecka i månad/år kvarhållningsperiod.
Automatisk klockan justering | Stöds ej.<br/><br/> Azure Backup tar inte automatisk justering av klockan för sommartid ändringar i beräkningen när du säkerhetskopierar virtuella datorer.<br/><br/>  Ändra principen manuellt vid behov.
[Säkerhetsfunktioner för hybrid-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Inaktivera funktionerna stöds inte.



## <a name="operating-system-support-windows"></a>Operativsystem (Windows)

Tabellen sammanfattar operativsystem som stöds när du säkerhetskopierar virtuella datorer i Windows Azure.

**Scenario** | **OS-support** 
--- | ---
Säkerhetskopiera med tillägget för Azure VM-agent | Windows-klient: Stöds inte<br/><br/> Windows Server: Windows Server 2008 R2 eller senare 
Säkerhetskopiera med MARS-agenten | [Stöds](backup-support-matrix-mars-agent.md#support-for-direct-backups) operativsystem.
Säkerhetskopiera via DPM/MABS | Operativsystem som stöds för säkerhetskopiering med [MABS](backup-mabs-protection-matrix.md) och [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Stöd för Linux-säkerhetskopiering

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Åtgärd** | **Support**
--- | --- 
Säkerhetskopiera virtuella Linux Azure-datorer med Linux Azure VM-agenten | Konsekvent säkerhetskopiering av filer.<br/><br/> Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Under återställning kan du skapa en ny virtuell dator, återställa en disk och använda den för att skapa en virtuell dator, eller återställa en disk och använda den för att ersätta en disk på en befintlig virtuell dator. Du kan även återställa enskilda filer och mappar.
Säkerhetskopiera virtuella Linux Azure-datorer med MARS-agenten | Stöds ej.<br/><br/> MARS-agenten kan endast installeras på Windows-datorer.
Säkerhetskopiera virtuella Linux Azure-datorer med DPM/MABS | Stöds ej.

## <a name="operating-system-support-linux"></a>Stöd för värdoperativsystem (Linux)

För Azure VM Linux säkerhetskopior, Azure Backup stöder lista över Linux [distributioner som godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Tänk på följande:

- Azure Backup stöder inte Core OS Linux.
- Azure Backup stöder inte 32-bitars operativsystem.
- Andra bring-your-own Linux-distributioner kan fungera så länge som den [Azure VM-agenten för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) är tillgänglig på den virtuella datorn och Python stöds.



## <a name="backup-frequency-and-retention"></a>Frekvens för säkerhetskopiering och kvarhållning

**Inställning** | **Begränsningar** 
--- | --- 
Högsta antal återställningspunkter per skyddad instans (dator/arbetsbelastning | 9999
Den längsta förfallotiden för en återställningspunkt | Obegränsad
Högsta säkerhetskopieringsfrekvensen till valv (Azure VM-tillägg) | En gång om dagen
Högsta säkerhetskopieringsfrekvensen till valv (MARS-agenten) | Tre säkerhetskopieringar per dag.
Högsta säkerhetskopieringsfrekvens till DPM/MABS | Varje kvart för SQL Server<br/><br/> En gång per timme för andra arbetsbelastningar.
Kvarhållning av återställningspunkt | Varje dag, varje vecka, varje månad, varje år.
Högsta kvarhållningsperiod | Beror på säkerhetskopieringsfrekvensen.
Återställningspunkter på DPM-/MABS-disk | 64 för filservrar, 448 för appservrar.<br/><br/> Bandåterställningspunkter är obegränsade för lokal DPM.


## <a name="supported-restore-methods"></a>Stöds restore-metoder

**Restore-metod** | **Detaljer**
--- | ---
**Skapa en ny virtuell dator** | Du kan skapa en virtuell dator under återställningsprocessen. <br/><br/> Det här alternativet får en grundläggande virtuell dator igång. Du kan ange den virtuella datorn namnet, resursgrupp, virtuellt nätverk, undernät, och storage.  
**Återställa en disk** | Du kan återställa en disk och använda den för att skapa en virtuell dator<br/><br/> När du väljer det här alternativet kan kopierar Azure Backup data från valvet till ett lagringskonto som du väljer. Återställningsjobbet genererar en mall. Du kan hämta den här mallen och använda den för att ange anpassade inställningar för virtuell dator och skapa en virtuell dator.<br/><br/> Det här alternativet kan du ange fler inställningar som du tidigare möjlighet att skapa en virtuell dator.<br/><br/>
**Ersätta en befintlig disk** | Du kan återställa en disk och sedan använda den återställda disken för att ersätta en disk som redan finns på en virtuell dator.
**Återställa filer** | Du kan återställa filer från en vald återställningspunkt. Du kan hämta ett skript för att montera den Virtuella disken från återställningspunkten. Du sedan bläddra igenom diskvolymer att hitta filer/mappar som du vill återställa och demontera disken när du är klar.

## <a name="support-for-file-level-restore"></a>Stöd för återställning på filnivå

**Återställ** | **Stöds**
--- | --- 
Återställa filer över operativsystem | Du kan återställa filer på en dator som har samma (eller kompatibla) OS som den säkerhetskopierade virtuella datorn. se den [kompatibla OS tabell](backup-azure-restore-files-from-vm.md#system-requirements).
Återställa filer på klassiska virtuella datorer | Stöds ej. 
Återställa filer från krypterade virtuella datorer | Stöds ej.
Återställa filer från nätverksbegränsade lagringskonton | Stöds ej.
Återställa filer på virtuella datorer med hjälp av lagringsutrymmen för Windows | Återställning stöds inte på samma virtuella dator.<br/><br/> I stället ska du återställa filer på en kompatibel virtuell dator.
Återställa filer på Linux VM med hjälp av LVM/raid-matriser | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställa på en kompatibel virtuell dator.
Återställa filer med särskilda nätverksinställningar | Återställning stöds inte på samma virtuella dator. <br/><br/> Återställa på en kompatibel virtuell dator.

## <a name="support-for-vm-management"></a>Stöd för hantering av virtuell dator

Tabellen sammanfattar stöd för säkerhetskopiering under VM-hanteringsuppgifter, till exempel lägga till eller ersätta VM-diskar.

**Återställ** | **Stöds** 
--- | --- 
Återställa i prenumeration eller region/zon. | Stöds inte 
Återställa till en befintlig virtuell dator | Använd alternativet för Ersätt disk.
Återställa en disk med storage-konto som är aktiverad för Azure Storage Service Encryption (SSE) | Stöds ej.<br/><br/> Återställa till ett konto som inte har aktiverat SSE.
Återställa till blandade storage-konton | Stöds ej.<br/><br/> Baserat på typ av lagringskonto, blir alla återställda diskar premium eller standard och inte blandat. 
Återställ till lagringskontot som använder zonredundant lagring | Stöds inte
Återställa en virtuell dator direkt till en tillgänglighetsuppsättning | För hanterade diskar, kan du återställa disken och använda set-alternativ för tillgänglighet i mallen.<br/><br/> Stöds inte för ohanterade diskar. Återställ disken för ohanterade diskar och skapa en virtuell dator i tillgänglighetsuppsättningen.
Återställ en säkerhetskopia av ohanterade virtuella datorer när du har uppgraderat till hanterad | Stöds.<br/><br/> Du kan återställa diskar och sedan skapa en hanterad virtuell dator.
Återställa en virtuell dator för att återställa platsen innan den virtuella datorn har migrerats till managed disks | Stöds.<br/><br/> Du återställer till ohanterade diskar (standard), konvertera de återställda diskarna till hanterade diskar och skapa en virtuell dator med hanterade diskar.
Återställa en virtuell dator som har tagits bort. | Stöds.<br/><br/> Du kan återställa den virtuella datorn från en återställningspunkt.
Återställa en domänkontrollant (DC) virtuell dator som är en del av en multi-DC-konfiguration via portalen | Stöds om du återställer disken och skapa en virtuell dator med hjälp av PowerShell.
Återställa en virtuell dator i olika virtuella nätverk |  Stöds.<br/><br/> Det virtuella nätverket måste finnas i samma prenumeration och region.




## <a name="vm-compute-support"></a>Support för beräkning av virtuella datorer

**Compute** | **Support** 
--- | --- 
Storlek på virtuell dator |   Alla Azure VM-storlekar med minst 2 CPU-kärnor och 1 GB RAM-minne.<br/><br/> [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
Säkerhetskopiera virtuella datorer i [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Stöds.<br/><br/> Du kan inte återställa en virtuell dator i en tillgänglig uppsättning med alternativet att snabbt skapa en virtuell dator. I stället när du återställer den virtuella datorn måste antingen återställa disken och använda den för att distribuera en virtuell dator eller återställa en disk och använda den för att ersätta en befintlig disk. 
Säkerhetskopiera virtuella datorer i [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Stöds ej.  
Säkerhetskopiera virtuella datorer som distribueras med [Hybrid Använd förmånen (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Stöds.
Säkerhetskopiera virtuella datorer som distribueras i en [skalningsuppsättning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |   Stöds inte   
Säkerhetskopiera virtuella datorer som distribueras från den [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicerats av Microsoft, tredje parts) |   Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> Vid återställning av filer på den virtuella datorn, kan du återställa endast till ett kompatibelt operativsystem (inte en tidigare eller senare-OS).
Säkerhetskopiera virtuella datorer som distribueras från en anpassad avbildning (från tredje part) |    Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> Vid återställning av filer på den virtuella datorn, kan du återställa endast till ett kompatibelt operativsystem (inte en tidigare eller senare-OS).
Säkerhetskopiera virtuella datorer migreras till Azure   | Stöds.<br/><br/> VM-agenten måste installeras på den migrerade datorn för att säkerhetskopiera den virtuella datorn. 



## <a name="vm-storage-support"></a>Support för VM-lagring

**Komponent** | **Support**
--- | ---
Datadiskar för virtuella Azure-datorer | Säkerhetskopiera en virtuell dator med 16 eller mindre datadiskar.
Datadiskstorleken | Enskilda disk kan vara upp till 4 095 GB.<br/><br/> Om du kör den senaste versionen av virtuell Azure-säkerhetskopiering (även kallat omedelbar återställning), disken storlekar upp till 4TB stöds. [Läs mer](backup-instant-restore-capability.md).
Lagringstyp | Standard HDD, SSD som standard, premium SSD <br/><br/> Standard SSD stöds om du kör den senaste versionen av virtuell Azure-säkerhetskopiering (även kallat omedelbar återställning), standard SSD stöds. [Läs mer](backup-instant-restore-capability.md).
Hanterade diskar | Stöds
Krypterade diskar | Stöds.<br/><br/> Virtuella Azure-datorer aktiverad med Azure Disk Encryption (ADE) kan säkerhetskopieras (med eller utan Azure AD-app).<br/><br/> Inte att återställa krypterade virtuella datorer på nivån fil/mapp. Du behöver återställa en hel virtuell dator.<br/><br/> Du kan aktivera kryptering på virtuella datorer som redan skyddas av Azure Backup.
Diskar med Write Accelerator-aktiverade | Stöds ej.<br/><br/> Om du kör den senaste versionen av virtuell Azure-säkerhetskopiering (kallas även [omedelbar återställning](backup-instant-restore-capability.md)), du kan utesluta diskar med Write Accelerator-aktiverade från en säkerhetskopia.
Säkerhetskopiera deduplicerade diskar | Stöds ej.
Lägg till disk till skyddad virtuell dator | Stöds.
Ändra storlek på disk på skyddad virtuell dator | Stöds.

## <a name="vm-network-support"></a>Stöd för VM-nätverk


**Komponent** | **Support**
--- | ---
Antal nätverkskort | Upp till maximalt antal nätverkskort som stöds för en viss Azure VM-storlek.<br/><br/> Nätverkskort skapas när den virtuella datorn har skapats under återställningsprocessen.<br/><br/> Antalet nätverkskort på den återställda virtuella datorn speglar antalet nätverkskort på den virtuella datorn när du har aktiverat skydd. Ta bort nätverkskort när du har aktiverat påverkar inte antalet.
Extern/intern belastningsutjämnare |   Stöds. <br/></br> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Flera reserverade IP-adress |  Stöds. <br/></br> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med flera nätverkskort  | Stöds. <br/></br> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) om att återställa virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med offentliga IP-adresser    | Stöds.<br/><br/> Du måste associera en befintlig offentlig IP-adress till nätverkskortet, eller skapa en adress och koppla den till nätverkskortet när återställningen är klar.
Nätverkssäkerhetsgrupp (NSG) på NIC/undernät. |   Stöds.
Reserverad IP-adress (statisk) | Stöds inte.<br/><br/> Du kan inte säkerhetskopiera en virtuell dator med en reserverad IP-adress och ingen definierad slutpunkt.
Dynamisk IP-adress |    Stöds.<br/><br/> Om nätverkskortet på käll-VM använder dynamisk IP-adressering, som standard gör nätverkskortet på den återställda virtuella datorn också.
Traffic Manager | Stöds<br/><br/> Om den säkerhetskopierade virtuella datorn är i Traffic Manager, måste du manuellt lägger till den återställda virtuella datorn till samma Traffic Manager. 
Azure DNS | Stöds.
Anpassad DNS |    Stöds.
Utgående anslutning via en HTTP-proxy | Stöds.<br/><br/> En autentiserad proxyserver stöds inte. 
VNet-tjänstslutpunkter  | Stöds.<br/><br/> Brandvägg och virtuellt nätverk inställningarna för lagringskontot ska tillåta åtkomst från alla nätverk. 



## <a name="vm-securityencryption-support"></a>Stöd för VM-säkerhet/kryptering

Azure Backup stöder kryptering för data under överföring och i vila:

Nätverkstrafik till Azure:
- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.
    > [!WARNING]
    > När du har konfigurerat valvet har bara du åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.
Datasäkerhet:
- När du säkerhetskopierar virtuella Azure-datorer behöver du konfigurera kryptering *på* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På serverdelen använder Azure Backup [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md), som skyddar vilande data.


**Dator** | **Under överföring** | **I vila**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuella Azure-datorer | ![Ja][green] | ![Ja][green] 
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]



## <a name="vm-compression-support"></a>Support för komprimering av virtuella datorer

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, vilket sammanfattas i tabellen nedan. Tänk på följande:

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagringsnätverket. Därför behöver du inte komprimera den här trafiken.
- Om du använder DPM eller MABS kan du komprimera data innan de säkerhetskopieras till DPM/MABS för att spara bandbredd. 

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera (HTTPS) till valv**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | Ej tillämpligt | Ja
Virtuella Azure-datorer | Ej tillämpligt | Ej tillämpligt
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]




## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md)
- [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
- [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
- [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

