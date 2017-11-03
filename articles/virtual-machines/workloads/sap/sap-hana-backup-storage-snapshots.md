---
title: "SAP HANA Azure backup baserat på lagring ögonblicksbilder | Microsoft Docs"
description: "Det finns två huvudsakliga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines, den här artikeln beskriver SAP HANA säkerhetskopia av ögonblicksbilder för lagring"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: f332b8ac091b75a23489ac27f15ad1fd10d24ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring

## <a name="introduction"></a>Introduktion

Detta är en del av en serie i tre delar av relaterade artiklar på SAP HANA-säkerhetskopia. [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) innehåller en översikt och information om att komma igång och [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) omfattar av filbaserade säkerhetskopieringen.

När du använder en VM-funktionen för säkerhetskopiering för en eninstanskategori i-ett demo-system, bör en bör du göra en säkerhetskopiering i stället för att hantera HANA säkerhetskopior på OS-nivå. Ett alternativ är att ta ögonblicksbilder av Azure blob att skapa kopior av enskilda virtuella diskar som är kopplade till en virtuell dator och behålla HANA datafiler. Men en kritisk är app konsekvenskontroll när du skapar en VM-säkerhetskopiering eller disk ögonblicksbild medan systemet är igång och körs. Se _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_ i den relaterade artikeln [säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA har en funktion som har stöd för dessa typer av lagring ögonblicksbilder.

## <a name="sap-hana-snapshots"></a>SAP HANA ögonblicksbilder

Det finns en funktion i SAP HANA som har stöd för en ögonblicksbild för lagring. Från och med December 2016 finns det dock en begränsning till en behållare system. Flera behållare konfigurationer stöder inte den här typen av ögonblicksbild av databasen (se [skapa en ögonblicksbild av lagring (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Den fungerar på följande sätt:

- Förbereda för en ögonblicksbild av lagring genom att initiera SAP HANA-ögonblicksbild
- Kör ögonblicksbilden av lagring (Azure blob ögonblicksbild, till exempel)
- Bekräfta SAP HANA-ögonblicksbild

![Den här skärmbilden visar att du kan skapa en ögonblicksbild för SAP HANA-data via en SQL-instruktion](media/sap-hana-backup-storage-snapshots/image011.png)

Den här skärmbilden visar att du kan skapa en ögonblicksbild för SAP HANA-data via en SQL-instruktion.

![Ögonblicksbilden sedan visas också i katalogen för säkerhetskopieringen i SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Ögonblicksbilden sedan visas också i katalogen för säkerhetskopiering i SAP HANA Studio.

![På disk visas ögonblicksbilden i katalogen för SAP HANA-data](media/sap-hana-backup-storage-snapshots/image013.png)

Ögonblicksbilden visas i katalogen för SAP HANA-data på disk.

Ett måste se till att filsystemkonsekvens också garanteras innan du kör ögonblicksbilden lagring medan SAP HANA förberedelseläge ögonblicksbild. Se _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_ i den relaterade artikeln [säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md).

När ögonblicksbilden lagring är klar, är det viktigt att bekräfta SAP HANA-ögonblicksbild. Det finns en motsvarande SQL-instruktion för att köra: Stäng säkerhetskopiering ÖGONBLICKSBILD (se [BACKUP DATA Stäng SNAPSHOT-instruktionen (säkerhetskopiering och återställning)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Bekräfta HANA ögonblicksbilden. På grund av att &quot;kopiering vid skrivning,&quot; SAP HANA kan kräva ytterligare diskutrymme i snapshot-Förbered läge och det går inte att starta nya säkerhetskopior tills SAP HANA ögonblicksbilden har bekräftats.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA VM säkerhetskopiering via Azure Backup-tjänsten

Agenten för säkerhetskopiering av Azure Backup-tjänsten är inte tillgänglig för virtuella Linux-datorer från och med December 2016. För att använda Azure-säkerhetskopiering på filen/katalogen-nivå, en skulle kopiera SAP HANA säkerhetskopieringsfiler till en virtuell Windows-dator och sedan använda backup-agenten. Annars går endast en fullständig säkerhetskopia Linux VM via tjänsten Azure Backup. Se [översikt över funktioner i Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) vill veta mer.

Azure Backup-tjänsten ger möjlighet att säkerhetskopiera och återställa en virtuell dator. Mer information om den här tjänsten och hur det fungerar finns i artikeln [planera din infrastruktur för VM-säkerhetskopiering i Azure](../../../backup/backup-azure-vms-introduction.md).

Det finns två viktiga saker enligt artikeln:

_&quot;För Linux virtuella datorer är endast filkonsekventa säkerhetskopior möjligt eftersom Linux saknar en motsvarande plattform i VSS.&quot;_

_&quot;Program behövs för att genomföra sina egna &quot;korrigering av&quot; mekanism på återställda data.&quot;_

En måste därför kontrollera SAP HANA är i ett konsekvent tillstånd på disken när säkerhetskopieringen startar. Se _SAP HANA ögonblicksbilder_ beskrivs tidigare i dokumentet. Men det finns ett potentiella problem när SAP HANA förblir i det här läget för förberedelse av ögonblicksbild. Se [skapa en ögonblicksbild av lagring (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) för mer information.

Artikeln tillstånd:

_&quot;Det rekommenderas starkt att bekräfta eller Avbryt en ögonblicksbild av lagring så snart som möjligt när den har skapats. När ögonblicksbilden lagring beredda eller skapa ögonblicksbild av relevanta data är frusen. När ögonblicksbilden relevanta data förblir fryst kan fortfarande ändras i databasen. Ändringarna kommer inte att orsaka frusen ögonblicksbild av relevanta data som ska ändras. I stället skrivs ändringarna till platser i dataområdet som skiljer sig från ögonblicksbilden lagring. Ändringar skrivs också till i loggen. Men ju längre ögonblicksbild av relevanta data sparas fryst, desto mer datavolym kan växa.&quot;_

Azure Backup hand tar om filsystemkonsekvens via Azure VM-tillägg. Dessa tillägg är inte tillgängliga fristående och fungerar bara i kombination med Azure Backup-tjänsten. Det är dock fortfarande ett krav för att hantera en SAP HANA ögonblicksbild för att garantera konsekvens för appen.

Azure-säkerhetskopiering har två huvudsakliga faser:

- Ta en ögonblicksbild
- Överför data till valvet

Ett kan bekräfta SAP HANA-ögonblicksbilder när Azure Backup service fas i en ögonblicksbild har slutförts. Det kan ta flera minuter att finns i Azure-portalen.

![Den här bilden visar en del av listan säkerhetskopiering av en Azure Backup-tjänst](media/sap-hana-backup-storage-snapshots/image014.png)

Den här bilden visar en del av jobblistan över ett Azure Backup-tjänsten, som användes för att säkerhetskopiera HANA testa VM.

![Om du vill visa Jobbinformationen klickar du på säkerhetskopieringsjobbet i Azure-portalen](media/sap-hana-backup-storage-snapshots/image015.png)

Om du vill visa Jobbinformationen klickar du på säkerhetskopieringsjobbet i Azure-portalen. Här kan se något av två faser. Det kan ta några minuter tills det visar fasen ögonblicksbild som slutförd. I de flesta fall används i fasen för överföring av data.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>HANA VM automatisk säkerhetskopiering via Azure Backup-tjänsten

Något gick manuellt bekräfta SAP HANA-ögonblicksbild i Azure Backup ögonblicksbild har avslutats, enligt beskrivningen ovan, men är det bra att överväga automatisering eftersom en administratör inte kan övervaka säkerhetskopieringsjobbet listan i Azure-portalen.

Här följer en förklaring hur det kan utföras via Azure PowerShell-cmdlets.

![Azure Backup-tjänsten har skapats med namnet hana-backup-valvet](media/sap-hana-backup-storage-snapshots/image016.png)

Azure Backup-tjänsten har skapats med namnet &quot;hana säkerhetskopieringsvalvet.&quot; Kommandot PS **Get-AzureRmRecoveryServicesVault-Name hana säkerhetskopieringsvalvet** hämtar motsvarande objekt. Det här objektet används sedan för att ange säkerhetskopiering kontext som det visas på nästa figur.

![En kan kontrollera om jobbet som pågår](media/sap-hana-backup-storage-snapshots/image017.png)

När du har angett rätt sammanhang en kontrollera om jobbet som pågår och leta efter jobbinformation om. Underaktivitet listan visar om den ögonblicksbild Azure jobbet har redan avslutats:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Avsöka värdet i en slinga tills den ändras till slutförd](media/sap-hana-backup-storage-snapshots/image018.png)

När Jobbinformationen lagras i en variabel, är det bara PS syntax för att komma åt den första matris transaktionen och hämta statusvärdet. För att slutföra automatiseringsskriptet avsöka värdet i en slinga tills den övergår i &quot;slutförd.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA licensnyckel och VM återställa via Azure Backup-tjänsten

Azure Backup-tjänsten är utformade för att skapa en ny virtuell dator under återställning. Det finns inga planer just nu för att göra en &quot;lokalt&quot; återställning av en befintlig virtuell Azure-dator.

![Den här bilden visar alternativ för återställning av Azure-tjänsten i Azure-portalen](media/sap-hana-backup-storage-snapshots/image019.png)

Den här bilden visar alternativ för återställning av Azure-tjänsten i Azure-portalen. Kan du välja mellan att skapa en virtuell dator vid återställning eller återställning av diskar. När du återställer diskarna är det ändå nödvändigt att skapa en ny virtuell dator på den. När en ny virtuell dator skapas på Azure den unika VM-ID: T ändras (se [få åtkomst till och med hjälp av Azure VM unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Den här bilden visar Azure VM unikt ID före och efter återställningen via Azure Backup-tjänsten](media/sap-hana-backup-storage-snapshots/image020.png)

Den här bilden visar Azure VM unikt ID före och efter återställningen via Azure Backup-tjänsten. Maskinvarunyckel SAP som används för SAP-licensiering, använder den här unikt VM-ID. En ny SAP-licens har följaktligen installeras när en virtuell dator har återställt.

En ny funktion i Azure Backup angavs i förhandsgranskningsläge under genereringen av handboken säkerhetskopiering. Det gör att en fil nivån återställning baserat på VM-ögonblicksbild skapades för den virtuella datorn säkerhetskopiering. Detta förhindrar att behöva distribuera en ny virtuell dator, och därför unikt ID för VM förblir densamma och inga nya SAP HANA-licensnyckeln krävs. Mer dokumentation om den här funktionen kommer att finnas när testats fullt ut.

Azure Backup kommer förr eller senare kan du säkerhetskopiera enskilda Azure virtuella diskar, plus filer och kataloger från inuti den virtuella datorn. En stor fördel med Azure Backup är hanteringen av alla säkerhetskopior, spara kunden inte behöver göra det. Om en återställning blir nödvändig väljer Azure Backup rätt säkerhetskopian ska använda.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA säkerhetskopiering via diskar manuellt ögonblicksbild

Istället för att använda tjänsten Azure Backup kunde en konfigurera en enskild lösning för säkerhetskopiering genom att skapa blob ögonblicksbilder av virtuella hårddiskar Azure manuellt via PowerShell. Se [Using blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) en beskrivning av stegen.

Det ger bättre flexibilitet men lösa inte de problem som beskrivs ovan i det här dokumentet:

- En måste fortfarande se till att SAP HANA är i ett konsekvent tillstånd
- OS-disken kan inte skrivas över även om den virtuella datorn har frigjorts på grund av ett felmeddelande om att det finns ett lån. Den fungerar bara när du tar bort den virtuella datorn, vilket leder till ett nytt unikt ID för virtuell dator och du behöver installera en ny SAP-licens.

![Det är möjligt att återställa datadiskar för en Azure VM](media/sap-hana-backup-storage-snapshots/image021.png)

Det är möjligt att återställa datadiskar för en Azure VM, undvika problem för att få ett nytt unikt ID för virtuell dator och därför ogiltiga SAP-licens:

- För testet, två Azure datadiskar har anslutits till en virtuell dator och programvarubaserad RAID har definierats på dem 
- Det har bekräftats att SAP HANA befann sig i ett konsekvent tillstånd av funktionen för SAP HANA ögonblicksbild
- Filsystemet Lås (se _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_ i den relaterade artikeln [säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md))
- BLOB-ögonblicksbilder togs från båda datadiskar
- Lås upp filsystemet
- Bekräftelse för SAP HANA ögonblicksbild
- Om du vill återställa datadiskar, den virtuella datorn har stängts och oberoende av båda diskarna
- När du kopplar bort diskarna har de över med tidigare blob-ögonblicksbilder
- Sedan bifogades de återställda virtuella diskarna igen till den virtuella datorn
- När du har startat den virtuella datorn, allt på programvaran RAID fungerade bra och angavs till dess att blob ögonblicksbild
- HANA angavs tillbaka till HANA ögonblicksbild

Om det var möjligt att stänga av SAP HANA innan blob-ögonblicksbilder är proceduren mindre komplex. I så fall kan en hoppa över HANA ögonblicksbilden och, om inget annat som händer i systemet, också hoppa över filen system låsa. Ökad komplexitet börjar bilden när det är nödvändigt att utföra ögonblicksbilder medan allting är online. Se _SAP HANA datakonsekvens när du tar lagring ögonblicksbilder_ i den relaterade artikeln [säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA säkerhetskopiering baserat på filnivå](sap-hana-backup-file-level.md) omfattar av filbaserade säkerhetskopieringen.
* Information om hur du upprättar och planera för katastrofåterställning för SAP HANA i Azure (stora instanser) med hög tillgänglighet finns [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
