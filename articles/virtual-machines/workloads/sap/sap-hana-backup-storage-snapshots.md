---
title: SAP HANA Azure backup baserat på ögonblicksbilder av lagring | Microsoft Docs
description: Det finns två viktiga säkerhetskopiering möjligheter för SAP HANA på Azure virtual machines, den här artikeln beskriver SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 1d3089052a67b899e2e4b38123145bd4ae51693f
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902307"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring

## <a name="introduction"></a>Introduktion

Det här är en del av en serie med tre delar av relaterade artiklar om säkerhetskopiering av SAP HANA. [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång och [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) täcker av filbaserade säkerhetskopieringen.

När du använder en VM-säkerhetskopieringsfunktion för en enda instans allt-i-ett-demo-system, bör en bra att göra en säkerhetskopiering av virtuella datorer i stället för att hantera HANA säkerhetskopior på operativsystemsnivån. Ett alternativ är att ta ögonblicksbilder av Azure-blob skapa kopior av enskilda virtuella diskar, som är kopplade till en virtuell dator, och hålla HANA datafiler. Men en kritisk är app konsekvens när du skapar en VM-säkerhetskopiering eller disk ögonblicksbild medan systemet är igång och körs. Se _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_ i den relaterade artikeln [guiden Säkerhetskopiering för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA har en funktion som har stöd för dessa typer av ögonblicksbilder av lagring.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA-ögonblicksbilder som en central del av programkonsekventa säkerhetskopior

Det finns en funktion i SAP HANA som har stöd för en ögonblicksbild för lagring. Det finns en begränsning till enskild behållare system. Scenarier registrerar SAP HANA MCS med fler än en klient inte har stöd för den här typen av ögonblicksbild av SAP HANA-databas (se [skapa Storage-ögonblicksbilder (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Den fungerar på följande sätt:

- Förbereda för en ögonblicksbild för lagring genom att initiera SAP HANA-ögonblicksbilder
- Kör storage-ögonblicksbilder (Azure blob-ögonblicksbild, till exempel)
- Bekräfta SAP HANA-ögonblicksbilder

![Den här skärmbilden visar att du kan skapa en ögonblicksbild för SAP HANA-data via ett SQL-uttryck](media/sap-hana-backup-storage-snapshots/image011.png)

Den här skärmbilden visar att du kan skapa en ögonblicksbild för SAP HANA-data via en SQL-instruktion.

![Ögonblicksbilden sedan visas också i säkerhetskopieringskatalogen i SAP HANA-Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Ögonblicksbilden sedan visas också i säkerhetskopieringskatalogen i SAP HANA-Studio.

![På disk, ögonblicksbilden som visas i katalogen för SAP HANA-data](media/sap-hana-backup-storage-snapshots/image013.png)

På disk, ögonblicksbilden som visas i katalogen för SAP HANA-data.

Ett måste se till att filsystemkonsekvens också är korrekt innan du kör storage-ögonblicksbilder när SAP HANA är i förberedelseläge ögonblicksbild. Se _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_ i den relaterade artikeln [guiden Säkerhetskopiering för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md).

Det är viktigt att bekräfta SAP HANA-ögonblicksbilder när storage-ögonblicksbilder är klart. Det finns en motsvarande SQL-uttryck som ska köras: Stäng BACKUP DATAÖGONBLICKSBILDEN (se [BACKUP DATA Stäng ÖGONBLICKSBILD-instruktionen (säkerhetskopiering och återställning)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Bekräfta HANA-ögonblicksbilder. På grund av &quot;kopiering vid skrivning,&quot; SAP HANA kan kräva ytterligare diskutrymme i ögonblicksbilden – förbereda läge och det går inte att starta nya säkerhetskopior tills SAP HANA-ögonblicksbilder har bekräftats.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA VM-säkerhetskopiering via Azure Backup-tjänsten

Agenten för säkerhetskopiering av Azure Backup-tjänsten är inte tillgänglig för virtuella Linux-datorer. Dessutom har Linux inte liknande funktionalitet som Windows har det med VSS.  Att använda Azure backup på filen eller katalogen-nivå, en skulle kopiera SAP HANA säkerhetskopieringsfiler till en virtuell Windows-dator och sedan använda backup-agenten. 

I annat fall går endast en fullständig Linux VM-säkerhetskopiering via Azure Backup-tjänsten. Se [översikt över funktionerna i Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) och lär dig mer.

Tjänsten Azure Backup erbjuder ett alternativ för att säkerhetskopiera och återställa en virtuell dator. Mer information om den här tjänsten och hur det fungerar finns i artikeln [planera din infrastruktur för VM-säkerhetskopiering i Azure](../../../backup/backup-azure-vms-introduction.md).

Det finns två viktiga överväganden enligt den här artikeln:

_&quot;För Linux-datorer är endast filkonsekvent säkerhetskopiering möjliga, eftersom Linux inte har en motsvarande plattform i VSS.&quot;_

_&quot;Program måste implementera sina egna &quot;åtgärds-&quot; mekanism för återställda data.&quot;_

Ett måste därför kontrollera att SAP HANA är i ett konsekvent tillstånd på disken när säkerhetskopieringen startar. Se _SAP HANA-ögonblicksbilder_ beskrivs tidigare i dokumentet. Men det finns ett potentiellt problem när SAP HANA förblir i det här läget för förberedelse av ögonblicksbild. Se [skapa Storage-ögonblicksbilder (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) för mer information.

Den här artikeln anger:

_&quot;Vi rekommenderar starkt att bekräfta eller lämna en storage-ögonblicksbilder så snart som möjligt när den har skapats. När storage-ögonblicksbilder förberedd eller skapat ögonblicksbild relevanta data är låsta. Medan ögonblicksbilden relevanta data fortfarande är låsta, kan fortfarande ändras i databasen. Sådana ändringar orsakar inte låsta ögonblicksbild-relevanta data ändras. I stället skrivs ändringarna till positioner i dataområdet som skiljer sig från storage-ögonblicksbilder. Ändringar skrivs också till i loggen. Men ju längre ögonblicksbild relevanta data sparas låsta, desto mer datavolymen kan växa.&quot;_

Azure Backup hand tar om filsystemkonsekvens via Azure VM-tillägg. De här tilläggen är inte tillgängliga fristående och fungerar bara i kombination med Azure Backup-tjänsten. Det är dock fortfarande ett krav på att använda skript för att skapa och ta bort en ögonblicksbild av SAP HANA för att garantera konsekvens för appen.

Azure Backup har fyra stora faser:

- Köra förbereda skript - skriptet behöver skapa en ögonblicksbild av SAP HANA
- Ta ögonblicksbild
- Kör skript efter ögonblicksbilden - skriptet behöver ta bort SAP HANA som skapats av skriptet Förbered
- Överför data till valv

Information om var du vill kopiera dessa skript och information om hur Azure Backup fungerar exakt, finns i följande artiklar:

- [Planera din infrastruktur för VM-säkerhetskopiering i Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction)
- [Konsekvent programkonsekvent säkerhetskopiering av virtuella Linux-datorer](https://docs.microsoft.com/en-us/azure/backup/backup-azure-linux-app-consistent)



Vid denna tidpunkt, inte Microsoft har publicerat förbereda skript och efter ögonblicksbilden skript för SAP HANA. Som kund eller system integrator skulle du behöva skapa dessa skript och konfigurera den procedur som bygger på dokumentation som anges ovan.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Återställa från en programkonsekvent säkerhetskopiering mot en virtuell dator
Återställningsprocessen för en programkonsekvent säkerhetskopiering tas av Azure backup finns i artikeln [återställa filer från säkerhetskopiering av Azure virtuella datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> I artikeln [återställa filer från säkerhetskopiering av Azure virtuella datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) är en lista över undantag och steg visas när du använder disk stripe-uppsättningar. Stripe diskar finns troligen vanliga VM-konfigurationen för SAP HANA. Det är därför viktigt att läsa artikeln och testa återställningsprocessen för sådana fall som anges i artikeln. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Licensnyckel för HANA och VM återställa via Azure Backup-tjänsten

Azure Backup-tjänsten är utformad för att skapa en ny virtuell dator under återställningen. Det finns inga planer på just nu för att göra en &quot;plats&quot; återställning av en befintlig Azure VM.

![Den här bilden visar alternativ för återställning av Azure-tjänsten i Azure portal](media/sap-hana-backup-storage-snapshots/image019.png)

Den här bilden visar alternativ för återställning av Azure-tjänsten i Azure-portalen. Kan du välja mellan att skapa en virtuell dator under återställningen eller återställa diskarna. Efter återställning diskarna, är det ändå nödvändigt att skapa en ny virtuell dator över detta. När en ny virtuell dator skapas på Azure det unika ID för virtuell dator ändras (se [åtkomst till och med hjälp av Azure VM unikt ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Den här bilden visar det unika ID för virtuella Azure-datorn före och efter återställningen via Azure Backup-tjänsten](media/sap-hana-backup-storage-snapshots/image020.png)

Den här bilden visar det unika ID för Azure VM före och efter återställningen via Azure Backup-tjänsten. SAP-maskinvarunyckel, som används för SAP-licensiering, använder den här unika VM-ID. En ny SAP-licens har följaktligen installeras när en virtuell dator har återställt.

En ny funktion i Azure Backup angavs under genereringen av den här säkerhetskopieringsguide i förhandsgranskningsläge. Det gör att en servicenivå filåterställning baserat på VM-ögonblicksbilden som utfördes för den virtuella datorn säkerhetskopiering. På så sätt undviker du behöver distribuera en ny virtuell dator, och därför unikt VM-ID förblir oförändrat och inga nya SAP HANA-licensnyckel krävs. Mer dokumentation om den här funktionen tillhandahålls när den har testats.

Azure Backup kommer så småningom kan du säkerhetskopiera enskilda Azure virtuella diskar, samt filer och kataloger från inuti den virtuella datorn. En stor fördel med Azure Backup är hanteringen av alla säkerhetskopieringar, sparar kunden från att göra det de behöver. Om en återställning blir nödvändigt, väljer Azure Backup rätt säkerhetskopian att använda.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA säkerhetskopiering via manuella ögonblicksbild

Istället för att använda Azure Backup-tjänsten, kan en konfigurera en enskild säkerhetskopieringslösning genom att skapa blobögonblicksbilder av virtuella Azure-hårddiskar manuellt via PowerShell. Se [med hjälp av blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) en beskrivning av stegen.

Det ger bättre flexibilitet men lösa inte de problem som beskrivs ovan i det här dokumentet:

- Fortfarande måste du se till att SAP HANA är i ett konsekvent tillstånd genom att skapa en ögonblicksbild av SAP HANA
- OS-disken kan inte skrivas över även om Virtuellt datorn frigörs på grund av ett felmeddelande om att det finns ett lån. Det fungerar bara när du tar bort den virtuella datorn, vilket skulle leda till ett nytt unikt ID för virtuell dator och behovet av att installera en ny SAP-licens.

![Det går att återställa endast datadiskar på en Azure-dator](media/sap-hana-backup-storage-snapshots/image021.png)

Det är möjligt att återställa endast datadiskar på en Azure-dator, undvika problemet för att få ett nytt unikt ID för virtuell dator och därför ogiltig SAP-licens:

- För testning, två Azure-datadiskar var kopplat till en virtuell dator och programvaru-RAID har definierats på dem 
- Det har bekräftats att SAP HANA var i ett konsekvent tillstånd genom att funktionen för SAP HANA-ögonblicksbild
- Låsa i filsystemet (se _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_ i den relaterade artikeln [guiden Säkerhetskopiering för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md))
- BLOB-ögonblicksbilder är tagna från båda datadiskar
- Lås upp filsystem
- Bekräftelse för SAP HANA-ögonblicksbild
- Om du vill återställa datadiskar, den virtuella datorn stängdes av och är oberoende av båda diskarna
- När du kopplar från diskarna skrevs de över med de tidigare versionen blobögonblicksbilderna
- Sedan bifogades de återställda virtuella diskarna igen till den virtuella datorn
- När du har startat den virtuella datorn, allt för programvaran RAID fungerade bra och har värdet tillbaka tiden för blob-ögonblicksbild
- HANA angavs tillbaka till HANA-ögonblicksbilder

Om det var möjligt att Stäng SAP HANA innan blob-ögonblicksbilder blir proceduren mindre komplex. I så fall kan kan en hoppa över HANA-ögonblicksbilder och, om inget annat som pågår i systemet, också hoppa över filen system-låsning. Ökad komplexitet kommer in i bilden när det är nödvändigt att utföra ögonblicksbilder medan allt är online. Se _SAP HANA-datakonsekvens när du tar ögonblicksbilder av lagring_ i den relaterade artikeln [guiden Säkerhetskopiering för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA-säkerhetskopia baserat på filnivå](sap-hana-backup-file-level.md) täcker av filbaserade säkerhetskopieringen.
* Läs hur du etablerar hög tillgänglighet och planera för katastrofåterställning av SAP HANA på Azure (stora instanser) i [SAP HANA (stora instanser) hög tillgänglighet och katastrofåterställning recovery på Azure](hana-overview-high-availability-disaster-recovery.md).
