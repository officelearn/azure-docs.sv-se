---
title: Azure Backup Server skyddar Systemtillstånd och återställer till utan operativsystem
description: Använda Azure Backup Server för att säkerhetskopiera systemtillståndet och ge bare metal recovery (BMR) skydd.
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: 7cb87847d6a1e191fb20dfa9cdf263066704eb6d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238820"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Säkerhetskopiera systemtillstånd och återställa till utan operativsystem med Azure Backup Server

Azure Backup Server säkerhetskopierar Systemtillstånd och bare metal recovery (BMR) skyddar.

*   **Säkerhetskopiering av systemtillstånd**: säkerhetskopierar operativsystemfiler, så att du kan återställa när datorn har startats men systemfiler och registret går förlorade. En säkerhetskopiering av systemtillstånd omfattar:
    * Domänmedlem: startfiler, COM +-klassens registreringsdatabas, register
    * Domänkontrollant: Windows Server Active Directory (NTDS), startfiler, COM +-klassens registreringsdatabas, register, systemvolym (SYSVOL)
    * Dator som kör klustertjänster: klusterserverns metadata
    * Dator som kör Certifikattjänster: certifikatdata
* **Bare metal-säkerhetskopiering**: säkerhetskopierar operativsystemfiler och alla data på kritiska volymer (utom användardata). Per definition omfattar en säkerhetskopiering av BMR en säkerhetskopiering av systemtillstånd. Det ger skydd när en dator inte startar och du måste återställa allt.

I följande tabell sammanfattas vad du kan säkerhetskopiera och återställa. Detaljerad information om appversioner av som kan skyddas med Systemtillstånd och BMR finns [vad gör Azure Backup Server säkerhetskopiera?](backup-mabs-protection-matrix.md).

|Backup|Problem|Återställa från säkerhetskopia av Azure Backup Server|Återställa från säkerhetskopia av systemtillståndet|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fildata**<br /><br />Regelbunden säkerhetskopiering av data<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorade fildata|Y|N|N|
|**Fildata**<br /><br />Azure Backup Server-säkerhetskopiering av fildata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**Fildata**<br /><br />Azure Backup Server-säkerhetskopiering av fildata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorad server (datavolymer orörda)|N|N|Y|
|**Fildata**<br /><br />Azure Backup Server-säkerhetskopiering av fildata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorad server (datavolymer förlorade)|Y|Nej|Ja (BMR, följt av reguljära återställning av säkerhetskopierade fildata)|
|**SharePoint-data**:<br /><br />Azure Backup Server-säkerhetskopia av servergruppdata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorad plats, listor, listobjekt och dokument|Y|N|N|
|**SharePoint-data**:<br /><br />Azure Backup Server-säkerhetskopia av servergruppdata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**SharePoint-data**:<br /><br />Azure Backup Server-säkerhetskopia av servergruppdata<br /><br />BMR-/ systemtillståndssäkerhetskopia|Haveriberedskap|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/systemtillståndssäkerhetskopia av värd|Förlorad VM|Y|N|N|
|Hyper-V<br /><br />Azure Backup Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/systemtillståndssäkerhetskopia av värd|Saknad eller skadad operativsystem|N|Y|Y|
|Hyper-V<br /><br />Azure Backup Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer orörda)|N|N|Y|
|Hyper-V<br /><br />Azure Backup Server-säkerhetskopiering av Hyper-V-värd eller gäst<br /><br />BMR/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer förlorade)|N|N|Y<br /><br />BMR, följt av reguljära återställning av Azure Backup Server|
|SQL Server/Exchange<br /><br />Azure Backup Server app-säkerhetskopiering<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorade AppData|Y|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server app-säkerhetskopiering<br /><br />BMR-/ systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server app-säkerhetskopiering<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar orörda)|N|N|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server app-säkerhetskopiering<br /><br />BMR-/ systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar förlorade)|N|N|Y<br /><br />BMR-återställning, följt av reguljära återställning av Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Hur säkerhetskopiering av systemtillstånd fungerar

När en säkerhetskopiering av systemtillstånd körs kommunicerar Backup Server med Windows Server Backup och begära en säkerhetskopia av serverns systemtillstånd. Som standard Backup Server och Windows Server Backup kan du använda den enhet som har mest tillgängligt ledigt utrymme. Information om den här enheten sparas i filen PSDataSourceConfig.xml. Det här är den enhet som Windows Server Backup använder för säkerhetskopiering.

Du kan anpassa den enhet som säkerhetskopieringsserver använder för säkerhetskopian av systemtillståndet. Gå till C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources på den skyddade servern. Öppna filen PSDataSourceConfig.xml för redigering. Ändra den \<FilesToProtect\> värde för enhetsbeteckningen. Spara och stäng filen. Om det finns en skyddsgruppuppsättning att skydda systemtillståndet på datorn, kör en konsekvenskontroll. Om en avisering, väljer **ändra skyddsgrupp** i aviseringen och slutför sedan guiden. Kör sedan en annan konsekvenskontroll.

Observera att om skyddsservern finns i ett kluster, är det möjligt att en klusterenhet markeras som enheten med mest ledigt utrymme. Om ägarskap som enheten har växlats till en annan nod och en system-tillstånd, säkerhetskopiering körs, enheten är inte tillgänglig och säkerhetskopieringen misslyckas. I det här scenariot kan du ändra PSDataSourceConfig.xml att peka mot en lokal enhet.

Windows Server Backup skapar sedan en mapp som kallas WindowsImageBackup i roten av återställningsmappen. Eftersom Windows Server Backup skapar säkerhetskopian placeras alla data i den här mappen. När säkerhetskopieringen är klar överförs filen till Backup Server-datorn. Notera följande information:

* Den här mappen och dess innehåll rensas inte när säkerhetskopieringen eller överföringen är klar. Det bästa sättet att tänka på detta är att området är som har reserverats för nästa gång en säkerhetskopia är klar.
* Mappen skapas varje gång en säkerhetskopia görs. Tids- och datumstämpel samma tid för senaste systemtillstånd.

## <a name="bmr-backup"></a>BMR säkerhetskopiering

För BMR (inklusive en säkerhetskopia av systemtillståndet), sparas säkerhetskopieringsjobbet direkt till en resurs på Backup Server-datorn. Det är inte sparas i en mapp på den skyddade servern.

Säkerhetskopieringsserver anropar Windows Server Backup och delar ut replikvolymen för BMR säkerhetskopian. I detta fall anvisar den inte Windows Server Backup använder enheten med mest ledigt utrymme. I stället använder den resurs som har skapats för jobbet.

När säkerhetskopieringen är klar överförs filen till Backup Server-datorn. Loggar lagras i C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

-   BMR stöds inte för datorer som kör Windows Server 2003 eller för datorer som kör ett klientoperativsystem.

-   Du kan inte skydda BMR och systemtillstånd för samma dator i olika skyddsgrupper.

-   En Backup Server-dator kan inte skydda sig själv för BMR.

-   Kortsiktigt skydd till band (disk till band eller D2T) stöds inte för BMR. Långsiktig lagring på band (disk-till-disk till band eller D2D2T) stöds.

-   Windows Server Backup måste installeras på den skyddade datorn för BMR-skydd.

-   För BMR-skydd har till skillnad från för skydd av systemtillstånd, Backup Server inte några krav på diskutrymme på den skyddade datorn. Windows Server Backup Överför säkerhetskopiorna direkt till Backup Server-datorn. Säkerhetskopiering överföringsjobbet visas inte i säkerhetskopieringsservern **jobb** vy.

-   Backup Server reserverar 30 GB utrymme på replikvolymen för BMR. Du kan ändra detta på den **diskallokering** sida i guiden Ändra Skyddsgrupp eller med hjälp av Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation PowerShell-cmdlets. På återställningspunktvolymen kräver BMR-skydd ca 6 GB för en lagring av fem dagar.
    * Observera att du kan minska replikeringsvolymens storlek till mindre än 15 GB.
    * Säkerhetskopieringsserver beräkna inte storleken på BMR-datakällan. Den förutsätter 30 GB för alla servrar. Ändra värdet baserat på storleken på BMR säkerhetskopieringar som du förväntar dig i din miljö. Storleken på en BMR-säkerhetskopia kan beräknas ungefär som summan av använt utrymme på alla kritiska volymer. Kritiska volymer = startvolym + systemvolym + volym där systemtillstånd, till exempel Active Directory.

-   Om du ändrar från skydd av systemtillstånd till BMR-skydd kräver BMR-skydd mindre utrymme på den *återställningspunktvolymen*. Det extra utrymmet på volymen frigörs emellertid inte. Du kan manuellt förminska volymen på den **ändra diskallokering** sidan i guiden Ändra Skyddsgrupp eller genom att använda Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation PowerShell-cmdlets.

    Om du ändrar från skydd av systemtillstånd till BMR-skydd kräver BMR-skydd mer utrymme på den *replikvolymen*. Volymen utökas automatiskt. Om du vill ändra standardutrymmesallokeringarna kan du använda Modify-DiskAllocation PowerShell-cmdleten.

-   Om du ändrar från BMR-skydd till skydd av systemtillstånd behöver du mer utrymme på återställningspunktvolymen. Säkerhetskopieringsservern kan försöka att automatiskt öka volymen. Om det finns inte tillräckligt med utrymme i lagringspoolen, uppstår ett fel.

    Om du ändrar från BMR-skydd till skydd av systemtillstånd behöver du utrymme på den skyddade datorn. Detta är eftersom skydd av systemtillstånd först skriver repliken till den lokala datorn och sedan överför den till Backup Server-datorn.

## <a name="before-you-begin"></a>Innan du börjar

1.  **Distribuera Azure Backup Server**. Kontrollera att Backup Server distribueras på rätt sätt. Mer information finns i:
    * [Systemkrav för Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Skyddsöversikt för Backup Server](backup-mabs-protection-matrix.md)

2.  **Konfigurera lagring**. Du kan lagra säkerhetskopierade data på disk, på band och i molnet med Azure. Mer information finns i [förbereda datalagring](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Konfigurera skyddsagenten**. Installera skyddsagenten på den dator som du vill säkerhetskopiera. Mer information finns i [distribuera DPM-skyddsagenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Säkerhetskopiera systemtillstånd och bare metal
Konfigurera en skyddsgrupp enligt beskrivningen i [distribuera skyddsgrupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Observera att du inte kan skydda BMR och systemtillstånd för samma dator i olika grupper. När du väljer BMR aktiveras också automatiskt systemtillstånd.


1.  Öppna guiden Skapa ny Skyddsgrupp i Backup Server-administratörskonsolen, Välj **Protection** > **åtgärder** > **skapa Skyddsgrupp** .

2.  På den **Välj typ av Skyddsgrupp** väljer **servrar**, och välj sedan **nästa**.

3.  På den **Välj gruppmedlemmar** , expandera datorn och välj antingen **BMR** eller **systemtillstånd**.

    Kom ihåg att du inte kan skydda både BMR och systemtillstånd för samma dator i olika grupper. När du väljer BMR aktiveras också automatiskt systemtillstånd. Mer information finns i [distribuera skyddsgrupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  På den **Välj Dataskyddsmetod** väljer du hur du vill hantera säkerhetskopieringen på kort och lång sikt. Kortsiktig säkerhetskopiering är alltid först på disk, med alternativet att säkerhetskopiera från disk till Azure i molnet med hjälp av Azure Backup (kortsiktig eller långsiktig). Ett alternativ till långsiktig säkerhetskopiering till molnet är att konfigurera långsiktig säkerhetskopiering till ett fristående eller ett bandbibliotek bandbibliotek som är ansluten till säkerhetskopieringsservern.

5.  På den **Välj kortvariga mål** väljer du hur du vill säkerhetskopiera till den kortsiktiga lagringen på disk:
    1. För **Kvarhållningsintervall**, Välj hur länge du vill behålla data på disken. 
    2. För **Synkroniseringsfrekvens**, väljer du hur ofta du vill köra en inkrementell säkerhetskopiering till disk. Om du inte vill ange ett intervall för säkerhetskopiering kan du kontrollera den **precis innan en återställningspunkt** alternativet. Säkerhetskopieringsserver körs en uttrycklig och fullständig säkerhetskopiering precis innan varje återställningspunkt schemaläggs.

6.  Om du vill lagra data på band för långsiktig lagring på den **ange långsiktiga mål** väljer du hur länge du vill behålla Banddata (1 – 99 år). 
    1. För **säkerhetskopieringsfrekvens**, väljer du hur ofta säkerhetskopiering till band ska köras. Frekvensen baseras på det kvarhållandeintervall som du har valt:
        * När kvarhållningsintervallet är 1 – 99 år kan välja du att säkerhetskopieringen ska göras varje dag, varje vecka, varannan vecka, månadsvis, kvartalsvis, Halvårsvis eller årsvis.
        * När kvarhållningsintervallet är 1 – 11 månader kan välja du säkerhetskopieringen ska göras varje dag, varje vecka, varannan vecka eller varje månad.
        * När kvarhållningsintervallet är 1 – 4 veckor kan välja du att säkerhetskopieringen ska göras varje dag eller varje vecka.

    2. På den **Välj band och biblioteksinformation** väljer du det band och bibliotek som ska användas, och om data ska komprimeras och krypteras.

7.  På den **granska diskallokering** granskar du lagringspoolens diskutrymme som allokerats för skyddsgruppen.

    1. **Total datastorlek** är storleken på de data du vill säkerhetskopiera.
    2. **Diskutrymme som ska etableras på Azure Backup Server** är det utrymme som Backup Server rekommenderar för skyddsgruppen. Säkerhetskopieringsserver väljer den mest lämpliga säkerhetskopieringsvolymen, baserat på inställningarna. Men du kan redigera säkerhetskopieringsvolym under **Disk detaljer om fördelningen**. 
    3. Välj önskad lagringsplats för arbetsbelastningar i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **totalt lagringsutrymme** och **ledigt lagringsutrymme** i den **tillgängligt Disklagringsutrymme** fönstret. Underetablerat utrymme är mängden lagringsutrymme som Backup Server föreslår att du lägger till volymen för att se till att smidiga säkerhetskopieringar.

8.  På den **väljer Replikskapandemetod** väljer du hur du vill hantera den första fullständiga datareplikeringen. Om du vill replikera via nätverket rekommenderar vi att du väljer en tid med låg belastning. Överväg att replikera data offline med hjälp av flyttbara media för stora mängder data eller för nätverksförhållanden som är mindre än optimala.

9. På den **välja alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan välja att köra en kontroll endast när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatiska konsekvenskontroller kan köra du en manuell kontroll när som helst. Att köra en manuell kontroll i den **Protection** Backup Server-administratörskonsolen högerklickar på skyddsgruppen och välj sedan **utför konsekvenskontroll**.

10. Om du har valt för att säkerhetskopiera till molnet med hjälp av Azure Backup på den **ange Onlineskyddsdata** kontrollerar du att du väljer de arbetsbelastningar du vill säkerhetskopiera till Azure.

11. På den **Ange schema för Online Backup** sidan, väljer du hur ofta inkrementella säkerhetskopieringar till Azure sker. Du kan schemalägga säkerhetskopieringar att köras varje dag, vecka, månad och år och välj tiden och datumet då de ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång en säkerhetskopiering körs skapas en återställningspunkt för data i Azure från kopian av säkerhetskopierade data som lagras på säkerhetskopieringsservern disken.

12. På den **ange bevarandeprincip** väljer du hur återställningspunkter som skapas från de dagliga, veckovisa, månatliga och årliga säkerhetskopiorna bevaras i Azure.

13. På den **Välj Onlinereplikering** väljer du hur den första fullständiga replikeringen av data sker. Du kan replikera via nätverket eller göra en offlinesäkerhetskopiering (offlineseeding). Vid offlinesäkerhetskopiering används funktionen Azure Import. Mer information finns i [arbetsflöde för säkerhetskopiering Offline i Azure Backup](backup-azure-backup-import-export.md).

14. På den **sammanfattning** kan du granska dina inställningar. När du har valt **Skapa grupp**, utförs inledande replikering av data. När datareplikeringen är klar på den **Status** sidan skyddsgruppens status är **OK**. Säkerhetskopieringen sker sedan per skyddet gruppinställningar.

## <a name="recover-system-state-or-bmr"></a>Återställa systemtillstånd eller BMR
Du kan återställa BMR eller systemtillstånd på en nätverksplats. Om du säkerhetskopierade BMR kan du använda Windows Recovery Environment (WinRE) för att starta datorn och ansluta till nätverket. Använd sedan Windows Server Backup för att återställa från nätverksplatsen. Om du säkerhetskopierade systemtillståndet, Använd bara Windows Server Backup för att återställa från nätverksplatsen.

### <a name="restore-bmr"></a>Återställa BMR
Köra återställning på Backup Server-datorn:

1.  I den **Recovery** rutan, hitta datorn du vill återställa och välj sedan **Bare Metal Recovery**.

2.  Tillgängliga återställningspunkter visas i fetstil i kalendern. Välj datum och tid för den återställningspunkt som du vill använda.

3.  På den **Välj typ av återställning** väljer **kopiera till en nätverksmapp.**

4.  På den **ange mål** markerar där du vill kopiera data. Kom ihåg att det markerade målet måste ha tillräckligt med utrymme. Vi rekommenderar att du skapar en ny mapp.

5.  På den **Ange återställningsalternativ** väljer säkerhetsinställningarna tillämpas. Välj om du vill använda lagringsnätverk (SAN)-baserade ögonblicksbilder av maskinvara, för snabbare återställning. (Detta är ett alternativ bara om du har en SAN-nätverk med den här funktionen tillgänglig och möjligheten att skapa och dela en klon så att de blir skrivbar. Dessutom kan måste den skyddade datorn och Backup Server-datorn vara ansluten till samma nätverk.)

6.  Ställ in meddelandealternativ. På den **bekräftelse** väljer **återställa**.

Ställ in plats där:

1.  Gå till mappen med säkerhetskopian i restore-platsen.

2.  Dela mappen som är en nivå ovanför WindowsImageBackup så att roten för den delade mappen är mappen WindowsImageBackup. Om du inte gör detta kommer återställningen inte att hitta säkerhetskopian. För att ansluta genom att använda Windows Recovery Environment (WinRE), behöver du en resurs som du kan komma åt i WinRE med rätt IP-adress och autentiseringsuppgifter.

Återställa systemet:

1.  Starta datorn där du vill återställa avbildningen med hjälp av Windows-DVD för systemet du återställer.

2.  På den första sidan, kontrollerar du språk och nationella inställningar. På den **installera** väljer **reparera datorn**.

3.  På den **alternativ för Systemåterställning** väljer **Återställ datorn med hjälp av en systemavbildning som du skapade tidigare**.

4.  På den **Välj en säkerhetskopierad systemavbildning** väljer **Välj en systemavbildning** > **Avancerat** > **Sök efter en systemavbildning i nätverket**. Om en varning visas, Välj **Ja**. Gå till resursens sökväg, ange autentiseringsuppgifterna och välj återställningspunkten. Detta söker efter specifika säkerhetskopior som är tillgängliga i denna återställningspunkt. Välj den återställningspunkt som du vill använda.

5.  På den **Välj hur säkerhetskopian ska återställas** väljer **formatera och partitionera om diskar**. Kontrollera inställningar på nästa sida. 

6.  Om du vill börja återställningen, Välj **Slutför**. En omstart krävs.

### <a name="restore-system-state"></a>Återställa systemtillstånd

Kör återställning i Backup Server:

1.  I den **Recovery** rutan, hitta den dator som du vill återställa och välj sedan **Bare Metal Recovery**.

2.  Tillgängliga återställningspunkter visas i fetstil i kalendern. Välj datum och tid för den återställningspunkt som du vill använda.

3.  På den **Välj typ av återställning** väljer **kopiera till en nätverksmapp**.

4.  På den **ange mål** markerar där du vill kopiera data. Kom ihåg att det valda målet behöver tillräckligt med utrymme. Vi rekommenderar att du skapar en ny mapp.

5.  På den **Ange återställningsalternativ** väljer säkerhetsinställningarna tillämpas. Välj om du vill använda SAN-baserade ögonblicksbilder av maskinvara för snabbare återställning. (Detta är ett alternativ bara om du har ett SAN-nätverk med den här funktionen och möjligheten att skapa och dela en klon så att den blir skrivbar. Dessutom kan måste den skyddade datorn och Backup Server-servern vara ansluten till samma nätverk.)

6.  Ställ in meddelandealternativ. På den **bekräftelse** väljer **återställa**.

Kör Windows Serverbackup:

1.  Välj **åtgärder** > **återställa** > **den här servern** > **nästa**.

2.  Välj **en annan Server**väljer den **Ange platstyp** och välj sedan **delad fjärrmapp**. Ange sökvägen till den mapp som innehåller återställningspunkten.

3.  På den **Välj typ av återställning** väljer **systemtillstånd**. 

4. På den **Välj plats för återställning av systemtillståndet** väljer **ursprungsplatsen**.

5.  På den **bekräftelse** väljer **återställa**. Starta om servern efter återställningen.

6.  Du kan också köra återställningen av systemtillståndet i Kommandotolken. Gör detta genom att starta Windows Server Backup på den dator som du vill återställa. Om du vill ha version-identifierare i Kommandotolken anger du: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Använd detta versions-ID för att starta återställningen av systemtillståndet. Ange följande i Kommandotolken: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Bekräfta att du vill starta återställningen. Du kan se processen i Kommandotolkens fönster. En återställningslogg skapas. Starta om servern efter återställningen.

