---
title: Azure Backup-Server skyddar Systemtillstånd och återställer till utan operativsystem
description: Använda Azure Backup Server att säkerhetskopiera din Systemtillstånd och bare metal recovery (BMR) skydd.
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: d35f8667cb1ca9a0b3abd08450ebc647d6d12276
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607216"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Säkerhetskopiera systemtillståndet och återställa till utan operativsystem med Azure Backup Server

Azure Backup Server säkerhetskopierar Systemtillstånd och bare metal recovery (BMR) skyddar.

*   **Säkerhetskopiering av systemtillstånd**: säkerhetskopierar operativsystemfiler, så kan du återställa när datorn startar, men systemfiler och registret går förlorade. En säkerhetskopia av systemtillståndet innehåller:
    * Domänmedlem: starta filer, registreringsdatabas för COM +-klassen, registret
    * Domänkontrollant: Windows Server Active Directory (NTDS), starta filer, registreringsdatabas för COM +-klassen, registret, systemvolymen (SYSVOL)
    * Dator som kör klustertjänster: klusterserverns metadata
    * Dator som kör Certifikattjänster: certifikatet data
* **Bare metal-säkerhetskopiering**: säkerhetskopierar operativsystemfiler och alla data på kritiska volymer (utom användardata). Per definition erbjuder omfattar en säkerhetskopiering av BMR en säkerhetskopia av systemtillståndet. Det ger skydd när en dator inte startar och du måste återställa allt.

I följande tabell sammanfattas vad du kan säkerhetskopiera och återställa. Detaljerad information om appversioner som kan skyddas med Systemtillstånd och BMR finns [vad är Azure Backup Server säkerhetskopiera?](backup-mabs-protection-matrix.md).

|Backup|Problem|Återställa från säkerhetskopia för Azure Backup-Server|Återställa från säkerhetskopia av systemtillståndet|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fildata**<br /><br />Regelbunden säkerhetskopiering<br /><br />BMR/systemtillstånd|Förlorade fildata|Y|N|N|
|**Fildata**<br /><br />Azure Backup-Server-säkerhetskopiering av fildata<br /><br />BMR/systemtillstånd|Saknad eller skadad operativsystem|N|Y|Y|
|**Fildata**<br /><br />Azure Backup-Server-säkerhetskopiering av fildata<br /><br />BMR/systemtillstånd|Förlorad server (datavolymer orörda)|N|N|Y|
|**Fildata**<br /><br />Azure Backup-Server-säkerhetskopiering av fildata<br /><br />BMR/systemtillstånd|Förlorad server (datavolymer tappas bort)|Y|Nej|Ja (BMR, följt av reguljära återställning av säkerhetskopierade filen data)|
|**SharePoint-data**:<br /><br />Azure Backup-Server-säkerhetskopiering av servergruppdata<br /><br />BMR/systemtillstånd|Förlorade plats, listor, listobjekt och dokument|Y|N|N|
|**SharePoint-data**:<br /><br />Azure Backup-Server-säkerhetskopiering av servergruppdata<br /><br />BMR/systemtillstånd|Saknad eller skadad operativsystem|N|Y|Y|
|**SharePoint-data**:<br /><br />Azure Backup-Server-säkerhetskopiering av servergruppdata<br /><br />BMR/systemtillstånd|Haveriberedskap|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup-Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/säkerhetskopia av systemtillståndet på värden|Förlorade VM|Y|N|N|
|Hyper-V<br /><br />Azure Backup-Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/säkerhetskopia av systemtillståndet på värden|Saknad eller skadad operativsystem|N|Y|Y|
|Hyper-V<br /><br />Azure Backup-Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/säkerhetskopia av systemtillståndet på värden|Förlorad Hyper-V-värd (virtuella datorer orörda)|N|N|Y|
|Hyper-V<br /><br />Azure Backup-Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/säkerhetskopia av systemtillståndet på värden|Förlorad Hyper-V-värden (VMs tappas bort)|N|N|Y<br /><br />BMR, följt av reguljära återställning av Azure Backup Server|
|SQL Server Exchange<br /><br />Azure Backup-Server app-säkerhetskopiering<br /><br />BMR/systemtillstånd|Förlorade AppData|Y|N|N|
|SQL Server Exchange<br /><br />Azure Backup-Server app-säkerhetskopiering<br /><br />BMR/systemtillstånd|Saknad eller skadad operativsystem|N|Y|Y|
|SQL Server Exchange<br /><br />Azure Backup-Server app-säkerhetskopiering<br /><br />BMR/systemtillstånd|Förlorad server (databas/transaktionsloggar orörda)|N|N|Y|
|SQL Server Exchange<br /><br />Azure Backup-Server app-säkerhetskopiering<br /><br />BMR/systemtillstånd|Förlorad server (databas/transaktionsloggar tappas bort)|N|N|Y<br /><br />BMR-återställning, följt av reguljära återställning av Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Så här fungerar systemtillstånd

När en säkerhetskopiering av systemtillstånd körs, kommunicerar Backup-servern med Windows Server Backup för att begära en säkerhetskopia av serverns systemtillstånd. Som standard Backup Server och Windows Server Backup kan du använda på enheten med mest tillgängligt ledigt utrymme. Information om den här enheten sparas i filen PSDataSourceConfig.xml. Detta är den enhet som Windows Server Backup använder för säkerhetskopiering.

Du kan anpassa den enhet som Backup-servern använder för systemtillståndet. Gå till C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources på den skyddade servern. Öppna filen PSDataSourceConfig.xml för redigering. Ändra den \<FilesToProtect\> värde för enhetsbeteckningen. Spara och stäng filen. Om det finns en skyddsgruppuppsättning för att skydda datorns systemtillstånd måste du köra en konsekvenskontroll. Om en avisering genereras väljer **ändra skyddsgrupp** i aviseringen och slutför sedan guiden. Kör sedan en annan konsekvenskontroll.

Observera att om skydd servern är i ett kluster, är det möjligt att en enhet för kluster markeras som enhet med mest ledigt utrymme. Om ägarskap som enheten har växlats till en annan nod och en system tillstånd säkerhetskopiering körs, enheten är inte tillgängligt och säkerhetskopieringen misslyckas. I det här scenariot, ändra PSDataSourceConfig.xml att peka till en lokal enhet.

Windows Server Backup skapar sedan en mapp som kallas WindowsImageBackup i roten av mappen återställning. Eftersom Windows Server Backup skapar säkerhetskopian placeras alla data i den här mappen. När säkerhetskopieringen är klar överförs filen till datorn reservserver. Notera följande information:

* Den här mappen och dess innehåll rensas inte när säkerhetskopieringen eller överföringen är klar. Det bästa sättet att tänka på detta är att området är som har reserverats för nästa gång en säkerhetskopia är klar.
* Mappen skapas varje gång en säkerhetskopia görs. Tid och datum tidsstämpel samma tid för din senaste säkerhetskopia av systemtillståndet.

## <a name="bmr-backup"></a>BMR säkerhetskopiering

För BMR (inklusive en säkerhetskopia av systemtillståndet), sparas säkerhetskopieringsjobbet direkt till en resurs på Backup Server-datorn. Den sparas inte till en mapp på den skyddade servern.

Backup-servern anropar Windows Server Backup och delar ut replikvolymen för BMR säkerhetskopian. Den tala inte i det här fallet Windows Server Backup för att använda enheten med mest ledigt utrymme. I stället används den resurs som har skapats för jobbet.

När säkerhetskopieringen är klar överförs filen till datorn reservserver. Loggfilerna lagras i C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

-   BMR stöds inte för datorer som kör Windows Server 2003 eller för datorer som kör ett klientoperativsystem.

-   Du kan inte skydda BMR och systemtillstånd för samma dator i olika skyddsgrupper.

-   En Backup Server-dator kan inte skydda sig själv för BMR.

-   Korta skydd till band (disk till band eller D2T) stöds inte för BMR. Långsiktig lagring på band (disk-till-disk till band eller D2D2T) stöds.

-   Windows Server Backup måste installeras på den skyddade datorn för BMR-skydd.

-   För BMR-skydd, till skillnad från för skydd av systemtillstånd Backup Server har inte några krav på diskutrymme på den skyddade datorn. Windows Server Backup Överför säkerhetskopiorna direkt till Backup Server-datorn. Säkerhetskopiering överföringsjobbet visas inte i reservserver **jobb** vyn.

-   Backup-Server reserverar 30 GB utrymme på replikvolymen för BMR. Du kan ändra detta på den **diskallokering** sidan i guiden Ändra Skyddsgrupp eller med hjälp av Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation PowerShell-cmdlets. På återställningspunktvolymen kräver BMR skydd om 6 GB för ett Kvarhållningsintervall på fem dagar.
    * Observera att du kan minska replikvolymens storlek till mindre än 15 GB.
    * Reservserver beräkna inte storleken på BMR-datakällan. Den förutsätter 30 GB för alla servrar. Ändra värdet baserat på storleken på BMR säkerhetskopieringar som du förväntar dig i din miljö. Storleken på en BMR säkerhetskopiering kan beräknas ungefär som summan av använt utrymme på alla kritiska volymer. Kritiska volymer = Start volymen + volym där systemtillstånd, till exempel Active Directory.

-   Om du ändrar från skydd av systemtillstånd till BMR-skydd kräver BMR-skydd mindre utrymme på den *återställningspunktvolymen*. Det extra utrymmet på volymen frigörs emellertid inte. Du kan manuellt förminska volymen på den **ändra diskallokering** sida i guiden Ändra Skyddsgrupp eller med hjälp av Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation PowerShell-cmdlets.

    Om du ändrar från skydd av systemtillstånd till BMR-skydd kräver BMR-skydd mer utrymme på den *replikvolymen*. Volymen utökas automatiskt. Om du vill ändra standardutrymmesallokeringarna kan du använda Modify-DiskAllocation PowerShell-cmdleten.

-   Om du ändrar från BMR-skydd till skydd av systemtillstånd behöver mer utrymme på återställningspunktvolymen. Backup-Server kan försöka automatiskt öka volymen. Om det finns inte tillräckligt med utrymme i lagringspoolen, uppstår ett fel.

    Om du ändrar från BMR-skydd till skydd av systemtillstånd behöver du utrymme på den skyddade datorn. Detta beror på att skydd av systemtillstånd först skriver repliken till den lokala datorn och överför dem till servern för säkerhetskopiering.

## <a name="before-you-begin"></a>Innan du börjar

1.  **Distribuera Azure Backup-Server**. Kontrollera att Backup Server distribueras på rätt sätt. Mer information finns i:
    * [Systemkrav för Azure Backup Server](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Säkerhetskopiera servern skydd matris](backup-mabs-protection-matrix.md)

2.  **Konfigurera lagring**. Du kan lagra säkerhetskopierade data på disk, på band och i molnet med Azure. Mer information finns i [förbereda datalagring](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Konfigurera skyddsagenten**. Installera skyddsagenten på den dator som du vill säkerhetskopiera. Mer information finns i [distribuera DPM-skyddsagenten](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Säkerhetskopiering av systemtillstånd och bare metal
Konfigurera en skyddsgrupp enligt beskrivningen i [distribuera skyddsgrupper](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Observera att du inte kan skydda BMR och systemtillstånd för samma dator i olika grupper. När du väljer BMR aktiveras också automatiskt systemtillstånd.


1.  Öppna guiden Skapa ny Skyddsgrupp i administratörskonsolen för servern för säkerhetskopiering, Välj **skydd** > **åtgärder** > **skapa Skyddsgrupp** .

2.  På den **Välj typ av Skyddsgrupp** väljer **servrar**, och välj sedan **nästa**.

3.  På den **Välj gruppmedlemmar** sidan, expandera datorn och välj sedan antingen **BMR** eller **systemtillstånd**.

    Kom ihåg att du inte kan skydda både BMR och systemtillstånd för samma dator i olika grupper. När du väljer BMR aktiveras också automatiskt systemtillstånd. Mer information finns i [distribuera skyddsgrupper](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  På den **Välj Dataskyddsmetod** väljer du hur du vill hantera kortsiktig och långsiktig säkerhetskopiering. Kortsiktig säkerhetskopiering är alltid till disk först med alternativet för att säkerhetskopiera från disken till Azure cloud med hjälp av Azure Backup (kortsiktiga eller långsiktiga). Ett alternativ till långsiktig säkerhetskopiering till molnet är att ställa in långsiktig säkerhetskopiering till en fristående enheten eller bandet bandbibliotek som är ansluten till reservserver.

5.  På den **Välj kortvariga mål** väljer du hur du vill säkerhetskopiera till kortsiktig lagring på disk:
    1. För **Kvarhållningsintervall**, Välj hur länge du vill behålla data på disken. 
    2. För **Synkroniseringsfrekvens**, Välj hur ofta du vill köra en inkrementell säkerhetskopiering till disk. Om du inte vill ange ett intervall för säkerhetskopiering, kan du kontrollera den **precis innan en återställningspunkt** alternativet. Backup-servern körs en snabb fullständig säkerhetskopiering precis innan varje återställningspunkt schemaläggs.

6.  Om du vill lagra data på band för långsiktig lagring på den **ange långsiktiga mål** väljer du hur länge du vill behålla Banddata (1 – 99 år). 
    1. För **säkerhetskopieringsfrekvens**, väljer du hur ofta säkerhetskopiering till band ska köras. Frekvens baseras på kvarhållningsintervallet som du har valt:
        * När kvarhållningsintervallet är 1 – 99 år kan välja du att säkerhetskopieringen ska göras varje dag, varje vecka, varannan vecka, månadsvis, kvartalsvis, Halvårsvis eller årsvis.
        * När kvarhållningsintervallet är 1 – 11 månader kan välja du säkerhetskopieringen ska göras varje dag, varje vecka, varannan vecka eller varje månad.
        * När kvarhållningsintervallet är 1 – 4 veckor kan välja du att säkerhetskopieringen ska göras varje dag eller varje vecka.

    2. På den **Välj band och biblioteksinformation** sidan Välj band och bibliotek som ska användas och om data ska komprimeras och krypteras.

7.  På den **granska diskallokering** granskar lagringspoolens diskutrymme som allokerats för skyddsgruppen.

    1. **Total storlek på Data** är storleken på data som du vill säkerhetskopiera.
    2. **Diskutrymme som ska etableras på Azure Backup Server** är det utrymme som Backup Server rekommenderas för skyddsgruppen. Backup-servern väljer den perfekt säkerhetskopieringsvolymen baserat på inställningarna. Men du kan redigera säkerhetskopieringsvolymen valen i **Disk detaljer om fördelningen**. 
    3. Välj önskade lagring för arbetsbelastningar, i den nedrullningsbara menyn. Redigeringarna ändra värdena för **totala lagringsutrymmet** och **ledigt utrymme** i den **tillgängliga disklagring** fönstret. Underprovisioned utrymme är mängden lagringsutrymme Backup Server om du lägger till volymen, så smooth säkerhetskopieringar.

8.  På den **Välj Replikskapandemetod** väljer du hur du vill hantera den första fullständiga datareplikeringen. Om du väljer att replikera via nätverket rekommenderar vi att du väljer en tid. Överväg att replikera data offline med hjälp av flyttbart medium för stora mängder data eller för nätverksförhållanden som är mindre än optimala.

9. På den **välja alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan välja att köra en kontroll endast när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvenskontroll kan du köra en manuell kontroll när som helst. Att köra en manuell kontroll i den **skydd** i konsolen säkerhetskopiering serveradministratören högerklickar på skyddsgruppen och välj sedan **utför konsekvenskontroll**.

10. Om du har valt för att säkerhetskopiera till molnet med Azure Backup på den **ange Onlineskyddsdata** kontrollerar du att du väljer de arbetsbelastningar som du vill säkerhetskopiera till Azure.

11. På den **Ange schema för Online säkerhetskopiering** sidan, Välj hur ofta inkrementella säkerhetskopieringar till Azure sker. Du kan också schemalägga säkerhetskopieringar att köras varje dag, vecka, månad och år. Välj datum och tid då de ska köras. Säkerhetskopieringar kan inträffa upp till två gånger per dag. Varje gång en säkerhetskopia körs skapas en data-återställningspunkt i Azure från en kopia av säkerhetskopierade data lagras på disk för reservserver.

12. På den **ange bevarandeprincip** väljer du hur återställningspunkter som skapas från de dagliga, veckovisa, månatliga och årliga säkerhetskopiorna bevaras i Azure.

13. På den **Välj Onlinereplikeringsmetoden** väljer du hur den första fullständiga replikeringen av data sker. Du kan replikera via nätverket eller göra ett offline säkerhetskopiering (offline seeding). Offlinesäkerhetskopiering använder funktionen Azure Import. Mer information finns i [Offline säkerhetskopiering arbetsflödet i Azure Backup](backup-azure-backup-import-export.md).

14. På den **sammanfattning** granskar du inställningarna. När du har valt **Skapa grupp**, inledande replikering av data. När replikering har slutförts på den **Status** , skyddsgruppens status är **OK**. Säkerhetskopieringen sker sedan per skyddet gruppinställningar.

## <a name="recover-system-state-or-bmr"></a>Återställa systemtillstånd eller BMR
Du kan återställa BMR eller systemtillstånd på en nätverksplats. Om du säkerhetskopierade BMR kan du använda Windows Recovery Environment (WinRE) för att starta systemet och ansluta till nätverket. Sedan kan använda Windows Server Backup för att återställa från nätverksplatsen. Om du säkerhetskopierade systemtillståndet, Använd bara Windows Server Backup för att återställa från en nätverksplats nedan.

### <a name="restore-bmr"></a>Återställa BMR
Köra återställning på serverdatorn för säkerhetskopiering:

1.  I den **Recovery** rutan, hitta datorn som du vill återställa och välj sedan **Bare Metal Recovery**.

2.  Tillgängliga återställningspunkter visas i fetstil i kalendern. Välj datum och tid för återställningspunkten som du vill använda.

3.  På den **Välj typ av återställning** väljer **kopiera till en nätverksmapp.**

4.  På den **ange mål** anger där du vill kopiera data till. Kom ihåg att den angivna platsen måste ha tillräckligt med utrymme. Vi rekommenderar att du skapar en ny mapp.

5.  På den **Ange återställningsalternativ** väljer att tillämpa säkerhetsinställningarna. Markera om du vill använda lagringsnätverk (SAN)-baserade ögonblicksbilder av maskinvara, för snabbare återställning. (Detta är ett alternativ om du har ett SAN-nätverk med den här funktionen är tillgänglig och möjligheten att skapa och dela en klon så att den är skrivbar. Dessutom kan måste den skyddade datorn och Backup Server-datorn vara ansluten till samma nätverk.)

6.  Konfigurera alternativ för aviseringar. På den **bekräftelse** väljer **återställa**.

Ange platsen för resursen:

1.  Gå till mappen med säkerhetskopian i restore-plats.

2.  Dela mappen som är en nivå ovanför WindowsImageBackup så att roten för den delade mappen WindowsImageBackup är. Om du inte gör det återställningen inte att hitta säkerhetskopian. Om du vill ansluta genom att använda Windows Recovery Environment (WinRE) behöver du en resurs som du har åtkomst till i WinRE med rätt IP-adress och autentiseringsuppgifter.

Återställa systemet:

1.  Starta datorn som du vill återställa bilden med hjälp av Windows-DVD för systemet som du återställer.

2.  På den första sidan, kontrollerar du språk och nationella inställningar. På den **installera** väljer **reparera datorn**.

3.  På den **Systemåterställningsalternativ** väljer **återställa datorn med en systemavbildning som du skapade tidigare**.

4.  På den **Välj en säkerhetskopierad systemavbildning** väljer **Välj en systemavbildning** > **Avancerat** > **Sök efter en systemavbildning i nätverket**. Om en varning visas väljer du **Ja**. Gå till resursens sökväg, ange autentiseringsuppgifterna och välj återställningspunkten. Detta söker efter specifika säkerhetskopior som är tillgängliga i denna återställningspunkt. Välj den återställningspunkt som du vill använda.

5.  På den **Välj hur du vill återställa säkerhetskopian** väljer **formatera och partitionera om diskar**. Kontrollera inställningarna på nästa sida. 

6.  Om du vill starta återställningen, Välj **Slutför**. En omstart krävs.

### <a name="restore-system-state"></a>Återställa systemtillståndet

Köra återställning Backup Server:

1.  I den **Recovery** rutan, hitta den dator som du vill återställa och välj sedan **Bare Metal Recovery**.

2.  Tillgängliga återställningspunkter visas i fetstil i kalendern. Välj datum och tid för återställningspunkten som du vill använda.

3.  På den **Välj typ av återställning** väljer **kopiera till en nätverksmapp**.

4.  På den **ange mål** anger där du vill kopiera data. Kom ihåg att den angivna platsen behöver tillräckligt med utrymme. Vi rekommenderar att du skapar en ny mapp.

5.  På den **Ange återställningsalternativ** väljer att tillämpa säkerhetsinställningarna. Markera om du vill använda SAN-baserade ögonblicksbilder av maskinvara för snabbare återställning. (Detta är ett alternativ om du har ett SAN-nätverk med den här funktionen och möjlighet att skapa och dela en klon så att den blir skrivbar. Dessutom kan måste den skyddade datorn och Backup Server vara ansluten till samma nätverk.)

6.  Konfigurera alternativ för aviseringar. På den **bekräftelse** väljer **återställa**.

Kör Windows Serverbackup:

1.  Välj **åtgärder** > **återställa** > **servern** > **nästa**.

2.  Välj **en annan Server**, Välj den **Ange platstyp** och väljer sedan **Remote delade mappen**. Ange sökvägen till den mapp som innehåller återställningspunkten.

3.  På den **Välj typ av återställning** väljer **systemtillstånd**. 

4. På den **Välj plats för återställning av systemtillstånd** väljer **ursprungsplatsen**.

5.  På den **bekräftelse** väljer **återställa**. Starta om servern efter återställningen.

6.  Du kan också köra återställningen av systemtillstånd i en kommandotolk. Gör detta genom att starta Windows Server Backup på den dator som du vill återställa. Ange om du vill ha version-Identiferare vid en kommandotolk: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Använd versions-ID för att starta återställningen av systemtillstånd. Ange följande i Kommandotolken: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Bekräfta att du vill starta återställningen. Du kan se processen i Kommandotolkens fönster. En återställningslogg skapas. Starta om servern efter återställningen.

