---
title: Planering för en Azure File Sync-distribution | Microsoft-dokument
description: Lär dig vad du bör tänka på när du planerar för en Azure Files-distribution.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255124"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync
[Azure-filer](storage-files-introduction.md) kan distribueras på två huvudsätt: genom att direkt montera de serverlösa Azure-filresurserna eller genom att cachelagring av Azure-filresurser lokalt med Azure File Sync. Vilket distributionsalternativ du väljer ändrar de saker du behöver tänka på när du planerar för distributionen. 

- **Direktmontering av en Azure-filresurs:** Eftersom Azure Files ger SMB-åtkomst kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standardklienten för SMB som är tillgänglig i Windows, macOS och Linux. Eftersom Azure-filresurser är serverlösa krävs inte distribution för produktionsscenarier att hantera en filserver eller NAS-enhet. Det innebär att du inte behöver använda programkorrigeringar eller byta ut fysiska diskar. 

- **Cache Azure-filresurs lokalt med Azure File Sync**: Azure File Sync gör att du kan centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar en lokal (eller moln) Windows Server till en snabb cache av din Azure-filresurs. 

Den här artikeln behandlar främst distributionsöverväganden för distribution av Azure File Sync. Information om hur du planerar att en distribution av Azure-filresurser ska monteras direkt av en lokal klient eller molnklient finns i [Planera för en Azure Files-distribution](storage-files-planning.md).

## <a name="management-concepts"></a>Ledningskoncept
En Azure File Sync-distribution har tre grundläggande hanteringsobjekt:

- **Azure-filresurs:** En Azure-filresurs är en serverlös molnfilresurs som tillhandahåller *molnslutpunkten* för en Synkroniseringsrelation för Azure File Sync. Filer i en Azure-filresurs kan nås direkt med SMB eller FileREST-protokollet, även om vi rekommenderar att du i första hand kommer åt filerna via Windows Server-cachen när Azure-filresursen används med Azure File Sync. Detta beror på att Azure Files idag saknar en effektiv ändringsidentifieringsmekanism som Windows Server har, så ändringar i Azure-filresursen direkt tar tid att sprida tillbaka till serverns slutpunkter.
- **Serverslutpunkt:** Sökvägen till Windows Server som synkroniseras med en Azure-filresurs. Detta kan vara en specifik mapp på en volym eller roten av volymen. Flera serverslutpunkter kan finnas på samma volym om deras namnområden inte överlappar varandra.
- **Synkroniseringsgrupp**: Objektet som definierar synkroniseringsrelationen mellan en **molnslutpunkt**eller Azure-filresurs och en serverslutpunkt. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. Om du till exempel har två olika uppsättningar filer som du vill hantera med Azure File Sync, skapar du två synkroniseringsgrupper och lägger till olika slutpunkter i varje synkroniseringsgrupp.

### <a name="azure-file-share-management-concepts"></a>Azure-filresurshanteringskoncept
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Hanteringskoncept för Azure File Sync
Synkroniseringsgrupper distribueras till **Storage Sync Services**, som är objekt på den högsta nivån som registrerar servrar för användning med Azure File Sync och innehåller synkroniseringsgrupprelationerna. Storage Sync Service-resursen är en peer av lagringskontoresursen och kan på samma sätt distribueras till Azure-resursgrupper. En Storage Sync-tjänst kan skapa synkroniseringsgrupper som innehåller Azure-filresurser över flera lagringskonton och flera registrerade Windows-servrar.

Innan du kan skapa en synkroniseringsgrupp i en storage sync-tjänst måste du först registrera en Windows-server med tjänsten Storage Sync. Detta skapar ett **registrerat serverobjekt** som representerar en förtroenderelation mellan servern eller klustret och lagringssynkroniseringstjänsten. Om du vill registrera en storage sync-tjänst måste du först installera Azure File Sync-agenten på servern. En enskild server eller ett enskilt kluster kan registreras med endast en Storage Sync Service åt gången.

En synkroniseringsgrupp innehåller en molnslutpunkt eller Azure-filresurs och minst en serverslutpunkt. Serverslutpunktsobjektet innehåller de inställningar som konfigurerar **molnnivådelningsfunktionen,** som tillhandahåller cachelagringsfunktionen i Azure File Sync. För att synkronisera med en Azure-filresurs måste lagringskontot som innehåller Azure-filresursen finnas i samma Azure-region som Storage Sync Service.

### <a name="management-guidance"></a>Ledningsvägledning
När du distribuerar Azure File Sync rekommenderar vi:

- Distribuera Azure-filresurser 1:1 med Windows-filresurser. Serverslutpunktsobjektet ger dig stor flexibilitet när det gäller hur du ställer in synkroniseringstopologin på serversidan av synkroniseringsrelationen. Om du vill förenkla hanteringen får du sökvägen till serverslutpunkten att matcha sökvägen till Windows-filresursen. 

- Använd så få Storage Sync Services som möjligt. Detta förenklar hanteringen när du har synkroniseringsgrupper som innehåller flera serverslutpunkter, eftersom en Windows Server endast kan registreras på en Storage Sync Service åt gången. 

- Var uppmärksam på ett lagringskontos IOPS-begränsningar när du distribuerar Azure-filresurser. Helst skulle du mappa filresurser 1:1 med lagringskonton, men detta kanske inte alltid är möjligt på grund av olika begränsningar och begränsningar, både från din organisation och från Azure. När det inte är möjligt att bara ha en filresurs distribuerad i ett lagringskonto bör du tänka på vilka resurser som kommer att vara mycket aktiva och vilka resurser som blir mindre aktiva för att säkerställa att de hetaste filresurserna inte placeras i samma lagringskonto tillsammans.

## <a name="windows-file-server-considerations"></a>Överväganden för Windows-filserver
Om du vill aktivera synkroniseringsfunktionen på Windows Server måste du installera den nedladdningsbara azure-filsynkroniseringsagenten. Azure File Sync-agenten innehåller `FileSyncSvc.exe`två huvudkomponenter: , den bakgrunds-Windows-tjänst som ansvarar för `StorageSync.sys`att övervaka ändringar på serverns slutpunkter och initiera synkroniseringssessioner och , ett filsystemfilter som möjliggör molnnivåhantering och snabb haveriberedskap.  

### <a name="operating-system-requirements"></a>Operativsystemskrav
Azure File Sync stöds med följande versioner av Windows Server:

| Version | SKU:er som stöds | Distributionsalternativ som stöds |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard och IoT | Full och core |
| Windows Server 2016 | Datacenter-, standard- och lagringsserver | Full och core |
| Windows Server 2012 R2 | Datacenter-, standard- och lagringsserver | Full och core |

Framtida versioner av Windows Server läggs till när de släpps.

> [!Important]  
> Vi rekommenderar att du håller alla servrar som du använder med Azure File Sync uppdaterade med de senaste uppdateringarna från Windows Update. 

### <a name="minimum-system-resources"></a>Minsta systemresurser
Azure File Sync kräver en server, antingen fysisk eller virtuell, med minst en PROCESSOR och minst 2 GiB minne.

> [!Important]  
> Om servern körs på en virtuell dator med dynamiskt minne aktiverat bör den virtuella datorn konfigureras med minst 2048 MiB minne.

För de flesta produktionsarbetsbelastningar rekommenderar vi inte att du konfigurerar en Azure File Sync-synkroniseringsserver med endast minimikraven. Mer information [finns i Rekommenderade systemresurser.](#recommended-system-resources)

### <a name="recommended-system-resources"></a>Rekommenderade systemresurser
Precis som alla serverfunktioner eller program bestäms systemresurskraven för Azure File Sync av distributionens skala. större distributioner på en server kräver större systemresurser. För Azure File Sync bestäms skalan av antalet objekt över serverns slutpunkter och omsättningen på datauppsättningen. En enda server kan ha serverslutpunkter i flera synkroniseringsgrupper och antalet objekt som visas i följande tabellkonton för det fullständiga namnområde som en server är kopplad till. 

Till exempel serverslutpunkt A med 10 miljoner objekt + serverslutpunkt B med 10 miljoner objekt = 20 miljoner objekt. För den exempeldistributionen rekommenderar vi 8 processorer, 16 GiB minne för stabilt tillstånd och (om möjligt) 48 GiB minne för den första migreringen.
 
Namnområdesdata lagras i minnet av prestandaskäl. På grund av detta kräver större namnområden mer minne för att upprätthålla bra prestanda, och mer omsättning kräver mer CPU för att bearbeta. 
 
I följande tabell har vi tillhandahållit både storleken på namnområdet samt en konvertering till kapacitet för typiska allmänna filresurser, där den genomsnittliga filstorleken är 512 KiB. Om filstorlekarna är mindre bör du överväga att lägga till ytterligare minne för samma kapacitet. Basera minneskonfigurationen på namnområdets storlek.

| Namnområdesstorlek - filer & kataloger (miljoner)  | Typisk kapacitet (TiB)  | CPU-kärnor  | Rekommenderat minne (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (initial synkronisering)/ 2 (typisk omsättning)      |
| 5        | 2.3     | 2        | 16 (första synkronisering)/ 4 (typisk omsättning)    |
| 10       | 4.7     | 4        | 32 (initial synkronisering)/ 8 (typisk omsättning)   |
| 30       | 14.0    | 8        | 48 (initial synkronisering)/ 16 (typisk omsättning)   |
| 50       | 23.3    | 16       | 64 (första synkronisering)/ 32 (typisk omsättning)  |
| 100*     | 46.6    | 32       | 128 (initial synkronisering)/ 32 (typisk omsättning)  |

\*Synkronisering av mer än 100 miljoner filer & kataloger rekommenderas inte just nu. Detta är en mjuk gräns baserad på våra testade trösklar. Mer information finns i [Azure Files skalbarhet och prestandamål](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Inledande synkronisering av ett namnområde är en intensiv åtgärd och vi rekommenderar att du allokerar mer minne tills den första synkroniseringen är klar. Detta är inte nödvändigt, men kan påskynda den första synkroniseringen. 
> 
> Typisk omsättning är 0,5% av namnområdet ändras per dag. För högre nivåer av omsättning, överväga att lägga till mer CPU. 

- En lokalt ansluten volym formaterad med NTFS-filsystemet.

### <a name="evaluation-cmdlet"></a>Utvärdering cmdlet
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med ditt system med hjälp av cmdlet för Azure File Sync-utvärdering. Den här cmdleten söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en version av operativsystemet som inte stöds. Dess kontroller omfattar de flesta men inte alla av de funktioner som nämns nedan; Vi rekommenderar att du läser igenom resten av det här avsnittet noggrant för att säkerställa att distributionen fungerar smidigt. 

Utvärderingscmdlet kan installeras genom att installera Az PowerShell-modulen, som kan installeras genom att följa instruktionerna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Användning  
Du kan anropa utvärderingsverktyget på några olika sätt: du kan utföra systemkontroller, datauppsättningskontroller eller båda. Så här utför du både system- och datauppsättningskontroller: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Så här testar du bara datauppsättningen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar du endast systemkraven:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Så här visar du resultaten i CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Kompatibilitet för filsystem
Azure File Sync stöds endast på direkt anslutna NTFS-volymer. Direkt ansluten lagring, eller DAS, på Windows Server innebär att Operativsystemet Windows Server äger filsystemet. DAS kan tillhandahållas genom att fysiskt koppla diskar till filservern, bifoga virtuella diskar till en virtuell filserver (till exempel en virtuell dator som finns värd Hyper-V) eller till och med via ISCSI.

Endast NTFS-volymer stöds. ReFS, FAT, FAT32 och andra filsystem stöds inte.

I följande tabell visas interop-tillståndet för NTFS-filsystemfunktioner: 

| Funktion | Supportstatus | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullt stöd | Diskretionära åtkomstkontrollistor i Windows-stil bevaras av Azure File Sync och tillämpas av Windows Server på serverslutpunkter. ACL kan också tillämpas när azure-filresursen monteras direkt, men detta kräver ytterligare konfiguration. Mer information finns i [avsnittet Identitet.](#identity) |
| Hårda länkar | Överhoppad | |
| Symboliska länkar | Överhoppad | |
| Montera punkter | Delvis stöds | Monteringspunkter kan vara roten till en serverslutpunkt, men de hoppas över om de finns i en serverslutpunkts namnområde. |
| Korsningar | Överhoppad | Till exempel mapparna Distributed File System DfrsrPrivate och DFSRoots. |
| Referenspunkter | Överhoppad | |
| NTFS-komprimering | Fullt stöd | |
| Glesa filer | Fullt stöd | Sparse filer synkroniseras (blockeras inte), men de synkroniseras med molnet som en fullständig fil. Om filinnehållet ändras i molnet (eller på en annan server) är filen inte längre gles när ändringen hämtas. |
| Alternativa dataströmmar (ADS) | Bevarad men inte synkroniserad | Klassificeringstaggar som skapats av filklassificeringsinfrastrukturen synkroniseras till exempel inte. Befintliga klassificeringstaggar på filer på var och en av serverns slutpunkter lämnas orörda. |

<a id="files-skipped"></a>Azure File Sync hoppar också över vissa temporära filer och systemmappar:

| Fil/mapp | Obs! |
|-|-|
| pagefile.sys | Fil som är specifik för systemet |
| Desktop.ini | Fil som är specifik för systemet |
| Thumbs.db | Temporär fil för miniatyrer |
| ehthumbs.db (på) | Temporär fil för medieminiatyrer |
| ~$\*.\* | Tillfällig office-fil |
| \*Tmp | Temporär fil |
| \*.laccdb (laccdb) | Komma åt DB-låsfil|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern synkroniseringsfil|
| \\Information om systemets volym | Mapp som är specifik för volymen |
| $RECYCLE. Bin| Mapp |
| \\Synkroniserade delstat | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server Redundanskluster stöds av Azure File Sync för distributionsalternativet "Filserver för allmänt bruk". Redundanskluster stöds inte på "Scale-Out File Server for application data" (SOFS) eller på Csv (Clustered Shared Volumes).

> [!Note]  
> Azure File Sync-agenten måste installeras på varje nod i ett redundanskluster för att synkroniseringen ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
**Windows Server 2016 och Windows Server 2019**   
Datadeduplicering stöds på volymer med molnnivåaktivering aktiverat på Windows Server 2016 och Windows Server 2019. Om du aktiverar dataduplicering på en volym med molnnivåaktivering aktiverat kan du cachelagra fler filer lokalt utan att etablera mer lagringsutrymme. 

När datadeduplicering är aktiverat på en volym med molnnivåaktiverad, kommer Dedup-optimerade filer inom serverslutpunktens plats att nivåindelas som liknar en normal fil baserat på principinställningarna för molnnivåiering. När de Dedup-optimerade filerna har nivåats upp körs datadedupliceringsavfallsinsamlingsjobbet automatiskt för att frigöra diskutrymme genom att ta bort onödiga segment som inte längre refereras av andra filer på volymen.

Observera att volymbesparingarna endast gäller för servern. Dina data i Azure-filresursen kommer inte att avderas.

> [!Note]  
> För att stödja datadeduplicering på volymer med molnnivåaktivering aktiverat på Windows Server 2019 måste Windows update [KB4520062](https://support.microsoft.com/help/4520062) installeras och Azure File Sync agent version 9.0.0.0 eller nyare krävs.

**Windows Server 2012 R2**  
Azure File Sync stöder inte dataduplicering och molnnivådelning på samma volym på Windows Server 2012 R2. Om datadyuplicering är aktiverat på en volym måste molnnivådelning inaktiveras. 

**Obs!**
- Om datadeduplicering installeras innan azure file sync-agenten installeras krävs en omstart för att stödja datadeduplicering och molnnivådelning på samma volym.
- Om datadeduplicering är aktiverat på en volym efter att molnnivåspel har aktiverats optimerar det första optimeringsjobbet för deduplicering filer på volymen som inte redan är nivåindelade och har följande inverkan på molnnivådelning:
    - Principen för ledigt utrymme fortsätter att nivåfiler enligt det lediga utrymmet på volymen med hjälp av heatmap.
    - Datumprincipen hoppar över nivåindelning av filer som annars kan ha varit berättigade till nivåindelning på grund av optimeringsoptimeringsjobbet för deduplicering som använder filerna.
- För pågående dedupliceringsoptimeringsjobb försenas molnnivåiering med datumprincip av inställningen [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) för datadredlicering, om filen inte redan är nivåindelade. 
    - Exempel: Om inställningen MinimumFileAgeDays är sju dagar och datumprincipen för molnnivånivå är 30 dagar, kommer datumprincipen att nivåfiler efter 37 dagar.
    - När en fil har nivåats av Azure File Sync hoppar deduplicationsoptimeringsjobbet över filen.
- Om en server som kör Windows Server 2012 R2 med Azure File Sync-agenten installerad uppgraderas till Windows Server 2016 eller Windows Server 2019 måste följande steg utföras för att stödja datadeduplicering och molnnivåiering på samma volym:  
    - Avinstallera Azure File Sync-agenten för Windows Server 2012 R2 och starta om servern.
    - Hämta Azure File Sync-agenten för den nya serveroperativsystemversionen (Windows Server 2016 eller Windows Server 2019).
    - Installera Azure File Sync-agenten och starta om servern.  
    
    Konfigurationsinställningarna för Azure File Sync på servern behålls när agenten avinstalleras och installeras om.

### <a name="distributed-file-system-dfs"></a>Distribuerat filsystem (DFS)
Azure File Sync stöder interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R).

**DFS-namnområden (DFS-N)**: Azure File Sync stöds fullt ut på DFS-N-servrar. Du kan installera Azure File Sync-agenten på en eller flera DFS-N-medlemmar för att synkronisera data mellan serverslutpunkterna och molnslutpunkten. Mer information finns i [översikt över DFS-namnområden](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-R)**: Eftersom DFS-R och Azure File Sync båda är replikeringslösningar rekommenderar vi i de flesta fall att ersätta DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync-distribution. Mer information finns i [Migrera en DFS-Replication-distribution (DFS-R) till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av fildata kan inte anslutas direkt till internet.
- Filialservrar konsoliderar data på en enda hubbserver, som du vill använda Azure File Sync för.

För Azure File Sync och DFS-R att arbeta sida vid sida:

1. Molnnivåindelning i Azure File Sync måste inaktiveras på volymer med DFS-R-replikerade mappar.
2. Serverslutpunkter bör inte konfigureras på DFS-R-skrivskyddade replikeringsmappar.

Mer information finns i [DFS Replication översikt](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Det går inte att använda sysprep på en server som har Azure File Sync-agenten installerad och kan leda till oväntade resultat. Agentinstallation och serverregistrering bör ske efter att serveravbildningen har distribuerats och systemprep-miniinstallationen har slutförts.

### <a name="windows-search"></a>Windows-sök
Om molnnivådelning är aktiverat på en serverslutpunkt hoppas filer som är nivåindelade över och indexeras inte av Windows Search. Filer som inte är nivåer indexeras korrekt.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra hierarkiska lagringshanteringslösningar (HSM)
Inga andra HSM-lösningar bör användas med Azure File Sync.

## <a name="identity"></a>Identitet
Azure File Sync fungerar med din vanliga AD-baserade identitet utan någon särskild inställning utöver att konfigurera synkronisering. När du använder Azure File Sync är den allmänna förväntningen att de flesta åtkomster går via cachelagringsservrarna för Azure File Sync i stället för via Azure-filresursen. Eftersom serverslutpunkterna finns på Windows Server och Windows Server har stöd för AD- och Windows-liknande ACL:er under mycket lång tid, behövs ingenting utöver att se till att De Windows-filservrar som är registrerade med Storage Sync Service är domänanslutna. Azure File Sync lagrar ACL:er på filerna i Azure-filresursen och replikerar dem till alla serverslutpunkter.

Även om ändringar som görs direkt i Azure-filresursen tar längre tid att synkronisera till serverslutpunkterna i synkroniseringsgruppen, kanske du också vill se till att du kan tillämpa dina AD-behörigheter för filresursen direkt i molnet också. För att kunna göra detta måste du domän ansluta ditt lagringskonto till din lokala AD, precis som hur dina Windows-filservrar är domänanslutna. Mer information om domän som ansluter ditt lagringskonto till en kundägd Active Directory finns i [Azure Files Active Directory översikt](storage-files-active-directory-overview.md).

> [!Important]  
> Domän som ansluter ditt lagringskonto till Active Directory krävs inte för att distribuera Azure File Sync. Det här är ett strikt valfritt steg som gör att Azure-filresursen kan framtvinga lokala ACL:er när användare monterar Azure-filresursen direkt.

## <a name="networking"></a>Nätverk
Azure File Sync-agenten kommunicerar med din Storage Sync Service och Azure-filresurs med hjälp av AZURE File Sync REST-protokollet och FileREST-protokollet, som båda alltid använder HTTPS över port 443. SMB används aldrig för att ladda upp eller hämta data mellan din Windows Server och Azure-filresursen. Eftersom de flesta organisationer tillåter HTTPS-trafik över port 443, som ett krav för att besöka de flesta webbplatser, krävs vanligtvis inte särskild nätverkskonfiguration för att distribuera Azure File Sync.

Baserat på organisationens princip eller unika regelkrav kan du kräva mer restriktiv kommunikation med Azure och därför tillhandahåller Azure File Sync flera mekanismer för att konfigurera nätverk. Baserat på dina krav kan du:

- Tunnelsynkronisering och filuppladdning/nedladdningstrafik via din ExpressRoute eller Azure VPN. 
- Använd Azure-filer och Azure Networking-funktioner som tjänstslutpunkter och privata slutpunkter.
- Konfigurera Azure File Sync så att den stöder din proxy i din miljö.
- Begränsa nätverksaktivitet från Azure File Sync.

Mer information om hur du konfigurerar nätverksfunktionerna i Azure File Sync finns i:
- [Proxy- och brandväggsinställningar för Azure File Sync](storage-sync-files-firewall-and-proxy.md)
- [Se till att Azure File Sync är en bra granne i ditt datacenter](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Kryptering
När du använder Azure File Sync finns det tre olika krypteringslager att tänka på: kryptering på lagring i vila av Windows Server, kryptering under överföring mellan Azure File Sync agent och Azure och kryptering i resten av dina data i Azure-filresursresursen. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-kryptering i vila 
Det finns två strategier för att kryptera data på Windows Server som fungerar i allmänhet med Azure File Sync: kryptering under filsystemet så att filsystemet och alla data som skrivs till det är krypterat och kryptering inom själva filformatet. Dessa metoder utesluter inte varandra. de kan användas tillsammans om så önskas eftersom syftet med kryptering är annorlunda.

Om du vill tillhandahålla kryptering under filsystemet tillhandahåller Windows Server BitLocker-inkorgen. BitLocker är helt transparent för Azure File Sync. Den främsta anledningen till att använda en krypteringsmekanism som BitLocker är att förhindra fysisk exfiltration av data från ditt lokala datacenter av någon som stjäl diskarna och för att förhindra att ett obehörigt operativsystem för att utföra obehöriga läsningar/skrivningar till dina data. Mer information om BitLocker finns i [Översikt över BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Tredjepartsprodukter som fungerar på samma sätt som BitLocker, eftersom de sitter under NTFS-volymen, bör på samma sätt fungera helt transparent med Azure File Sync. 

Den andra huvudmetoden för att kryptera data är att kryptera filens dataström när programmet sparar filen. Vissa program kan göra detta inbyggt, men detta är oftast inte fallet. Ett exempel på en metod för att kryptera filens dataström är Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Den främsta anledningen till att använda en krypteringsmekanism som AIP/RMS är att förhindra dataexfiltration av data från filresursen genom att personer kopierar den till alternativa platser, till exempel till en flash-enhet eller e-posta den till en obehörig person. När en fils dataström krypteras som en del av filformatet fortsätter den här filen att krypteras på Azure-filresursen. 

Azure File Sync samverkar inte med NTFS Encrypted File System (NTFS EFS) eller krypteringslösningar från tredje part som sitter ovanför filsystemet men under filens dataström. 

### <a name="encryption-in-transit"></a>Kryptering under överföring
Azure File Sync agent kommunicerar med din Storage Sync Service och Azure-filresurs med hjälp av Azure File Sync REST-protokollet och FileREST-protokollet, som båda alltid använder HTTPS över port 443. Azure File Sync skickar inte okrypterade begäranden via HTTP. 

Azure-lagringskonton innehåller en växel för att kräva kryptering under överföring, vilket är aktiverat som standard. Även om växeln på lagringskontonivå är inaktiverad, vilket innebär att okrypterade anslutningar till dina Azure-filresurser är möjliga, används Azure File Sync fortfarande bara krypterade kanaler för att komma åt filresursen.

Den främsta orsaken till att inaktivera kryptering under överföring för lagringskontot är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller äldre Linux-distribution, och prata med en Azure-filresurs direkt. Om det äldre programmet talar med filresursens Windows Server-cache får den här inställningen ingen effekt. 

Vi rekommenderar starkt att du ser till att kryptering av data under överföringen är aktiverad.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure-lagring](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Kryptering av Azure-fildelning i vila
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standardfilresurser för att sträcka sig över upp till 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Tillgänglighet för Azure-filsynkroniseringsregion
Azure File Sync är tillgängligt i följande regioner:

| Azure-molnet | Geografisk region | Azure-region | Regionkod |
|-------------|-------------------|--------------|-------------|
| Offentlig | Asien | Asien, östra | `eastasia` |
| Offentlig | Asien | Sydostasien | `southeastasia` |
| Offentlig | Australien | Australien, östra | `australiaeast` |
| Offentlig | Australien | Australien, sydöstra | `australiasoutheast` |
| Offentlig | Brasilien | Brasilien, södra | `brazilsouth` |
| Offentlig | Kanada | Kanada, centrala | `canadacentral` |
| Offentlig | Kanada | Kanada, östra | `canadaeast` |
| Offentlig | Europa | Europa, norra | `northeurope` |
| Offentlig | Europa | Europa, västra | `westeurope` |
| Offentlig | Frankrike | Frankrike, centrala | `francecentral` |
| Offentlig | Frankrike | Södra Frankrike* | `francesouth` |
| Offentlig | Indien | Indien, centrala | `centralindia` |
| Offentlig | Indien | Indien, södra | `southindia` |
| Offentlig | Japan | Japan, östra | `japaneast` |
| Offentlig | Japan | Japan, västra | `japanwest` |
| Offentlig | Korea | Sydkorea, centrala | `koreacentral` |
| Offentlig | Korea | Sydkorea, södra | `koreasouth` |
| Offentlig | Sydafrika | Sydafrika North | `southafricanorth` |
| Offentlig | Sydafrika | Sydafrika Väst* | `southafricawest` |
| Offentlig | UAE | Centrala Förenade Arabemiraten* | `uaecentral` |
| Offentlig | UAE | Uae Norra | `uaenorth` |
| Offentlig | Storbritannien | Storbritannien, södra | `uksouth` |
| Offentlig | Storbritannien | Storbritannien, västra | `ukwest` |
| Offentlig | USA | USA, centrala | `centralus` |
| Offentlig | USA | USA, östra | `eastus` |
| Offentlig | USA | USA, östra 2 | `eastus2` |
| Offentlig | USA | USA, norra centrala | `northcentralus` |
| Offentlig | USA | USA, södra centrala | `southcentralus` |
| Offentlig | USA | USA, västra centrala | `westcentralus` |
| Offentlig | USA | USA, västra | `westus` |
| Offentlig | USA | USA, västra 2 | `westus2` |
| US Gov | USA | US Gov, Arizona | `usgovarizona` |
| US Gov | USA | US Gov, Texas | `usgovtexas` |
| US Gov | USA | US Gov, Virginia | `usgovvirginia` |

Azure File Sync stöder synkronisering endast med en Azure-filresurs som finns i samma region som Storage Sync Service.

För de regioner som är markerade med asterisker måste du kontakta Azure Support för att begära åtkomst till Azure Storage i dessa regioner. Processen beskrivs i [det här dokumentet](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geo-redundant och Geo-zon redundant lagring har möjlighet att manuellt redundanslagring till den sekundära regionen. Vi rekommenderar att du inte gör detta utanför en katastrof när du använder Azure File Sync på grund av den ökade sannolikheten för dataförlust. I händelse av en katastrof där du vill initiera en manuell redundans av lagring måste du öppna ett supportärende med Microsoft för att få Azure File Sync att återuppta synkroniseringen med den sekundära slutpunkten.

## <a name="migration"></a>Migrering
Om du har en befintlig Windows-filserver kan Azure File Sync installeras direkt, utan att behöva flytta data över till en ny server. Om du planerar att migrera till en ny Windows-filserver som en del av att anta Azure File Sync finns det flera möjliga metoder för att flytta data över:

- Skapa serverslutpunkter för din gamla filresurs och den nya filresursen och låt Azure File Sync synkronisera data mellan serverslutpunkterna. Fördelen med den här metoden är att det gör det mycket enkelt att överteckna lagringen på din nya filserver, eftersom Azure File Sync är molnnivåbaserad medveten. När du är redo kan du klippa över slutanvändare till filresursen på den nya servern och ta bort den gamla filresursens serverslutpunkt.

- Skapa en serverslutpunkt endast på den nya filservern och `robocopy`kopiera data till från den gamla filresursen med . Beroende på topologin för filresurser på din nya server (hur många resurser du har på varje volym, hur fri varje `robocopy` volym är osv.) kan du behöva tillfälligt etablera ytterligare lagringsutrymme eftersom det förväntas att från din gamla server till din nya server i ditt lokala datacenter kommer att slutföras snabbare än azure file sync kommer att flytta data till Azure.

Det är också möjligt att använda Data Box för att migrera data till en Azure File Sync-distribution. För det mesta, när kunder vill använda Data Box för att använda data, gör de det eftersom de tror att det kommer att öka hastigheten på deras distribution eller eftersom det kommer att hjälpa till med begränsade bandbredd scenarier. Även om det är sant att användningen av en dataruta för att använda data i din Azure File Sync-distribution minskar bandbreddsanvändningen, kommer det sannolikt att vara snabbare för de flesta scenarier att fortsätta en online-dataöverföring via en av de metoder som beskrivs ovan. Mer information om hur du använder Data Box för att använda data i din Azure File Sync-distribution finns [i Migrera data till Azure File Sync med Azure Data Box](storage-sync-offline-data-transfer.md).

Ett vanligt misstag som kunder gör när de migrerar data till sin nya Azure File Sync-distribution är att kopiera data direkt till Azure-filresursen i stället för på sina Windows-filservrar. Även om Azure File Sync identifierar alla nya filer på Azure-filresursen och synkroniserar dem tillbaka till dina Windows-filresurser, är detta i allmänhet betydligt långsammare än att läsa in data via Windows-filservern. Många Azure-kopieringsverktyg, till exempel AzCopy, har den ytterligare nackdelen att inte kopiera alla viktiga metadata för en fil, till exempel tidsstämplar och ACL:er.

## <a name="antivirus"></a>Antivirus
Eftersom antivirus fungerar genom att söka igenom filer efter känd skadlig kod kan ett antivirusprogram orsaka återkallande av nivåindelade filer. I version 4.0 och högre av Azure File Sync-agenten har nivåindelade filer det säkra Windows-attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS angett. Vi rekommenderar att du rådfrågar programvaruleverantören för att lära dig hur du konfigurerar sin lösning för att hoppa över läsarfiler med den här attributuppsättningen (många gör det automatiskt). 

Microsofts interna antiviruslösningar, Windows Defender och System Center Endpoint Protection (SCEP), hoppar båda automatiskt över läsande filer som har den här attributuppsättningen. Vi har testat dem och identifierat ett mindre problem: när du lägger till en server i en befintlig synkroniseringsgrupp återkallas filer som är mindre än 800 byte (hämtas) på den nya servern. Dessa filer kommer att finnas kvar på den nya servern och kommer inte att nivåindelad eftersom de inte uppfyller nivåstorlekskravet (>64kb).

> [!Note]  
> Antivirusleverantörer kan kontrollera kompatibiliteten mellan sin produkt och Azure File Sync med Hjälp av [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), som är tillgänglig för nedladdning på Microsoft Download Center.

## <a name="backup"></a>Säkerhetskopiering 
Precis som antiviruslösningar kan säkerhetskopieringslösningar leda till att nivåindelade filer återkallas. Vi rekommenderar att du använder en lösning för säkerhetskopiering av molnet för att säkerhetskopiera Azure-filresursen i stället för en lokal säkerhetskopieringsprodukt.

Om du använder en lokal säkerhetskopieringslösning bör säkerhetskopior utföras på en server i synkroniseringsgruppen som har inaktiverat molnnivådelning. När du utför en återställning använder du alternativen för återställning på volymnivå eller filnivå. Filer som återställs med alternativet återställning på filnivå synkroniseras till alla slutpunkter i synkroniseringsgruppen och befintliga filer ersätts med den version som återställs från säkerhetskopian.  Återställningar på volymnivå ersätter inte nyare filversioner i Azure-filresursen eller andra serverslutpunkter.

> [!Note]  
> BMR-återställning (Bare-metal) kan orsaka oväntade resultat och stöds för närvarande inte.

> [!Note]  
> Med version 9 av Azure File Sync-agenten stöds nu VSS-ögonblicksbilder (inklusive fliken Tidigare versioner) på volymer som har aktiverat molnnivådelning. Du måste dock aktivera tidigare versionskompatibilitet via PowerShell. [Lär dig mer](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg brandväggs- och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Övervaka Azure File Sync](storage-sync-files-monitoring.md)