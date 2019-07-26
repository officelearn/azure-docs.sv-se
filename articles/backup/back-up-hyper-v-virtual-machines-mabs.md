---
title: Säkerhetskopiera virtuella Hyper-V-datorer med Azure Backup Server
description: Den här artikeln innehåller procedurer för att säkerhetskopiera och återställa virtuella datorer med hjälp av Microsoft Azure Backup Server.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: dacurwin
ms.openlocfilehash: 46d9f33dedff5a5682385b9cb22cf310581eefde
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466847"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Säkerhetskopiera virtuella Hyper-V-datorer med Azure Backup Server

Den här artikeln förklarar hur du säkerhetskopierar virtuella Hyper-V-datorer med hjälp av Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Scenarier som stöds
MABS kan säkerhetskopiera virtuella datorer som körs på Hyper-V-värd servrar i följande scenarier:

-   **Virtuella datorer med lokal eller direkt lagring** – säkerhetskopiera virtuella datorer som finns på fristående Hyper-V-värdar som har lokal eller direktansluten lagring. Till exempel: en hård disk, en storage area network-enhet (SAN) eller en nätverksansluten lagrings enhet (NAS). MABS-skyddsagenten måste vara installerad på alla värdar.

-   **Virtuella datorer i ett kluster med CSV-lagring** – säkerhetskopiera virtuella datorer som finns på ett Hyper-V-kluster med klusterdelad volym lagring (CSV). MABS-skyddsagenten installeras på varje klusternod.



## <a name="host-versus-guest-backup"></a>Värd kontra gäst säkerhets kopiering
MABS kan göra en säkerhets kopia av virtuella Hyper-V-datorer på värd-eller gästnivå. På värdnivå är MABS-skyddsagenten installerad på Hyper-V-värdservern eller klustret och skyddar alla virtuella datorer och datafiler som körs på värden.   På gästnivå är agenten installerad på varje virtuell dator och skyddar arbets belastningen på den datorn.

Båda metoderna har-och nack delar:

-   Säkerhets kopiering på värdnivå är flexibla eftersom de fungerar oavsett vilken typ av operativ system som körs på gäst datorerna och inte kräver installation av MABS-skyddsagenten på varje virtuell dator. Om du distribuerar säkerhets kopiering på värdnivå kan du återställa en hel virtuell dator eller filer och mappar (återställning på objekt nivå).

-   Säkerhets kopiering på gästnivå är användbart om du vill skydda vissa arbets belastningar som körs på en virtuell dator. På värdnivå kan du återställa en hel virtuell dator eller specifika filer, men den ger inte återställnings kontexten för ett särskilt program. Om du till exempel vill återställa vissa SharePoint-objekt från en säkerhets kopie rad virtuell dator bör du göra en säkerhets kopia av den virtuella datorn på gästnivå. Använd säkerhets kopiering på gästnivå om du vill skydda data som lagras på genom strömnings diskar. Genom strömning får den virtuella datorn direkt åtkomst till lagrings enheten och lagrar inte virtuella volym data i en VHD-fil.



## <a name="how-the-backup-process-works"></a>Så här fungerar säkerhets kopieringen
MABS utför säkerhets kopiering med VSS på följande sätt. Stegen i den här beskrivningen är numrerade för att hjälpa dig med klarhet.

1. MABS-block-based Synchronization-motorn gör en ursprunglig kopia av den skyddade virtuella datorn och ser till att kopian av den virtuella datorn är fullständig och konsekvent.

2. När den första kopian har gjorts och verifierats använder MABS Hyper-V VSS-skrivaren för att samla in säkerhets kopior. VSS-skrivaren tillhandahåller en data enhetlig uppsättning disk block som är synkroniserade med MABS-servern. Den här metoden ger fördelen med en fullständig säkerhets kopiering med MABS-servern och minimerar de säkerhets kopierings data som måste överföras i nätverket.

3. MABS-skyddsagenten på en server där Hyper-V körs använder de befintliga Hyper-V API: erna för att avgöra om en skyddad virtuell dator också har stöd för VSS.

   - Om en virtuell dator uppfyller kraven för onlinesäkerhetskopiering och har komponenten för Hyper-V integrations tjänster installerad, vidarebefordrar Hyper-V VSS-skrivaren rekursivt VSS-begäran till alla VSS-medvetna processer på den virtuella datorn. Den här åtgärden utförs utan att MABS-skyddsagenten installeras på den virtuella datorn. Den rekursiva VSS-begäran gör att Hyper-V VSS-skrivaren kan säkerställa att disk skrivnings åtgärder synkroniseras så att en VSS-ögonblicksbild samlas in utan data förlust.

     Komponenten för integrerings tjänster för Hyper-V anropar Hyper-V VSS-skrivaren i Volume Shadow Copy Services (VSS) på virtuella datorer för att säkerställa att deras program data är i ett konsekvent tillstånd.

   - Om den virtuella datorn inte följer säkerhets kopierings kraven online använder MABS automatiskt Hyper-V-API: erna för att pausa den virtuella datorn innan de fångar in datafiler.

4. När den ursprungliga bas linje kopian av den virtuella datorn har synkroniserats med MABS-servern, fångas alla ändringar som görs i de virtuella dator resurserna på en ny återställnings punkt. Återställnings punkten representerar det konsekventa läget för den virtuella datorn vid en viss tidpunkt. Avbildningar av återställnings punkter kan ske minst en gång per dag. När en ny återställnings punkt skapas använder MABS block nivå replikering tillsammans med Hyper-V VSS-skrivaren för att avgöra vilka block som har ändrats på den server där Hyper-V körs när den sista återställnings punkten skapades. Dessa data block överförs sedan till MABS-servern och tillämpas på repliken av skyddade data.

5. MABS-servern använder VSS på de volymer som är värdar för återställnings data så att flera skugg kopior är tillgängliga. Var och en av dessa skugg kopior ger en separat återställning. VSS-återställnings punkter lagras på MABS-servern. Den tillfälliga kopia som görs på servern som kör Hyper-V lagras bara under hela MABS-synkroniseringen.

>[!NOTE]
>
>Från och med Windows Server 2016 har Hyper-V-virtuella hård diskar inbyggd ändrings spårning som kallas elastisk ändrings spårning (RCT). MABS använder RCT (den interna ändrings spårningen i Hyper-V), vilket minskar behovet av konsekvens kontroller i scenarier som VM kraschar. RCT ger bättre återhämtning än ändrings spårningen som tillhandahålls av VSS Snapshot-baserade säkerhets kopieringar. MABS v3 optimerar användningen av nätverks-och lagrings utrymme ytterligare genom att endast överföra ändrade data under en konsekvens kontroll.


## <a name="backup-prerequisites"></a>Krav för säkerhets kopiering
Detta är kraven för att säkerhetskopiera virtuella Hyper-V-datorer med MABS:

|Krav|Information|
|------------|-------|
|MABS-krav|– Om du vill utföra återställning på objekt nivå för virtuella datorer (återställa filer, mappar, volymer) måste du installera Hyper-V-rollen på MABS-servern.  Om du bara vill återställa den virtuella datorn och inte på objekt nivå så krävs inte rollen.<br />-Du kan skydda upp till 800 virtuella datorer på 100 GB vardera på en MABS-Server och tillåta flera MABS-servrar som stöder större kluster.<br />-MABS utesluter växlings filen från stegvisa säkerhets kopieringar för att förbättra prestanda för säkerhets kopiering av virtuella datorer.<br />-MABS kan säkerhetskopiera en Hyper-V-server eller ett kluster i samma domän som MABS-servern eller i en underordnad eller betrodd domän. Om du vill säkerhetskopiera Hyper-V i en arbets grupp eller en ej betrodd domän måste du konfigurera autentisering. Du kan använda NTLM eller certifikatautentisering för en enskild Hyper-V-server. Du kan endast använda certifikatautentisering för ett kluster.<br />-Att använda säkerhets kopiering på värdnivå för att säkerhetskopiera data för virtuella datorer på passthrough-diskar stöds inte. I det här scenariot rekommenderar vi att du använder säkerhets kopiering på värdnivå för att säkerhetskopiera VHD-filer och säkerhets kopiering på gästnivå för att säkerhetskopiera andra data som inte är synliga på värden.<br />   -Du kan säkerhetskopiera virtuella datorer som lagras på deduplicerade volymer.|
|Krav för virtuell Hyper-V-dator|-Versionen av integrations komponenterna som körs på den virtuella datorn måste vara samma som versionen av Hyper-V-värden. <br />– För varje säkerhets kopiering av virtuella datorer behöver du ledigt utrymme på volymen som är värd för de virtuella hård diskarna för att tillåta att Hyper-V är tillräckligt med utrymme för differentierings diskar (AVHD) under säkerhets kopieringen. Utrymmet måste minst vara lika med beräkningen **Ursprunglig disk storlek\*\*omsättnings tid för säkerhets kopierings** fönstret. Om du kör flera säkerhets kopieringar i ett kluster behöver du tillräckligt med lagrings kapacitet för att hantera AVHD: erna för var och en av de virtuella datorerna som använder den här beräkningen.<br />– Om du vill säkerhetskopiera virtuella datorer som finns på Hyper-V-värd servrar som kör Windows Server 2012 R2, måste den virtuella datorn ha en angiven SCSI-styrenhet, även om den inte är ansluten till något. (I Windows Server 2012 R2 online backup monterar Hyper-V-värden en ny virtuell hård disk i den virtuella datorn och demonterar sedan den senare. Endast SCSI-styrenheten stöder detta och krävs därför för onlinesäkerhetskopiering av den virtuella datorn.  Utan den här inställningen kommer händelse-ID 10103 att utfärdas när du försöker säkerhetskopiera den virtuella datorn.)|
|Förutsättningar för Linux|– Du kan säkerhetskopiera virtuella Linux-datorer med MABS 2012 R2. Endast filkonsekventa ögonblicks bilder stöds.|
|Säkerhetskopiera virtuella datorer med CSV-lagring|– För CSV-lagring installerar du VSS-maskinvaruprovidern (Volume Shadow Copy Services) på Hyper-V-servern. Kontakta din storage area network-leverantör (SAN) för VSS-maskinvaruprovidern.<br />– Om en enda nod stängs av utan förvarning i ett CSV-kluster utför MABS en konsekvens kontroll mot de virtuella datorer som kördes på noden.<br />– Om du behöver starta om en Hyper-V-server som har BitLocker-diskkryptering aktiverat i CSV-klustret måste du köra en konsekvens kontroll för virtuella Hyper-V-datorer.|
|Säkerhetskopiera virtuella datorer med SMB-lagring|– Aktivera automatisk montering på den server som kör Hyper-V för att aktivera skydd av virtuella datorer.<br />   -Inaktivera TCP Chimney-avlastning.<br />– Kontrol lera att alla Hyper-V Machine $-konton har fullständig behörighet för de enskilda SMB-filresurserna.<br />– Kontrol lera att fil Sök vägen för alla virtuella dator komponenter under återställningen till den alternativa platsen är färre än 260 tecken. Annars kan återställningen lyckas, men Hyper-V kan inte montera den virtuella datorn.<br />-Följande scenarier stöds inte:<br />     Distributioner där vissa komponenter i den virtuella datorn finns på lokala volymer och vissa komponenter finns på fjärrvolymer. en IPv4-eller IPv6-adress för lagrings platsens fil server och återställning av en virtuell dator till en dator som använder SMB-fjärrresurser.<br />-Du måste aktivera VSS-agenttjänsten för fil server på varje SMB-server – Lägg till den i **Lägg till roller och funktioner** > **Välj Server roller** > **fil-och lagrings tjänster** > **fil tjänster**  >  **Fil tjänst** **VSS-agenttjänsten för fil server.**  > |

## <a name="back-up-virtual-machines"></a>Säkerhetskopiera virtuella datorer

1. Konfigurera din [Mabs-Server](backup-azure-microsoft-azure-backup.md) och [lagrings utrymme](backup-mabs-add-storage.md). Använd dessa rikt linjer för lagrings kapacitet när du konfigurerar lagringen.
   - Genomsnittlig storlek på virtuell dator – 100 GB
   - Antal virtuella datorer per MABS-Server – 800
   - Total storlek på 800 VM-80 TB
   - Nödvändigt utrymme för säkerhets kopierings lagring – 80 TB

2. Konfigurera skydds agenten för MABS på Hyper-V-servern eller Hyper-V-klusternoderna. Om du utför säkerhets kopiering på gästnivå installerar du agenten på de virtuella datorer som du vill säkerhetskopiera på gästnivå.

3. I administratörs konsolen för Mabs klickar du på **skydd** > **skapa skydds grupp** för att öppna guiden **Skapa ny skydds grupp** .

4. På sidan **Välj grupp medlemmar** väljer du de virtuella datorer som du vill skydda från de Hyper-V-värd servrar som de finns på. Vi rekommenderar att du sätter alla virtuella datorer som ska ha samma skydds princip i en skydds grupp. Aktivera samplacering för att få effektiv användning av utrymme. Med samplacering kan du hitta data från olika skydds grupper på samma disk-eller band lagring, så att flera data källor har en enda replik och återställnings punkt volym.

5. På sidan **Välj data skydds metod** anger du ett skydds grupp namn. Välj **Jag vill ha kortvarigt skydd med disk** och välj **Jag vill ha onlineskydd** om du vill säkerhetskopiera data till Azure med hjälp av tjänsten Azure Backup. 


6. I **Ange kortsiktiga mål** > **kvarhållningsintervall**anger du hur länge du vill behålla disk data. I **Synkroniseringsfrekvens**anger du hur ofta stegvis säkerhets kopiering av data ska köras. Alternativt kan du, i stället för att välja ett intervall för stegvisa säkerhets kopieringar, aktivera **precis innan en återställnings punkt**. När den här inställningen är aktive rad kommer MABS att köra en fullständig snabb säkerhets kopiering precis innan varje schemalagd återställnings punkt.

    > [!NOTE]
    >
    >Om du skyddar program arbets belastningar skapas återställnings punkter i enlighet med synkroniseringsfrekvensen, förutsatt att programmet har stöd för stegvisa säkerhets kopieringar. Om den inte gör det kör MABS en fullständig snabb säkerhets kopiering i stället för en stegvis säkerhets kopiering och skapar återställnings punkter i enlighet med schemat för snabb säkerhets kopiering.

    

7. På sidan **Granska diskallokering** granskar du allokerat disk utrymme för lagringspoolen för skydds gruppen.

   **Total data storlek** är storleken på de data som du vill säkerhetskopiera och **disk utrymme som ska tillhandahållas på Mabs** är det utrymme som Mabs rekommenderar för skydds gruppen. MABS väljer den ideala säkerhets kopierings volymen baserat på inställningarna. Du kan dock redigera säkerhets kopierings volym alternativen i **disk tilldelnings informationen**. För arbets belastningarna väljer du önskad lagring i list menyn. Dina ändringar ändrar värdena för **Total lagring** och ledigt **lagrings utrymme** i fönstret **tillgängliga disklagring** . Underetablerat utrymme är mängden lagrings MABS som föreslår att du lägger till volymen, för att fortsätta med säkerhets kopieringar smidigt i framtiden.

8. På sidan **Välj metod för skapande av replik** anger du hur den inledande replikeringen av data i skydds gruppen ska utföras. Om du väljer att **Replikera över nätverket automatiskt**rekommenderar vi att du väljer en tid med låg belastning. För stora mängder data eller mindre än optimala nätverks förhållanden bör du överväga att välja **manuellt**, vilket kräver att replikera data offline med hjälp av flyttbara medier.

9. På sidan **alternativ för konsekvens kontroll** väljer du hur du vill automatisera konsekvens kontroller. Du kan aktivera att en kontroll endast körs när replik data blir inkonsekventa eller enligt ett schema. Om du inte vill konfigurera automatisk konsekvens kontroll kan du när som helst köra en manuell kontroll genom att högerklicka på skydds gruppen och välja **utför konsekvens kontroll**.

    När du har skapat skydds gruppen utförs inledande replikering av data i enlighet med den metod som du har valt. Efter den inledande replikeringen sker varje säkerhets kopiering i enlighet med inställningarna för skydds gruppen. Tänk på följande om du behöver återställa säkerhetskopierade data:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Säkerhetskopiera virtuella datorer som kon figurer ATS för direktmigrering
När virtuella datorer är involverade i Direktmigrering, fortsätter MABS att skydda de virtuella datorerna så länge MABS-skyddsagenten är installerad på Hyper-V-värden. Hur MABS skyddar de virtuella datorerna beror på vilken typ av Direktmigrering som berörs.

**Direktmigrering i ett kluster** – när en virtuell dator migreras i ett kluster Mabs identifierar migreringen och säkerhetskopierar den virtuella datorn från den nya klusternoden utan krav på åtgärder från användaren. Eftersom lagrings platsen inte har ändrats fortsätter MABS med snabba och fullständiga säkerhets kopieringar. 

**Direktmigrering utanför klustret** – när en virtuell dator migreras mellan fristående servrar, olika kluster eller mellan en fristående server och ett kluster, identifierar Mabs migreringen och kan säkerhetskopiera den virtuella datorn utan användare berörda.

### <a name="requirements-for-maintaining-protection"></a>Krav för att upprätthålla skyddet

Följande är krav för att upprätthålla skyddet under Direktmigrering:

- Hyper-V-värdarna för de virtuella datorerna måste finnas i ett System Center VMM-moln på en VMM-server som kör minst System Center 2012 med SP1.

- MABS-skydds agenten måste vara installerad på alla Hyper-V-värdar.

- MABS-servrar måste vara anslutna till VMM-servern. Alla Hyper-V-värdstyrenheter i VMM-molnet måste också vara anslutna till MABS-servrarna. Detta gör att MABS kan kommunicera med VMM-servern så att MABS kan ta reda på vilken Hyper-V-värdservern som den virtuella datorn körs för tillfället och för att skapa en ny säkerhets kopia från den Hyper-V-servern. Om det inte går att upprätta en anslutning till Hyper-V-servern Miss lyckas säkerhets kopieringen med ett meddelande om att MABS-skyddsagenten inte kan kontaktas.

- Alla MABS-servrar, VMM-servrar och Hyper-V-värd servrar måste finnas i samma domän.

### <a name="details-about-live-migration"></a>Information om Direktmigrering

Observera följande för säkerhets kopiering under Direktmigrering:


- Om en Direktmigrering överför lagring, utför MABS en fullständig konsekvens kontroll av den virtuella datorn och fortsätter sedan med snabba och fullständiga säkerhets kopieringar. När direktmigrering av lagring sker omorganiserar Hyper-V den virtuella hård disken (VHD) eller VHDX, vilket orsakar en engångs insamling i storleken på MABS säkerhets kopierings data.

- Aktivera automatisk montering på den virtuella värddatorn för att aktivera virtuellt skydd och inaktivera TCP Chimney-avlastning.

- MABS använder port 6070 som standard port för att vara värd för DPM-VMM Helper-tjänsten. Så här ändrar du registret:

    1. Gå till **HKLM\Software\Microsoft\Microsoft Data Protection Manager\Configuration**.
    2. Skapa ett 32-bitars DWORD-värde: DpmVmmHelperServicePort och skriv det uppdaterade port numret som en del av register nyckeln.
    3.  Öppna ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```och ändra port numret från 6070 till den nya porten. Exempel: ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. Starta om DPM-VMM Helper service och starta om DPM-tjänsten.

### <a name="set-up-protection-for-live-migration"></a>Konfigurera skydd för direktmigrering

Konfigurera skydd för direktmigrering:

1. Konfigurera MABS-servern och dess lagring och installera MABS-skyddsagenten på varje Hyper-V-värdservern eller klusternod i VMM-molnet. Om du använder SMB-lagring i ett kluster, installerar du MABS-skyddsagenten på alla klusternoder.

2. Installera VMM-konsolen som en klient komponent på MABS-servern så att MABS kan kommunicera med VMM-servern. Konsolen ska vara samma version som den som körs på VMM-servern.

3. Tilldela MABSMachineName $-kontot som ett skrivskyddat administratörs konto på VMM-hanteringsservern.

4. Anslut alla Hyper-V-värdstyrenheter till alla Mabs-servrar med `Set-DPMGlobalProperty` PowerShell-cmdleten. Cmdleten accepterar flera MABS-Server namn. Använd formatet: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Mer information finns i [Set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. När alla virtuella datorer som körs på Hyper-V-värdar i VMM-molnet har identifierats i VMM skapar du en skydds grupp och lägger till de virtuella datorer som du vill skydda. Automatiska konsekvens kontroller bör vara aktiverat på skydds grupps nivå för skydd under scenarier med virtuella datorer.

6. När inställningarna har kon figurer ATS, när en virtuell dator migreras från ett kluster till ett annat, fortsätter alla säkerhets kopior som förväntat. Du kan kontrol lera att direktmigrering är aktiverat som förväntat på följande sätt:

   1. Kontrol lera att DPM-VMM Helper Service körs. Om den inte är det startar du den.

   2. Öppna Microsoft SQL Server Management Studio och Anslut till den instans som är värd för MABS-databasen (DPMDB). Kör följande fråga på DPMDB: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Den här frågan innehåller en egenskap `KnownVMMServer`som heter. Värdet ska vara samma värde som du angav med `Set-DPMGlobalProperty` cmdleten.

   3. Kör följande fråga för att verifiera *VMMIdentifier* -parametern i `PhysicalPathXML` för en viss virtuell dator. Ersätt `VMName` med namnet på den virtuella datorn.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Öppna XML-filen som den här frågan returnerar och kontrol lera att fältet *VMMIdentifier* har ett värde.


### <a name="run-manual-migration"></a>Kör manuell migrering

När du har slutfört stegen i föregående avsnitt och MABS Summary Manager-jobbet har slutförts, aktive ras migreringen. Som standard startar detta jobb vid midnatt och körs varje morgon. Om du vill köra en manuell migrering för att kontrol lera att allt fungerar som förväntat gör du följande:

1. Öppna SQL Server Management Studio och Anslut till den instans som är värd för MABS-databasen.

2. Kör följande fråga: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Den här frågan returnerar **ScheduleID**. Observera detta ID eftersom du kommer att använda det i nästa steg.

3. Expandera **SQL Server Agent**i SQL Server Management Studio och expandera sedan **jobb**. Högerklicka på **ScheduleID** som du har antecknat och välj **starta jobb i steg**.

Prestanda vid säkerhets kopiering påverkas när jobbet körs. Distributionens storlek och skala avgör hur lång tid det tar att slutföra jobbet.


## <a name="back-up-replica-virtual-machines"></a>Säkerhetskopiera virtuella replik datorer

Om MABS körs på Windows Server 2012 R2 eller senare kan du säkerhetskopiera virtuella replik datorer. Detta är användbart av flera skäl:

**Minskar effekten av säkerhets kopieringar på** arbets belastningen som körs – en säkerhets kopia av en virtuell dator medför en del kostnader när en ögonblicks bild skapas. Genom att avlasta säkerhets kopierings processen till en sekundär fjärrplats påverkas inte längre den aktiva arbets belastningen av säkerhets kopierings åtgärden. Detta gäller endast för distributioner där säkerhets kopian lagras på en fjärran sluten plats. Du kan till exempel göra dagliga säkerhets kopior och lagra data lokalt för att säkerställa snabba återställnings tider, men göra månatliga eller kvartals Visa säkerhets kopior från virtuella replik datorer som fjärrlagras för långsiktig kvarhållning.

**Sparar bandbredd** – i en typisk distribution av fjärranslutna avdelnings kontor/huvud kontor behöver du en lämplig mängd allokerad bandbredd för att överföra säkerhets kopierings data mellan platser. Om du skapar en replikerings-och redundansväxlings strategi kan du, förutom din data säkerhets kopierings strategi, minska mängden redundanta data som skickas över nätverket. Genom att säkerhetskopiera den virtuella replik datorns data i stället för den primära, sparar du omkostnaderna för att skicka säkerhetskopierade data över nätverket.

**Aktiverar säkerhets kopiering av värdar** – du kan använda ett värdbaserad Data Center som en replik plats, utan att det finns något behov av ett sekundärt Data Center. I det här fallet kräver värd avtalets service avtal konsekvent säkerhets kopiering av virtuella replik datorer.

En virtuell replik dator stängs av tills en redundansväxling initieras, och VSS kan inte garantera en programkonsekvent säkerhets kopiering för en virtuell replik dator. Därför kommer säkerhets kopieringen av en virtuell replik dator att krascha. Om krasch konsekvens inte kan garanteras kommer säkerhets kopieringen att Miss lyckas och detta kan inträffa under ett antal förhållanden:

- Den virtuella replik datorn är inte felfri och är i ett kritiskt tillstånd.

- Den virtuella replik datorn omsynkroniseras (i läget omsynkronisering pågår eller Omsynkronisering krävs).

- Inledande replikering mellan den primära och den sekundära platsen pågår eller väntar på den virtuella datorn.

- . HRL-loggar tillämpas på den virtuella replik datorn eller en tidigare åtgärd för att tillämpa. HRL-loggarna på den virtuella disken misslyckades eller avbröts eller avbröts.

- Migrering eller redundans av den virtuella replik datorn pågår

## <a name="recover-backed-up-virtual-machines"></a>Återställa säkerhetskopierade virtuella datorer

När du kan återställa en säkerhets kopie rad virtuell dator använder du återställnings guiden för att välja den virtuella datorn och den aktuella återställnings punkten. Öppna återställnings guiden och Återställ en virtuell dator:

1. I administratörs konsolen för MABS anger du namnet på den virtuella datorn eller expanderar listan över skyddade objekt och väljer den virtuella dator som du vill återställa.

2. I rutan **återställnings punkter för** klickar du på ett datum i kalendern för att se tillgängliga återställnings punkter. I rutan **sökväg** väljer du sedan den återställnings punkt som du vill använda i återställnings guiden.

3. Från menyn **åtgärder** klickar du på **Återställ** för att öppna återställnings guiden.

    Den virtuella datorn och återställnings punkten du har valt visas på skärmen **Granska val av återställning** . Klicka på **Nästa**.

4. På skärmen **Välj återställnings typ** väljer du var du vill återställa data och klickar sedan på **Nästa**.

    -   **Återställ till ursprunglig instans**: När du återställer till den ursprungliga instansen tas den ursprungliga virtuella hård disken bort. MABS återställer den virtuella hård disken och andra konfigurationsfiler till den ursprungliga platsen med hjälp av VSS-skrivaren för Hyper-V. I slutet av återställnings processen har virtuella datorer fortfarande hög tillgänglighet.
        Resurs gruppen måste vara tillgänglig för återställning. Om den inte är tillgänglig återställer du till en annan plats och gör sedan den virtuella datorn hög tillgänglig.

    -   **Återställ som virtuell dator till valfri värd**: MABS stöder alternativ plats återställning (återställning till), som ger en sömlös återställning av en skyddad virtuell Hyper-V-dator till en annan Hyper-V-värd, oberoende av processor arkitekturen. Virtuella Hyper-V-datorer som återställs till en klusternod är inte hög tillgängliga. Om du väljer det här alternativet visar återställnings guiden en ytterligare skärm för att identifiera mål-och mål Sök vägen.

    -   **Kopiera till en nätverksmapp**: MABS stöder återställning på objekt nivå (ILR), vilket gör att du kan utföra återställning på objekt nivå av filer, mappar, volymer och virtuella hård diskar (VHD: er) från en säkerhets kopia på värdnivå av virtuella Hyper-V-datorer till en nätverks resurs eller en volym på en MABS-skyddad Server. MABS-skyddsagenten behöver inte vara installerad på gästen för att utföra återställning på objekt nivå. Om du väljer det här alternativet visar återställnings guiden en ytterligare skärm för att identifiera mål-och mål Sök vägen.

5. Konfigurera återställnings alternativen i **Ange återställnings alternativ** och klicka på **Nästa**:

    - Om du återställer en virtuell dator över låg bandbredd klickar du på **ändra** för att aktivera **begränsning av nätverks bandbredd**. När du har aktiverat alternativet för begränsning kan du ange hur mycket bandbredd som ska vara tillgänglig och hur lång tid det tar att använda bandbredden.
    - Välj **Aktivera San-baserad återställning med hjälp av ögonblicks bilder av maskin vara** om du har konfigurerat nätverket.
    - Välj **Skicka ett e-postmeddelande när återställningen är slutförd** och ange sedan e-postadresserna, om du vill att e-postaviseringar ska skickas när återställningen är klar

6. På sidan Sammanfattning kontrollerar du att all information är korrekt. Om informationen inte är korrekt, eller om du vill göra en ändring, klickar du på **tillbaka**. Om du är nöjd med inställningarna klickar du på **Återställ** för att starta återställnings processen.

7. Skärmen **återställnings status** innehåller information om återställnings jobbet.


## <a name="next-steps"></a>Nästa steg
[Återställa data från Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
