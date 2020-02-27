---
title: SAP HANA Azure Backup baserat på lagrings ögonblicks bilder | Microsoft Docs
description: Det finns två huvudsakliga säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer. i den här artikeln beskrivs SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: c977bc7db5608e5718e98a26ed594e5ebf2be998
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617420"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder

## <a name="introduction"></a>Introduktion

Detta ingår i en serie med tre delar av relaterade artiklar på SAP HANA säkerhets kopiering. [Säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om hur du kommer igång och [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) omfattar det filbaserade säkerhets kopierings alternativet.

När du använder en funktion för säkerhets kopiering av virtuella datorer för ett demo system med en enda instans, bör du överväga att göra en VM-säkerhetskopiering i stället för att hantera HANA-säkerhetskopiering på operativ system nivå. Ett alternativ är att ta Azure Blob-ögonblicksbilder för att skapa kopior av enskilda virtuella diskar, som är kopplade till en virtuell dator och behålla HANA-datafilerna. Men en kritisk punkt är applikations konsekvens när du skapar en säkerhets kopia av en virtuell dator eller en disk ögonblicks bild medan systemet är igång. Se _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_ i den relaterade artikeln [säkerhets kopierings guide för SAP HANA på Azure-Virtual Machines](sap-hana-backup-guide.md). SAP HANA har en funktion som stöder dessa typer av lagrings ögonblicks bilder.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA ögonblicks bilder som central del av program konsekventa säkerhets kopieringar

Det finns en funktion i SAP HANA som har stöd för lagring av ögonblicks bilder. Det finns en begränsning för system med en container. Scenarier enkel SAP HANA MCS med fler än en klient har inte stöd för den här typen av SAP HANA databas ögonblicks bild (se [skapa en ögonblicks bild av lagring (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Det fungerar på följande sätt:

- Förbered för en lagrings ögonblicks bild genom att initiera SAP HANA ögonblicks bilden
- Kör lagrings ögonblicks bilden (till exempel Azure Blob snapshot)
- Bekräfta SAP HANA ögonblicks bilden

![Den här skärm bilden visar att en SAP HANA data ögonblicks bild kan skapas via ett SQL-uttryck](media/sap-hana-backup-storage-snapshots/image011.png)

Den här skärm bilden visar att en SAP HANA data ögonblicks bild kan skapas via ett SQL-uttryck.

![Ögonblicks bilden visas sedan även i säkerhets kopie katalogen i SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

Ögonblicks bilden visas sedan även i säkerhets kopierings katalogen i SAP HANA Studio.

![På disk visas ögonblicks bilden i SAP HANA data katalog](media/sap-hana-backup-storage-snapshots/image013.png)

På disk visas ögonblicks bilden i SAP HANA data katalog.

En måste säkerställa att fil systemets konsekvens också garanteras innan du kör ögonblicks bilden av lagringen medan SAP HANA är i förberedelse läget för ögonblicks bild. Se _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_ i den relaterade artikeln [säkerhets kopierings guide för SAP HANA på Azure-Virtual Machines](sap-hana-backup-guide.md).

När ögonblicks bilden av lagringen är färdig är det viktigt att bekräfta SAP HANA ögonblicks bilden. Det finns en motsvarande SQL-instruktion att köra: ÖGONBLICKs bild av säkerhets kopierings DATA (se [säkerhets kopierings data Stäng ögonblicks bilds instruktioner](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Bekräfta HANA-ögonblicksbilden. På grund av &quot;kopiering vid skrivning kan&quot; SAP HANA kräva ytterligare disk utrymme i ögonblicks bilds förberedelse läge och det går inte att starta nya säkerhets kopior förrän SAP HANA ögonblicks bilden har bekräftats.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA VM-säkerhetskopiering via Azure Backup-tjänsten

Säkerhets kopierings agenten för Azure Backups tjänsten är inte tillgänglig för virtuella Linux-datorer. Dessutom har Linux inte liknande funktioner som Windows har med VSS.  Om du vill använda Azure Backup på fil-/katalog nivå kopierar du SAP HANA säkerhetskopierade filer till en virtuell Windows-dator och använder sedan säkerhets kopierings agenten. 

Annars kan endast en fullständig säkerhets kopia av virtuella Linux-datorer göras via tjänsten Azure Backup. Se [Översikt över funktionerna i Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) för att få mer information.

Tjänsten Azure Backup erbjuder ett alternativ för att säkerhetskopiera och återställa en virtuell dator. Mer information om den här tjänsten och hur den fungerar finns i artikeln [Planera infrastrukturen för säkerhets kopiering av virtuella datorer i Azure](../../../backup/backup-azure-vms-introduction.md).

Det finns två viktiga överväganden enligt artikeln:

_&quot;för virtuella Linux-datorer är det möjligt att bara filkonsekventa säkerhets kopieringar, eftersom Linux inte har en motsvarande plattform till VSS.&quot;_

_&quot;-program måste implementera egna &quot;-lösningar för att lösa in de återställda data&quot; mekanismen.&quot;_

Därför måste en vara säker på att SAP HANA är i ett konsekvent tillstånd på disken när säkerhets kopieringen startar. Se _SAP HANA ögonblicks bilder_ som beskrivs tidigare i dokumentet. Men det finns ett möjligt problem när SAP HANA stannar kvar i det här läget för ögonblicks bilds förberedelse. Mer information finns i [skapa en ögonblicks bild av lagring (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) .

Artikel staterna:

_&quot;rekommenderas starkt att bekräfta eller överge en lagrings ögonblicks bild så snart som möjligt efter att den har skapats. Medan ögonblicks bilden förbereds eller skapas, är den ögonblicks bild som är relevant att frysa. Även om ögonblicks bilden av relevanta data förblir frusen kan ändringar fortfarande göras i databasen. Sådana ändringar kommer inte att orsaka att ögonblicks bilder som är relevanta för ögonblicks bilder ändras. I stället skrivs ändringarna till positioner i data områden som är åtskilda från ögonblicks bilden av lagringen. Ändringar skrivs också till loggen. Men längre den ögonblicks bild som relevanta data hålls låsta, desto mer data volym kan växa.&quot;_

Azure Backup sköter fil systemets konsekvens via Azure VM-tillägg. Dessa tillägg är inte tillgängliga fristående och fungerar bara i kombination med Azure Backup-tjänst. Dock är det fortfarande nödvändigt att tillhandahålla skript för att skapa och ta bort en SAP HANA ögonblicks bild för att garantera att appen är konsekvent.

Azure Backup har fyra huvud faser:

- Kör förbereda skript – skript måste skapa en SAP HANA ögonblicks bild
- Ta ögonblicks bild
- Kör skript efter ögonblicks bild – skript måste ta bort SAP HANA som skapats av förberedelse skriptet
- Överför data till valv

Mer information om var du kopierar dessa skript och information om hur Azure Backup fungerar exakt finns i följande artiklar:

- [Planera din infrastruktur för VM-säkerhetskopiering i Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Programkonsekvent säkerhets kopiering av virtuella Azure Linux-datorer](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Vid den här tidpunkten har Microsoft inte publicerat förberedelse skript och skript efter ögonblicks bilder för SAP HANA. Du som kund-eller system integrerare måste skapa dessa skript och konfigurera proceduren baserat på den dokumentation som anges ovan.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Återställa från program konsekvent säkerhets kopiering mot en virtuell dator
Återställnings processen för en program konsekvent säkerhets kopia som tas av Azure Backup dokumenteras i artikeln [återställa filer från säkerhets kopian av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> I artikeln [återställa filer från säkerhets kopian av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) är en lista över undantag och steg som anges när du använder disk stripe-uppsättningar. Striped disks är förmodligen den normala VM-konfigurationen för SAP HANA. Därför är det viktigt att läsa artikeln och testa återställnings processen för de fall som anges i artikeln. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA-licens nyckel och VM-återställning via Azure Backup-tjänsten

Azure Backups tjänsten är utformad för att skapa en ny virtuell dator under återställningen. Det finns ingen plan just nu för att göra en &quot;på plats&quot; återställning av en befintlig virtuell Azure-dator.

![Den här bilden visar alternativet för återställning av Azure-tjänsten i Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

Den här bilden visar alternativet för återställning av Azure-tjänsten i Azure Portal. En kan välja mellan att skapa en virtuell dator när du återställer eller återställer diskarna. När du har återställt diskarna är det fortfarande nödvändigt att skapa en ny virtuell dator ovanpå den. När en ny virtuell dator skapas på Azure ändras unika VM-ID-ändringar (se [åtkomst till och använda unikt ID för virtuell Azure-dator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Den här bilden visar unikt ID för Azure VM före och efter återställningen via Azure Backup-tjänsten](media/sap-hana-backup-storage-snapshots/image020.png)

Den här bilden visar unikt ID för Azure VM före och efter återställningen via Azure Backup-tjänsten. SAP-maskinvaran, som används för SAP-licensiering, använder detta unika VM-ID. Till följd av detta måste en ny SAP-licens installeras efter en VM-återställning.

En ny Azure Backup funktion visas i förhands gransknings läge under skapandet av den här säkerhets kopierings guiden. Den tillåter en återställning på filnivå baserat på ögonblicks bilden av den virtuella datorn som togs för säkerhets kopieringen av den virtuella datorn. På så sätt undviker du att distribuera en ny virtuell dator och därför måste det unika VM-ID: t vara detsamma och ingen ny SAP HANA licens nyckel krävs. Mer dokumentation om den här funktionen kommer att tillhandahållas när den har testats fullständigt.

Azure Backup kommer till och med att tillåta säkerhets kopiering av enskilda virtuella Azure-diskar samt filer och kataloger inifrån den virtuella datorn. En stor fördel med Azure Backup är dess hantering av alla säkerhets kopior, vilket sparar kunden från att behöva göra det. Om en återställning blir nödvändig kommer Azure Backup att välja rätt säkerhets kopia som ska användas.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA VM-säkerhetskopiering via manuell disk ögonblicks bild

I stället för att använda Azure Backup tjänsten kan du konfigurera en enskild säkerhets kopierings lösning genom att skapa BLOB-ögonblicksbilder från virtuella Azure-hårddiskar manuellt via PowerShell. En beskrivning av stegen finns i [använda BLOB-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) .

Den ger större flexibilitet men löser inte problemen som beskrivits tidigare i det här dokumentet:

- Du måste fortfarande se till att SAP HANA är i ett konsekvent tillstånd genom att skapa en SAP HANA ögonblicks bild
- Det går inte att skriva över OS-disken även om den virtuella datorn har frigjorts på grund av ett fel som anger att det finns ett lån. Den fungerar bara när den virtuella datorn har tagits bort, vilket leder till ett nytt unikt VM-ID och behovet av att installera en ny SAP-licens.

![Det går bara att återställa data diskarna för en virtuell Azure-dator](media/sap-hana-backup-storage-snapshots/image021.png)

Det går bara att återställa data diskarna på en virtuell Azure-dator, vilket undviker problemet med att få ett nytt unikt VM-ID och därmed ogiltig SAP-licens:

- För testningen var två Azure-datadiskarna kopplade till en virtuell dator och en RAID-programvara definierades ovanpå dem 
- Det bekräftades att SAP HANA var i ett konsekvent tillstånd av SAP HANA ögonblicks bild funktionen
- Fil Systems frysning (se _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_ i den relaterade artikeln [säkerhets kopierings guide för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md))
- BLOB-ögonblicksbilder togs från båda data diskarna
- Det gick inte att låsa fil systemet
- Bekräfta SAP HANA ögonblicks bild
- För att återställa data diskarna stängdes den virtuella datorn av och båda diskarna kopplades från
- När diskarna har kopplats från skrevs de över med tidigare BLOB-ögonblicksbilder
- Sedan anslöts de återställda virtuella diskarna till den virtuella datorn
- När du har startat den virtuella datorn har allt på RAID-servern fungerat fint och ställts tillbaka till ögonblicks bilds tiden för BLOB
- HANA ställdes tillbaka till HANA-ögonblicksbilden

Om det var möjligt att avsluta SAP HANA före BLOB-ögonblicksbilderna skulle proceduren vara mindre komplex. I så fall kan en hoppa över HANA-ögonblicksbilden och, om inget annat händer i systemet, även hoppa över fil systemets frysning. En ökad komplexitet kommer till bilden när det är nödvändigt att göra ögonblicks bilder medan allt är online. Se _SAP HANA data konsekvens när du tar lagrings ögonblicks bilder_ i den relaterade artikeln [säkerhets kopierings guide för SAP HANA på Azure-Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Nästa steg
* [Säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](sap-hana-backup-guide.md) ger en översikt och information om att komma igång.
* [SAP HANA säkerhets kopiering baserat på filnivå](sap-hana-backup-file-level.md) täcker det filbaserade säkerhets kopierings alternativet.
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
