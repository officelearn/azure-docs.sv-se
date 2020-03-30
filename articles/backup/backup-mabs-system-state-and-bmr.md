---
title: Systemtillstånd och skydd för återvinning av bare-metal
description: Använd Azure Backup Server för att säkerhetskopiera ditt systemtillstånd och tillhandahålla BMR-skydd (bare-metal recovery).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505873"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Säkerhetskopiera systemtillstånd och återställa till bare metal med hjälp av Azure Backup Server

Azure Backup Server säkerhetskopierar systemtillstånd och ger BMR-skydd (bare-metal recovery).

* **Säkerhetskopiering av systemtillstånd:** Säkerhetskopierar operativsystemfiler. Med den här säkerhetskopian kan du återställa när en dator startar, men systemfiler och registret går förlorade. En säkerhetskopia av systemtillstånd innehåller följande element:
  * Domänmedlem: startfiler, COM+-klassens registreringsdatabas, register
  * Domänkontrollant: Windows Server Active Directory (NTDS), startfiler, REGISTRERINGSDATABAS FÖR COM+-klass, register, SYSTEMVOLYM (SYSVOL)
  * Dator som kör klustertjänster: Klusterservermetadata
  * Dator som kör certifikattjänster: Certifikatdata
* **Bare-metal backup**: Säkerhetskopierar operativsystemfiler och alla data om kritiska volymer, med undantag för användardata. Per definition innehåller en BMR-säkerhetskopia en säkerhetskopia av systemtillståndet. Det ger skydd när en dator inte startar och du måste återställa allt.

I följande tabell sammanfattas vad du kan säkerhetskopiera och återställa. Information om appversioner som systemtillståndet och BMR kan skydda finns i [Vad säkerhetskopierar Azure Backup Server?](backup-mabs-protection-matrix.md).

|Säkerhetskopiering|Problem|Återställa från säkerhetskopiering av Azure Backup Server|Återställa systemtillståndssäkerhetskopia|Återställning utan operativsystem|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fildata**<br /><br />Regelbunden säkerhetskopiering av data<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorade fildata|Y|N|N|
|**Fildata**<br /><br />Azure Backup Server säkerhetskopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**Fildata**<br /><br />Azure Backup Server säkerhetskopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (datavolymerna intakta)|N|N|Y|
|**Fildata**<br /><br />Azure Backup Server säkerhetskopiering av fildata<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (datavolymer förlorade)|Y|N|Y<br /><br />BMR, följt av regelbunden återställning av säkerhetskopierade fildata|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhetskopiering av serverdata<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad webbplats, listor, listobjekt, dokument|Y|N|N|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhetskopiering av serverdata<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|**SharePoint-data**<br /><br />Azure Backup Server säkerhetskopiering av serverdata<br /><br />BMR-/systemtillståndssäkerhetskopia|Haveriberedskap|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server backup av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad VM|Y|N|N|
|Hyper-V<br /><br />Azure Backup Server backup av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Saknad eller skadad operativsystem|N|Y|Y|
|Hyper-V<br /><br />Azure Backup Server backup av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer orörda)|N|N|Y|
|Hyper-V<br /><br />Azure Backup Server backup av Hyper-V-värd eller gäst<br /><br />BMR-/systemtillståndssäkerhetskopia av värd|Förlorad Hyper-V-värd (virtuella datorer förlorade)|N|N|Y<br /><br />BMR, följt av regelbunden Azure Backup Server-återställning|
|SQL Server/Exchange<br /><br />Säkerhetskopiering av Azure Backup Server-appen<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorade appdata|Y|N|N|
|SQL Server/Exchange<br /><br />Säkerhetskopiering av Azure Backup Server-appen<br /><br />BMR-/systemtillståndssäkerhetskopia|Saknad eller skadad operativsystem|N|Y|Y|
|SQL Server/Exchange<br /><br />Säkerhetskopiering av Azure Backup Server-appen<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar orörda)|N|N|Y|
|SQL Server/Exchange<br /><br />Säkerhetskopiering av Azure Backup Server-appen<br /><br />BMR-/systemtillståndssäkerhetskopia|Förlorad server (databas/transaktionsloggar förlorade)|N|N|Y<br /><br />BMR-återställning, följt av regelbunden återställning av Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Hur säkerhetskopiering av systemtillstånd fungerar

När en säkerhetskopiering av systemtillstånd körs kommunicerar Säkerhetskopieringsserver med Windows Server Backup för att begära en säkerhetskopia av serverns systemtillstånd. Som standard använder Säkerhetskopieringsserver och Windows Server Backup den enhet som har det mest tillgängliga lediga utrymmet. Information om den här enheten sparas i filen *PSDataSourceConfig.xml.* 

Du kan anpassa enheten som Backup Server använder för säkerhetskopiering av systemtillstånd: 

1. Gå till *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*på den skyddade servern. 
1. Öppna *filen PSDataSourceConfig.xml* för redigering. 
1. Ändra \<värdet FilesToProtect\> för enhetsbeteckningen. 
1. Spara och stäng filen. 

Om en skyddsgrupp är inställd på att skydda datorns systemtillstånd kör du en konsekvenskontroll. Om en avisering genereras väljer du **Ändra skyddsgrupp** i aviseringen och slutför sedan sidorna i guiden. Kör sedan en annan konsekvenskontroll.

Om skyddsservern finns i ett kluster kan en klusterenhet väljas som den enhet som har mest ledigt utrymme. Om ägarskapet för enheten växlas till en annan nod och säkerhetskopiering av systemtillstånd körs, är enheten inte tillgänglig och säkerhetskopian misslyckas. I det här fallet ändra *PSDataSourceConfig.xml* att peka på en lokal enhet.

Därefter skapar Windows Server Backup en mapp med namnet *WindowsImageBackup* i roten till återställningsmappen. När säkerhetskopian skapas i Windows Server skapas placeras alla data i den här mappen. När säkerhetskopian är klar överförs filen till backupserver-datorn. Notera följande information:

* Den här mappen och dess innehåll rensas inte när säkerhetskopieringen eller överföringen är klar. Det bästa sättet att tänka på detta är att utrymmet är reserverat för nästa gång en säkerhetskopia är klar.
* Mappen skapas för varje säkerhetskopia. Tids- och datumstämpeln återspeglar tiden för den senaste säkerhetskopieringen av systemtillstånd.

## <a name="how-bmr-backup-works"></a>Så här fungerar BMR-säkerhetskopiering

För BMR (inklusive en säkerhetskopiering av systemtillstånd) sparas säkerhetskopieringsjobbet direkt till en resurs på backupserver-datorn. Det sparas inte i en mapp på den skyddade servern.

Backup Server anropar Windows Server Backup och delar ut replikvolymen för den BMR-säkerhetskopian. I det här fallet krävs inte Windows Server Backup för att använda den enhet som har mest ledigt utrymme. I stället används resursen som skapades för jobbet.

När säkerhetskopian är klar överförs filen till backupserver-datorn. Loggar lagras i *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

* BMR stöds inte för datorer som kör Windows Server 2003 eller för datorer som kör ett klientoperativsystem.

* Du kan inte skydda BMR och systemtillstånd för samma dator i olika skyddsgrupper.

* En backup server-dator kan inte skydda sig själv för BMR.

* Korttidsskydd för band (disk till band eller D2T) stöds inte för BMR. Långtidslagring till band (disk till disk till band eller D2D2T) stöds.

* För BMR-skydd måste Windows Server Backup vara installerat på den skyddade datorn.

* För BMR-skydd har Säkerhetskopieringsserver, till skillnad från systemtillståndsskydd, inga utrymmeskrav på den skyddade datorn. Säkerhetskopiering av Windows Server överför säkerhetskopiering direkt till backupserver-datorn. Jobbet för säkerhetskopieringsöverföring visas inte i vyn **Säkerhetskopieringsserverjobb.**

* Säkerhetskopieringsserver reserverar 30 GB utrymme på replikvolymen för BMR. Du kan ändra den här utrymmestilldelningen på sidan **Diskallokering** i guiden Ändra skydd. Du kan också använda PowerShell-cmdlets för Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation. På återställningspunkten kräver BMR-skydd ca 6 GB för kvarhållning av fem dagar.
  * Du kan inte minska replikvolymens storlek till mindre än 15 GB.
  * Säkerhetskopieringsserver beräknar inte storleken på BMR-datakällan. Det förutsätter 30 GB för alla servrar. Ändra värdet baserat på storleken på BMR-säkerhetskopior som du förväntar dig i din miljö. Du kan ungefär beräkna storleken på en BMR-säkerhetskopia som summan av använt utrymme på alla kritiska volymer. Kritiska volymer = startvolym + systemvolym + volymvärdsystemtillståndsdata, till exempel Active Directory.

* Om du byter från systemtillståndsskydd till BMR-skydd kräver BMR-skydd mindre utrymme på *återställningspunktens volym*. Det extra utrymmet på volymen återvinns dock inte. Du kan manuellt minska volymstorleken på sidan **Ändra diskallokering** i guiden Ändra skydd. Du kan också använda PowerShell-cmdlets för Get-DatasourceDiskAllocation och Set-DatasourceDiskAllocation.

    Om du byter från systemtillståndsskydd till BMR-skydd kräver BMR-skydd mer utrymme på *replikvolymen*. Volymen utökas automatiskt. Om du vill ändra standardutrymmesallokeringarna använder du cmdleten Ändra diskAllocation PowerShell.

* Om du byter från BMR-skydd till systemtillståndsskydd behöver du mer utrymme på återställningspunktvolymen. Backup Server kan försöka öka volymen automatiskt. Om lagringspoolen inte har tillräckligt med utrymme uppstår ett fel.

    Om du byter från BMR-skydd till systemtillståndsskydd behöver du utrymme på den skyddade datorn. Du behöver utrymmet eftersom systemtillståndsskyddet först skriver repliken till den lokala datorn och sedan överför repliken till backupserver-datorn.

## <a name="before-you-begin"></a>Innan du börjar

1. **Distribuera Azure Backup Server**. Kontrollera att säkerhetskopieringsservern har distribuerats korrekt. Mer information finns i:
    * [Systemkrav för Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matris för skydd av säkerhetskopieringsserver](backup-mabs-protection-matrix.md)

1. **Konfigurera lagring**. Du kan lagra säkerhetskopierade data på disk, på band och i molnet med Azure. Mer information finns i [Förbereda datalagring](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Ställ in skyddsagenten**. Installera skyddsagenten på den dator som du vill säkerhetskopiera. Mer information finns i [Distribuera DPM-skyddsagenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Säkerhetskopiera systemtillstånd och utan operativsystem (Bare Metal)

Så här säkerhetskopierar du systemtillstånd och bar metall:

1. Om du vill öppna guiden Skapa ny skyddsgrupp väljer du > **Skyddsgrupp** > för skydd för **skydd**för skydd i administratörskonsolen För säkerhetskopiering**av**servern .

1. På sidan **Välj skyddsgrupptyp** väljer du **Servrar**och väljer sedan **Nästa**.

1. På sidan **Välj gruppmedlemmar** expanderar du datorn och väljer sedan **antingen BMR** eller **systemtillstånd**.

    Kom ihåg att du inte kan skydda både BMR och systemtillstånd för samma dator i olika grupper. När du väljer BMR aktiveras systemtillståndet automatiskt. Mer information finns i [Distribuera skyddsgrupper](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

1. På sidan **Välj dataskyddsmetod** väljer du hur du ska hantera kortsiktig säkerhetskopiering och långsiktig säkerhetskopiering. 

    Kortsiktig säkerhetskopiering är alltid att diska först, med möjlighet att säkerhetskopiera från disken till Azure med hjälp av Azure Backup (kortsiktiga eller långsiktiga). Ett alternativ till långsiktig säkerhetskopiering till molnet är att konfigurera långsiktig säkerhetskopiering till en fristående bandenhet eller ett bandbibliotek som är anslutet till Säkerhetskopieringsserver.

1. På sidan **Välj kortsiktiga mål** väljer du hur du säkerhetskopierar till korttidslagring på disk:
    * För **kvarhållningsintervall**väljer du hur länge data ska lagras på disken.
    * För **synkroniseringsfrekvens**väljer du hur ofta en inkrementell säkerhetskopia ska köras till disken. Om du inte vill ställa in ett säkerhetskopieringsintervall kan du välja **Precis före en återställningspunkt**. Backup Server kör en snabb fullständig säkerhetskopiering strax innan varje återställningspunkt schemaläggs.

1. Om du vill lagra data på band för långsiktig lagring väljer du hur länge du ska behålla banddata (1 till 99 år) på sidan **Ange långsiktiga mål.**
    1. För **Frekvensen för säkerhetskopiering**väljer du hur ofta säkerhetskopieringen ska köras på band. Frekvensen baseras på det kvarhållningsområde du valt:
        * När retentionsintervallet är 1 till 99 år kan du säkerhetskopiera dagligen, veckovis, varannan vecka, månadsvis, kvartalsvis, halvårsvis eller årligen.
        * När kvarhållningsintervallet är 1 till 11 månader kan du säkerhetskopiera dagligen, veckovis, varannan vecka eller varje månad.
        * När kvarhållningsintervallet är 1 till 4 veckor kan du säkerhetskopiera dagligen eller veckovis.

    1. På sidan **Välj band- och biblioteksinformation** väljer du det band och bibliotek som ska användas. Välj också om data ska komprimeras och krypteras.

1. På sidan **Granska diskallokering** granskar du det diskutrymme för lagringspool som är tillgängligt för skyddsgruppen.

    * **Total datastorlek** är storleken på de data som du vill säkerhetskopiera.
    * **Diskutrymme som ska etableras på Azure Backup Server** är det utrymme som Backup Server rekommenderar för skyddsgruppen. Backup Server använder dessa inställningar för att välja den perfekta säkerhetskopieringsvolymen. Du kan redigera alternativen för säkerhetskopieringsvolymen i **Diskallokeringsinformation**.
    * Välj önskad lagring i den nedrullningsbara menyn för arbetsbelastningar. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetablerat utrymme är den mängd lagringsutrymme som Backup Server föreslår som du lägger till volymen för att säkerställa smidiga säkerhetskopior.

1. På sidan **Välj metod för att skapa repliker** väljer du hur du ska hantera den första fullständig datareplikeringen. 

    Om du väljer att replikera över nätverket rekommenderar vi att du väljer en lågtrafiktid. För stora mängder data eller för nätverksvillkor som är mindre än optimala bör du överväga att replikera data offline med hjälp av flyttbara media.

1. På sidan Välj alternativ för **konsekvenskontroll** väljer du hur konsekvenskontroller ska automatiseras. 

    Du kan välja att bara köra en kontroll när replikdata blir inkonsekventa eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvenskontroll kan du när som helst köra en manuell kontroll. Om du vill köra en manuell kontroll högerklickar du på skyddsgruppen i området **Skydd** i administratörskonsolen För säkerhetskopieringsserver och väljer sedan **Utföra konsekvenskontroll**.

1. Om du väljer att säkerhetskopiera till molnet med hjälp av Azure Backup väljer du de arbetsbelastningar som du vill säkerhetskopiera till Azure på sidan **Ange onlineskyddsdata.**

1. På sidan **Ange schema för onlinesäkerhetsuppställning** väljer du hur ofta du ska säkerhetskopiera till Azure stegvis. 

    Du kan schemalägga säkerhetskopior så att de körs varje dag, vecka, månad och år. Du kan också välja tid och datum då säkerhetskopior ska köras. Säkerhetskopieringar kan göras upp till två gånger per dag. Varje gång en säkerhetskopia körs skapas en dataåterställningspunkt i Azure från kopian av säkerhetskopieringsdata som lagras på backup Server-disken.

1. På sidan **Ange onlinelagringsprincip** väljer du hur återställningspunkterna som skapas från de dagliga, veckovisa, månadsvisa och årliga säkerhetskopiorna ska sparas i Azure.

1. På sidan **Välj onlinereplikering** väljer du hur den första fullständiga replikeringen av data ska ske. 

    Du kan replikera via nätverket eller säkerhetskopiera offline (offline-sådd). En offline säkerhetskopiering använder Azure Import-funktionen. Mer information finns [i Arbetsflöde för offlinesäkerhetskopiering i Azure Backup](offline-backup-azure-data-box.md).

1. Granska inställningarna på sidan **Sammanfattning.** När du har valt **Skapa grupp**sker den första replikeringen av data. När datareplikeringen är klar på sidan **Status** är skyddsgruppsstatusen **OK**. Säkerhetskopior sker sedan enligt skyddsgruppsinställningarna.

## <a name="recover-system-state-or-bmr"></a>Återställa systemtillstånd eller BMR

Du kan återställa BMR eller systemtillstånd på en nätverksplats. Om du säkerhetskopierade BMR använder du WinRE (Windows Recovery Environment) för att starta systemet och ansluta det till nätverket. Använd sedan Windows Server Backup för att återställa från nätverksplatsen. Om du säkerhetskopierade systemtillståndet använder du bara Windows Server Backup för att återställa från nätverksplatsen.

### <a name="restore-bmr"></a>Återställa BMR

Så här kör du återställning på backupserver-datorn:

1. Leta reda **på** den dator som du vill återställa i återställningsfönstret. Välj sedan **Bare Metal Recovery**.

1. Tillgängliga återställningspunkter anges i fetstil i kalendern. Välj datum och tid för den återställningspunkt som du vill använda.

1. På sidan **Välj återställningstyp** väljer du **Kopiera till en nätverksmapp**.

1. På sidan **Ange mål** väljer du målet för de kopierade data. 

    Kom ihåg att destinationen måste ha tillräckligt med utrymme för data. Vi rekommenderar att du skapar en ny mapp för målet.

1. Välj säkerhetsinställningarna på sidan **Ange återställningsalternativ.** Välj sedan om du vill använda SAN-baserade maskinvaruögonblicksbilder (Storage Area Network) för snabbare återställning. Det här alternativet är endast tillgängligt om:
    * Du har ett SAN som tillhandahåller den här funktionen. 
    * Du kan skapa och dela en klon för att göra den skrivbar.
    * Den skyddade datorn och datorn för säkerhetskopieringsserver är anslutna till samma nätverk.

1. Ställ in meddelandealternativ. 
1. På sidan **Bekräftelse** väljer du **Återställ**.

Så här ställer du in delningsplatsen:

1. På återställningsplatsen går du till mappen som har säkerhetskopian.

1. Dela mappen som är en nivå ovanför *WindowsImageBackup* så att roten till den delade mappen är mappen *WindowsImageBackup.* 

    Om du inte delar den här mappen hittar återställningen inte säkerhetskopian. Om du vill ansluta med WinRE behöver du en resurs som du kan komma åt i WinRE med rätt IP-adress och autentiseringsuppgifter.

Så här återställer du systemet:

1. Starta datorn där du vill återställa avbildningen med hjälp av Windows DVD för det system du återställer.

1. På den första sidan kontrollerar du inställningarna för språk och språk. På sidan **Installera** väljer du **Reparera datorn**.

1. På sidan **Alternativ för systemåterställning** väljer du **Återställ datorn med hjälp av en systemavbildning som du skapade tidigare**.

1. På sidan **Välj en säkerhetskopia av systemavbildningen** väljer du Välj en **systemavbildning** > **Avancerad** > **sökning efter en systemavbildning i nätverket**. Om en varning visas väljer du **Ja**. Gå till resurssökvägen, ange autentiseringsuppgifterna och välj sedan återställningspunkten. Systemet söker efter specifika säkerhetskopior som är tillgängliga i återställningspunkten. Välj den återställningspunkt som du vill använda.

1. På sidan **Välj hur du återställer säkerhetskopian** väljer du **Formatera och partitionerar om**. Kontrollera inställningarna på nästa sida.

1. Om du vill påbörja återställningen väljer du **Slutför**. En omstart krävs.

### <a name="restore-system-state"></a>Återställa systemtillståndet

Så här kör du återställning i Säkerhetskopieringsserver:

1. Leta reda **på** den dator som du vill återställa i återställningsfönstret och välj sedan Bare Metal **Recovery**.

1. Tillgängliga återställningspunkter anges i fetstil i kalendern. Välj datum och tid för den återställningspunkt som du vill använda.

1. På sidan **Välj återställningstyp** väljer du **Kopiera till en nätverksmapp**.

1. På sidan **Ange mål** väljer du var data ska kopieras. 

    Kom ihåg att målet du väljer måste ha tillräckligt med utrymme för data. Vi rekommenderar att du skapar en ny mapp för målet.

1. Välj säkerhetsinställningarna på sidan **Ange återställningsalternativ.** Välj sedan om du vill använda SAN-baserade maskinvaruögonblicksbilder för snabbare återställning. Det här alternativet är endast tillgängligt om: 
    * Du har ett SAN som tillhandahåller den här funktionen.
    * Du kan skapa och dela en klon för att göra den skrivbar. 
    * Den skyddade datorn och säkerhetskopieringsservern är anslutna till samma nätverk.

1. Ställ in meddelandealternativ. 
1. På sidan **Bekräftelse** väljer du **Återställ**.

Så här kör du Windows Server Backup:

1. Välj **Åtgärder** > **för att återställa** > **den här servern** > **nästa**.

1. Välj **En annan server,** välj sidan **Ange platstyp** och välj sedan **Delad fjärrmapp**. Ange sökvägen till mappen som innehåller återställningspunkten.

1. Välj **Systemtillstånd**på sidan **Välj återställningstyp** .

1. På sidan **Välj plats för systemtillståndsåterställning** väljer du **Ursprunglig plats**.

1. På sidan **Bekräftelse** väljer du **Återställ**. 

1. Efter återställningen startar du om servern.

Du kan också köra systemtillståndsåterställningen vid en kommandotolk: 

1. Starta Windows Server Backup på den dator som du vill återställa. 

1. Om du vill hämta versionsidentifer anger du i en kommandotolk:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Använd versionsidentifieraren för att starta systemtillståndsåterställningen. Skriv följande i kommandotolken: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Bekräfta att du vill starta återställningen. Du kan se processen i kommandotolksfönstret. En återställningslogg skapas. 

1. Efter återställningen startar du om servern.
