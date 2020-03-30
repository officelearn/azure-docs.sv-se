---
title: Säkerhetskopiera virtuella Hyper-V-datorer med MABS
description: Den här artikeln innehåller procedurer för säkerhetskopiering och återställning av virtuella datorer med Hjälp av Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 00d1dd04522c51e4d68450a7b8f25d7159d63724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255053"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Säkerhetskopiera virtuella Hyper-V-datorer med Azure Backup Server

I den hÃ¤r artikeln beskrivs hur du säkerhetskopierar virtuella Hyper-V-datorer med Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Scenarier som stöds

MABS kan säkerhetskopiera virtuella datorer som körs på Hyper-V-värdservrar i följande scenarier:

- **Virtuella datorer med lokal eller direkt lagring** – Säkerhetskopiera virtuella datorer som finns på fristående Hyper-V-värdservrar med lokal eller direktansluten lagring. Till exempel: en hårddisk, en SAN-enhet (Storage Area Network) eller en NAS-enhet (Network fastsatt lagring). MABS-skyddsagenten måste installeras på alla värdar.

- **Virtuella datorer som finns på ett kluster med CSV-lagring** – Säkerhetskopiera virtuella datorer som finns på ett Hyper-V-kluster med lagring på klusterdelade volymer (CSV). MABS-skyddsagenten installeras på varje klusternod.

## <a name="host-versus-guest-backup"></a>Värd kontra gäst säkerhetskopiering

MABS kan göra en värd- eller gästnivå backup av virtuella hyper-virtuella datorer. På värdnivå installeras MABS-skyddsagenten på Hyper-V-värdservern eller -klustret och skyddar hela virtuella datorer och datafiler som körs på den värden.   På gästnivå installeras agenten på varje virtuell dator och skyddar arbetsbelastningen som finns på den datorn.

Det för- och nackdelar med båda metoderna:

- Säkerhetskopior på värdnivå är flexibla eftersom de fungerar oavsett vilken typ av operativsystem som körs på gästdatorerna och inte kräver installation av MABS-skyddsagenten på varje virtuell dator. Om du distribuerar säkerhetskopia på värdnivå kan du återställa en hel virtuell dator eller filer och mappar (återställning på objektnivå).

- Säkerhetskopiering på gästnivå är användbart om du vill skydda specifika arbetsbelastningar som körs på en virtuell dator. På värdnivå kan du återställa en hel virtuell dator eller specifika filer, men det ger inte återställning i samband med ett visst program. Om du till exempel vill återställa specifika SharePoint-objekt från en säkerhetskopierad virtuell dator bör du göra säkerhetskopiering på gästnivå av den virtuella datorn. Använd säkerhetskopiering på gästnivå om du vill skydda data som lagras på genomströmningsdiskar. Genomströmning kan den virtuella datorn komma åt lagringsenheten direkt och lagrar inte virtuella volymdata i en VHD-fil.

## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhetskopieringsprocessen

MABS utför säkerhetskopiering med VSS enligt följande. Stegen i den här beskrivningen är numrerade för tydlighetens skull.

1. Den MABS-blockbaserade synkroniseringsmotorn gör en första kopia av den skyddade virtuella datorn och säkerställer att kopian av den virtuella datorn är fullständig och konsekvent.

2. När den första kopian har gjorts och verifierats använder MABS Hyper-V VSS-skrivaren för att fånga säkerhetskopior. VSS-skrivaren tillhandahåller en datakonsekvent uppsättning diskblock som synkroniseras med MABS-servern. Den här metoden ger fördelen av en "fullständig säkerhetskopiering" med MABS-servern, samtidigt som du minimerar säkerhetskopieringsdata som måste överföras över nätverket.

3. MABS-skyddsagenten på en server som kör Hyper-V använder befintliga Hyper-V-API:er för att avgöra om en skyddad virtuell dator också stöder VSS.

   - Om en virtuell dator uppfyller kraven för onlinesäkerhetskopiering och har komponenten för integrering av Hyper-V-tjänster installerad kommer Hyper-V VSS-skrivaren rekursivt att vidarebefordra VSS-begäran till alla VSS-medvetna processer på den virtuella datorn. Den här åtgärden utförs utan att MABS-skyddsagenten installeras på den virtuella datorn. Denna rekursiva VSS-begäran gör att Hyper-V VSS-skrivaren kan kontrollera att diskskrivningsåtgärderna synkroniseras så att en VSS-ögonblicksbild tas utan dataförlust.

     Komponenten för Hyper-V-integreringstjänsterna anropar Hyper-V VSS-skrivaren i VSS-tjänsterna (Volume Shadow Copy) på virtuella datorer för att säkerställa att deras programdata är i ett enhetligt tillstånd.

   - Om den virtuella datorn inte uppfyller kraven för säkerhetskopiering online använder MABS automatiskt Hyper-V-API:erna för att pausa den virtuella datorn innan de samlar in datafiler.

4. När den första originalvarian för den virtuella datorn synkroniseras med MABS-servern, fångas alla ändringar som görs i resurser för den virtuella datorn i en ny återställningspunkt. Återställningspunkten representerar det konsekventa tillståndet hos den virtuella datorn vid en viss tidpunkt. Registreringar av återställningspunkter kan ske minst en gång per dag. När en ny återställningspunkt skapas använder MABS replikering på blocknivå tillsammans med Hyper-V VSS-skrivaren för att avgöra vilka block som har ändrats på servern som kör Hyper-V efter att den senaste återställningspunkten skapades. Dessa datablock överförs sedan till MABS-servern och tillämpas på repliken av skyddade data.

5. MABS-servern använder VSS på de volymer som är värd för återställningsdata så att flera skuggkopior är tillgängliga. Var och en av dessa skuggkopior möjliggör separat återställning. VSS-återställningspunkter lagras på MABS-servern. Den tillfälliga kopian som görs på servern som kör Hyper-V lagras endast under hela MABS-synkroniseringen.

>[!NOTE]
>
>Från och med Windows Server 2016 har Hyper-V virtuella hårddiskar inbyggd ändringsspårning som kallas motståndskraftig ändringsspårning (RCT). MABS använder RCT (den inbyggda ändringsspårningen i Hyper-V), vilket minskar behovet av tidskrävande konsekvenskontroller i scenarier som vm-krascher. RCT ger bättre flexibilitet än den ändringsspårning som tillhandahålls av säkerhetskopiering med VSS-ögonblicksbild. MABS V3 optimerar nätverks- och lagringsförbrukningen ytterligare genom att endast överföra ändrade data under eventuella konsekvenskontroller.

## <a name="backup-prerequisites"></a>Förutsättningar för säkerhetskopiering

Dessa är förutsättningarna för säkerhetskopiering av virtuella Hyper-V-datorer med MABS:

|Krav|Information|
|------------|-------|
|MABS förutsättningar|- Om du vill utföra återställning på objektnivå för virtuella datorer (återställning av filer, mappar, volymer) måste du installera Hyper-V-rollen på MABS-servern.  Om du bara vill återställa den virtuella datorn och inte på objektnivå krävs inte rollen.<br />- Du kan skydda upp till 800 virtuella datorer på 100 GB vardera på en MABS-server och tillåta flera MABS-servrar som stöder större kluster.<br />- MABS utesluter sidfilen från inkrementella säkerhetskopior för att förbättra prestanda för säkerhetskopiering av virtuella datorer.<br />- MABS kan säkerhetskopiera en Hyper-V-server eller ett kluster i samma domän som MABS-servern eller i en underordnad eller betrodd domän. Om du vill säkerhetskopiera Hyper-V i en arbetsgrupp eller en domän som inte har anförtrotts måste du konfigurera autentisering. För en enda Hyper-V-server kan du använda NTLM- eller certifikatautentisering. För ett kluster kan du bara använda certifikatautentisering.<br />-   Du kan inte använda säkerhetskopiering på värdnivå för att säkerhetskopiera data på virtuella datorer på direktlagringsdiskar. I det här fallet rekommenderar vi att du använder säkerhetskopiering på värdnivå för att säkerhetskopiera VHD-filer och säkerhetskopiering på gästnivå för att säkerhetskopiera andra data som inte visas på värden.<br />   -Du kan säkerhetskopiera virtuella datorer som lagras på deduplicated volymer.|
|Förutsättningar för Hyper-V VM|- Den version av integrationskomponenter som körs på den virtuella datorn ska vara samma som versionen av Hyper-V-värden. <br />-   För varje säkerhetskopiering av virtuella datorer måste du frigöra utrymme på volymen där VHD-filerna finns så att Hyper-V har tillräckligt med utrymme för differentieringsdiskar (AVHD:er) under säkerhetskopieringen. Utrymmet måste minst vara lika med beräkningen **ursprunglig hårddiskstorlek\* omsättningshastighet tidslucka för \*säkerhetskopiering**. Om du använder flera säkerhetskopieringar i ett kluster behöver du tillräckligt mycket lagringskapacitet för att få plats med AVHD:erna för var och en av de virtuella datorerna baserat på den här beräkningen.<br />- Om du vill säkerhetskopiera virtuella datorer som finns på Hyper-V-värdservrar som kör Windows Server 2012 R2 bör den virtuella datorn ha en SCSI-styrenhet angiven, även om den inte är ansluten till någonting. (I windows server 2012 R2 online backup monterar Hyper-V-värden en ny virtuell hårddisk i den virtuella datorn och demonterar den senare. Endast SCSI-styrenheten kan stödja detta och krävs därför för säkerhetskopiering online av den virtuella datorn.  Utan den här inställningen utfärdas händelse-ID 10103 när du försöker säkerhetskopiera den virtuella datorn.)|
|Förutsättningar för Linux|- Du kan säkerhetskopiera Virtuella Linux-datorer med HJÄLP AV MABS. Endast filkonsekventa ögonblicksbilder stöds.|
|Säkerhetskopiera virtuella datorer med CSV-lagring|-   För CSV-lagring installerar du maskinvaruprovidern Volume Shadow Copy Services (VSS) på Hyper-V-servern. Kontakta din SAN-leverantör (Storage Area Network) för VSS-maskinvaruprovidern.<br />- Om en enda nod stängs av oväntat i ett CSV-kluster, utför MABS en konsekvenskontroll mot de virtuella datorer som kördes på noden.<br />-   Om du behöver starta om en Hyper-V-server som har BitLocker-diskkryptering aktiverat för CSV-klustret måste du utföra en konsekvenskontroll för virtuella Hyper-V-datorer.|
|Säkerhetskopiera virtuella datorer med SMB-lagring|-   Aktivera automatisk montering på servern där Hyper-V körs så att skyddet av virtuella datorer aktiveras.<br />   -   Inaktivera TCP Chimney-avlastning.<br />-   Kontrollera att alla Hyper-V:s machine$-konton har fullständig behörighet till de aktuella SMB-filresurserna.<br />- Se till att filsökvägen för alla komponenter till virtuella datorer under återställning till alternativ plats är färre än 260 tecken. Om inte, kan återställningen lyckas, men Hyper-V kan inte montera den virtuella datorn.<br />- Följande scenarier stöds inte:<br />     Distributioner där vissa komponenter i den virtuella datorn finns på lokala volymer och vissa komponenter finns på fjärrvolymer. en IPv4- eller IPv6-adress för lagringsplatsfilserver och återställning av en virtuell dator till en dator som använder fjärr-SMB-resurser.<br />- Du måste aktivera tjänsten File Server VSS Agent på varje SMB-server - Lägg till den i **Lägg till roller och funktioner** > Välj**serverroller** > **Fil- och lagringstjänster** > **File Services** > **Filtjänstfilserver** > **VSS-agenttjänsten**.|

## <a name="back-up-virtual-machines"></a>Säkerhetskopiera virtuella datorer

1. Konfigurera [MABS-servern](backup-azure-microsoft-azure-backup.md) och [ditt lagringsutrymme](backup-mabs-add-storage.md). Använd följande riktlinjer för lagringskapacitet när du konfigurerar lagringen.
   - Genomsnittlig storlek för virtuell dator – 100 GB
   - Antal virtuella datorer per MABS-server - 800
   - Total storlek på 800 virtuella datorer – 80 TB
   - Utrymme som krävs för lagring av säkerhetskopior – 80 TB

2. Konfigurera MABS-skyddsagenten på Hyper-V-servern eller Hyper-V-klusternoderna. Om du säkerhetskopierar gästnivå installerar du agenten på de virtuella datorer som du vill säkerhetskopiera på gästnivå.

3. Öppna > **guiden** Skapa nytt skydd **grupp** **i**MABS-administratörskonsolen.

4. På sidan **Välj gruppmedlemmar** väljer du de virtuella datorer som du vill skydda (från de Hyper-V-värdservrar som de virtuella datorerna finns på). Vi rekommenderar att du placerar alla virtuella datorer som har samma skyddsprincip i samma skyddsgrupp. Du får en effektivare användning av diskutrymmet om du aktiverar samplacering. Med samplacering kan du hitta data i olika skyddsgrupper på samma disk- eller bandlagring. Flera datakällor får då en enda replik och återställningspunktvolym.

5. På sidan **Välj dataskyddsmetod** anger du ett skyddsgruppnamn. Välj **Jag vill ha kortvarigt skydd med disk** och välj **Jag vill ha ett onlineskydd** om du vill säkerhetskopiera data till Azure med tjänsten Azure Backup.

6. I **Ange korttidsmål** > **kvarhållningsintervall**anger du hur länge du vill behålla diskdata. I **Synkroniseringsfrekvens**anger du hur ofta inkrementella säkerhetskopior av data ska köras. Du kan också välja att aktivera **Precis innan en återställningspunkt** i stället för att välja ett intervall för inkrementell säkerhetskopiering. Med den här inställningen aktiverad kör MABS en express fullständig säkerhetskopiering strax före varje schemalagd återställningspunkt.

    > [!NOTE]
    >
    >Om du skyddar programarbetsbelastningarna skapas återställningspunkter i enlighet med synkroniseringsfrekvensen under förutsättning att programmet har stöd för inkrementella säkerhetskopior. Om den inte gör det kör MABS en snabb fullständig säkerhetskopiering i stället för en inkrementell säkerhetskopiering och skapar återställningspunkter i enlighet med schemat för snabb säkerhetskopiering.

7. På sidan **Granska diskallokering** granskar du det lagringsutrymme för lagringspool som tilldelats för skyddsgruppen.

   **Total datastorlek** är storleken på de data som du vill säkerhetskopiera och **diskutrymme som ska etableras på MABS** är det utrymme som MABS rekommenderar för skyddsgruppen. MABS väljer den perfekta säkerhetskopieringsvolymen, baserat på inställningarna. Du kan dock redigera valen av säkerhetskopieringsvolym under **Disk allocation details (Diskallokeringsdetaljer)**. Välj önskad lagringsplats för arbetsbelastningarna i den nedrullningsbara menyn. Redigeringarna ändrar värdena för **Totalt lagringsutrymme** och **Ledigt lagringsutrymme** i fönstret **Tillgängligt disklagringsutrymme**. Underetablerat utrymme är mängden lagringsutrymme MABS föreslår att du lägger till volymen, för att fortsätta med säkerhetskopior smidigt i framtiden.

8. På sidan **Välj metod för skapande av replik** anger du hur den första replikeringen av data i skyddsgruppen ska utföras. Om du väljer att **replikera automatiskt över nätverket**rekommenderar vi att du väljer en lågtrafiktid. För stora mängder data eller mindre än optimala nätverksvillkor bör du överväga att välja **Manuellt**, vilket kräver att data replikeras offline med hjälp av flyttbara media.

9. På sidan **Alternativ för konsekvenskontroll** väljer du hur du vill automatisera konsekvenskontroller. Du kan aktivera att en kontroll endast körs när replikdata blir inkonsekvent, eller enligt ett schema. Om du inte vill konfigurera automatiska konsekvenskontroller kan du när som helst köra en manuell kontroll genom att högerklicka på skyddsgruppen och välja **Utför konsekvenskontroll**.

    När du har skapat skyddsgruppen utförs inledande replikering av data i enlighet med den metod som du har valt. Efter den inledande replikeringen utförs varje säkerhetskopiering i enlighet med inställningarna för skyddsgruppen. Om du behöver återställa säkerhetskopierade data bör du tänka på följande:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Säkerhetskopiera virtuella datorer som är konfigurerade för direktmigrering

När virtuella datorer är involverade i livemigrering fortsätter MABS att skydda de virtuella datorerna så länge MABS-skyddsagenten är installerad på Hyper-V-värden. Hur MABS skyddar de virtuella datorerna beror på vilken typ av livemigrering det innebär.

**Live migrering inom ett kluster** - När en virtuell dator migreras inom ett kluster MABS identifierar migreringen och säkerhetskopierar den virtuella datorn från den nya klusternoden utan några krav på användarintervention. Eftersom lagringsplatsen inte har ändrats fortsätter MABS med fullständiga säkerhetskopior.

**Livemigrering utanför klustret** – När en virtuell dator migreras mellan fristående servrar, olika kluster eller mellan en fristående server och ett kluster identifierar MABS migreringen och kan säkerhetskopiera den virtuella datorn utan att användaren behöver ingripa.

### <a name="requirements-for-maintaining-protection"></a>Krav för att bevara skyddet

Följande är krav för att bevara skyddet under direktmigrering:

- Hyper-V-värdarna för de virtuella datorerna måste finnas i ett VMM-moln i System Center, på en VMM-server som kör minst System Center 2012 med SP1.

- MABS-skyddsagenten måste vara installerad på alla Hyper-V-värdar.

- MABS-servrar måste vara anslutna till VMM-servern. Alla Hyper-V-värdservrar i VMM-molnet måste också vara anslutna till MABS-servrarna. Detta gör att MABS att kommunicera med VMM-servern så MABS kan ta reda på på vilken Hyper-V-värdserver den virtuella datorn körs för närvarande och skapa en ny säkerhetskopia från den Hyper-V-servern. Om en anslutning inte kan upprättas till Hyper-V-servern misslyckas säkerhetskopieringen med ett meddelande om att MABS-skyddsagenten inte kan nås.

- Alla MABS-servrar, VMM-servrar och Hyper-V-värdservrar måste finnas i samma domän.

### <a name="details-about-live-migration"></a>Information om direktmigrering

Observera följande om säkerhetskopiering under direktmigrering:

- Om en livemigrering överför lagring utför MABS en fullständig konsekvenskontroll av den virtuella datorn och fortsätter sedan med fullständiga snabbsäkerhetskopior. När direktmigrering av lagring sker omorganiserar Hyper-V den virtuella hårddisken (VHD) eller VHDX, vilket medför en engångsökning i storleken på MABS-säkerhetskopieringsdata.

- Aktivera automatisk montering på den virtuella datorvärden om du vill aktivera virtuellt skydd, och inaktivera TCP Chimney-avlastning.

- MABS använder port 6070 som standardport för att vara värd för DPM-VMM Helper Service. Så här ändrar du i registret:

    1. Navigera till **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Skapa ett 32-bitars DWORD-värde: DpmVmmHelperServicePort, och skriv det uppdaterade portnumret som en del av registernyckeln.
    3. Öppna ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```, och ändra portnumret från 6070 till den nya porten. Exempel: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Starta om DPM-VMM Helper Service och starta om DPM-tjänsten.

### <a name="set-up-protection-for-live-migration"></a>Konfigurera skydd vid direktmigrering

Så här konfigurerar du skydd vid direktmigrering:

1. Konfigurera MABS-servern och dess lagring och installera MABS-skyddsagenten på varje Hyper-V-värdserver eller klusternod i VMM-molnet. Om du använder SMB-lagring i ett kluster installerar du MABS-skyddsagenten på alla klusternoder.

2. Installera VMM-konsolen som en klientkomponent på MABS-servern så att MABS kan kommunicera med VMM-servern. Konsolen ska vara av samma version som den som körs på VMM-servern.

3. Tilldela KONTOT MABSMachineName$ som ett skrivskyddat administratörskonto på VMM-hanteringsservern.

4. Anslut alla Hyper-V-värdservrar till alla `Set-DPMGlobalProperty` MABS-servrar med PowerShell-cmdleten. Cmdlet accepterar flera MABS-servernamn. Använd formatet `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Mer information finns i [Set-DPMGlobalProperty](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmglobalproperty?view=systemcenter-ps-2019).

5. När alla virtuella datorer som körs på Hyper-V-värdarna i VMM-molnet har identifierats i VMM skapar du en skyddsgrupp och lägger till de virtuella datorer som du vill skydda. Automatiska konsekvenskontroller bör aktiveras på skyddsgruppsnivå för skydd under mobilitetsscenarier för virtuella datorer.

6. När inställningarna har konfigurerats fortsätter alla säkerhetskopior som förväntat när en virtuell dator migrerar från ett kluster till ett annat. Så här gör du för att kontrollera att direktmigreringen aktiveras som väntat:

   1. Kontrollera att DPM-VMM Helper Service körs. Om det inte är det, starta den.

   2. Öppna Microsoft SQL Server Management Studio och anslut till den instans som är värd för MABS-databasen (DPMDB). Kör följande fråga på DPMDB:`SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Den här frågan innehåller `KnownVMMServer`en egenskap som heter . Detta värde bör vara detsamma som värdet som du angav med cmdletten `Set-DPMGlobalProperty`.

   3. Kör följande fråga för att verifiera parametern *VMMIdentifier* i `PhysicalPathXML` för en specifik virtuell dator. Ersätt `VMName` med den virtuella datorns namn.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Öppna xml-filen som den här frågan returnerar och kontrollera att fältet *VMMIdentifier* har ett värde.

### <a name="run-manual-migration"></a>Köra manuell migrering

När du har slutfört stegen i föregående avsnitt och jobbet MABS Summary Manager har slutförts aktiveras migreringen. Detta jobb startas normalt vid midnatt och körs varje morgon. Gör så här om du vill köra en manuell migrering för att kontrollera att allt fungerar som förväntat:

1. Öppna SQL Server Management Studio och anslut till den instans som är värd för MABS-databasen.

2. Kör följande fråga: `SELECT SCH.ScheduleId FROM tbl_JM_JobDefinition JD JOIN tbl_SCH_ScheduleDefinition SCH ON JD.JobDefinitionId = SCH.JobDefinitionId WHERE JD.Type = '282faac6-e3cb-4015-8c6d-4276fcca11d4' AND JD.IsDeleted = 0 AND SCH.IsDeleted = 0`. Frågan returnerar **ScheduleID**. Skriv upp detta ID eftersom du kommer att använda det i nästa steg.

3. Utöka **SQL Server Agent** i SQL Server Management Studio och utöka sedan **Jobs (Jobb)**. Högerklicka på det **ScheduleID (Schema-ID) **som du har antecknat och välj **Start Job at Step (Starta jobb vid steg)**.

Säkerhetskopieringsprestanda påverkas när jobbet körs. Distributionens storlek och skala avgör hur lång tid jobbet tar.

## <a name="back-up-replica-virtual-machines"></a>Säkerhetskopiera virtuella replikdatorer

Om MABS körs på Windows Server 2012 R2 eller senare kan du säkerhetskopiera virtuella replikdatorer. Detta är användbart av flera skäl:

**Det minskar säkerhetskopieringens inverkan på arbetsbelastningen som körs** – När du gör en säkerhetskopia av en virtuell dator krävs vissa extra resurser när en ögonblicksbild skapas. Genom att avlasta säkerhetskopieringsprocessen till en sekundär fjärrplats påverkas den arbetsbelastning som körs inte längre av säkerhetskopieringen. Detta gäller endast för distributioner där säkerhetskopian lagras på en fjärrplats. Du kan till exempel göra dagliga säkerhetskopior och lagra data lokalt för att säkerställa snabba återställningstider, och samtidigt göra månadsvisa eller kvartalsvisa säkerhetskopior från virtuella replikdatorer som fjärrlagras för långsiktig kvarhållning.

**Sparar bandbredd** – I en typisk distribution för fjärranslutna avdelningskontor/huvudkontor behöver du en lämplig mängd etablerad bandbredd för att överföra säkerhetskopieringsdata mellan olika platser. Om du skapar en replikerings- och redundansstrategi utöver din säkerhetskopieringsstrategi kan du minska mängden redundanta data som skickas över nätverket. Genom att säkerhetskopiera replikdata för virtuella datorer i stället för den primära sparar du omkostnaderna för att skicka säkerhetskopierade data över nätverket.

**Aktiverar värdleverantörens säkerhetskopiering** – Du kan använda ett värdbaserat datacenter som en replikplats utan behov av ett sekundärt datacenter. I det här fallet kräver hoster SLA konsekvent säkerhetskopiering av virtuella datorer för repliker.

En virtuell replikdator är inaktiverad till dess att redundansväxling initieras, och VSS kan inte garantera en programkonsekvent säkerhetskopiering för en virtuell replikdator. Därför kommer säkerhetskopieringen av en virtuell replikdator endast att vara kraschkonsekvent. Om kraschkonsekvens inte kan garanteras misslyckas säkerhetskopieringen. Detta kan inträffa i ett antal situationer:

- Den virtuella replikdatorn är inte felfri och är i ett kritiskt tillstånd.

- Den virtuella replikdatorn omsynkroniseras (den är i något av tillstånden Omsynkronisering pågår eller Omsynkronisering krävs).

- Den första replikeringen mellan den primära och den sekundära platsen pågår eller väntar på den virtuella datorn.

- HRL-loggar tillämpas på den virtuella datorn för repliken eller en tidigare åtgärd för att tillämpa HRL-loggarna på den virtuella disken misslyckades eller avbröts eller avbröts.

- Migrering eller redundans av den virtuella replikdatorn pågår

## <a name="recover-backed-up-virtual-machines"></a>Återställa säkerhetskopierade virtuella datorer

När du kan återställa en säkerhetskopierade virtuell dator använder du guiden Återställning för att välja den virtuella datorn och specifik återställningspunkt. Så här öppnar du guiden Återställning och återställer en virtuell dator:

1. Skriv namnet på den virtuella datorn i MABS-administratörskonsolen eller expandera listan över skyddade objekt och välj den virtuella dator som du vill återställa.

2. Gå till kalendern i fönstret **Återställningspunkter för** och klicka på ett datum om du vill se vilka återställningspunkter som är tillgängliga. Välj den återställningspunkt som du vill använda på menyn **Sökväg** i återställningsguiden.

3. Klicka på menyn **Återställ** i fönstret **Åtgärder** så öppnas återställningsguiden.

    Den virtuella dator och återställningspunkt som du har valt visas på skärmen **Granska val av återställning**. Klicka på **Nästa**.

4. På skärmen **Välj typ av återställning** väljer du var du vill återställa data och klickar sedan på **Nästa**.

    - **Återställ till ursprunglig instans**: När du återställer till den ursprungliga instansen tas den ursprungliga virtuella hårddisken bort. MABS återställer den virtuella hårddisken och andra konfigurationsfiler till den ursprungliga platsen med Hyper-V VSS-brännare. I slutet av återställningsprocessen är de virtuella datorerna fortfarande högtillgängliga.
        Resursgruppen måste vara tillgänglig för återställning. Om den inte är tillgänglig återställer du till en annan plats och gör sedan den virtuella datorn högtillgänglig.

    - **Återställ som virtuell dator till alla värdar:** MABS stöder alternativ platsåterställning (ALR), vilket ger en sömlös återställning av en skyddad Hyper-V virtuell dator till en annan Hyper-V-värd, oberoende av processorarkitektur. Virtuella Hyper-V-datorer som återställs till en klusternod blir inte högtillgängliga. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.

    - **Kopiera till en nätverksmapp:** MABS stöder återställning på objektnivå (ILR), vilket gör att du kan göra återställning på objektnivå av filer, mappar, volymer och virtuella hårddiskar (VHD) från en säkerhetskopia på värdnivå av Virtuella Hyper-V-datorer till en nätverksresurs eller en volym på en MABS-skyddad server. MABS-skyddsagenten behöver inte installeras i gästen för att utföra återställning på objektnivå. Om du väljer det här alternativet visas ytterligare en skärm i Återställningsguiden där du kan identifiera mål och målsökväg.

5. Under **Ange återställningsalternativ** konfigurerar du återställningsalternativen och klickar på **Nästa**:

    - Om du återställer en virtuell dator via låg bandbredd klickar du på **Ändra** om du vill aktivera **Begränsning av nätverksbandbredd**. Du kan ange hur mycket bandbredd som du vill göra tillgänglig och den tidpunkt då bandbredd är tillgänglig när du har aktiverat alternativet för begränsning.
    - Välj **Aktivera SAN-baserad återställning med hjälp av ögonblicksbilder av maskinvara** om du har konfigurerat nätverket.
    - Välj **Send an e-mail when the recovery complete (Skicka ett e-postmeddelande när återställningen har slutförts)** och ange de e-postadresserna om du vill att e-postmeddelandena ska skickas när återställningen är klar.

6. Kontrollera att alla uppgifter i fönstret Sammanfattning är korrekta. Om uppgifterna inte är korrekta, eller om du vill göra en ändring, klickar du på **Tillbaka**. Om du är nöjd med inställningarna klickar du på **Återställ** så startar återställningsprocessen.

7. Skärmen **Återställningsstatus** innehåller information om återställningsjobbet.

## <a name="next-steps"></a>Nästa steg

[Återställa data från Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
