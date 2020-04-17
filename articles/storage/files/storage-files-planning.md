---
title: Planering för en Azure Files-distribution | Microsoft-dokument
description: Lär dig vad du bör tänka på när du planerar för en Azure Files-distribution.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d5bf3a6df9d7292c18a93737fb7dea5d8c91f984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536511"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planera för distribution av Azure Files
[Azure-filer](storage-files-introduction.md) kan distribueras på två huvudsätt: genom att direkt montera de serverlösa Azure-filresurserna eller genom att cachelagring av Azure-filresurser lokalt med Azure File Sync. Vilket distributionsalternativ du väljer ändrar de saker du behöver tänka på när du planerar för distributionen. 

- **Direktmontering av en Azure-filresurs:** Eftersom Azure Files ger SMB-åtkomst kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standardklienten för SMB som är tillgänglig i Windows, macOS och Linux. Eftersom Azure-filresurser är serverlösa krävs inte distribution för produktionsscenarier att hantera en filserver eller NAS-enhet. Det innebär att du inte behöver använda programkorrigeringar eller byta ut fysiska diskar. 

- **Cache Azure-filresurs lokalt med Azure File Sync**: Azure File Sync gör att du kan centralisera organisationens filresurser i Azure Files, samtidigt som flexibiliteten, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar en lokal (eller moln) Windows Server till en snabb cache av din Azure-filresurs. 

Den här artikeln behandlar främst distributionsöverväganden för distribution av en Azure-filresurs som ska monteras direkt av en lokal klient eller molnklient. Information om hur du planerar för en Azure File Sync-distribution finns i [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Ledningskoncept
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

När du distribuerar Azure-filresurser till lagringskonton rekommenderar vi:

- Distribuera endast Azure-filresurser i lagringskonton med andra Azure-filresurser. Även om GPv2-lagringskonton gör att du kan ha lagringskonton för blandade ändamål, eftersom lagringsresurser som Azure-filresurser och blob-behållare delar lagringskontots gränser, kan det göra det svårare att felsöka prestandaproblem senare. 

- Var uppmärksam på ett lagringskontos IOPS-begränsningar när du distribuerar Azure-filresurser. Helst skulle du mappa filresurser 1:1 med lagringskonton, men detta kanske inte alltid är möjligt på grund av olika begränsningar och begränsningar, både från din organisation och från Azure. När det inte är möjligt att bara ha en filresurs distribuerad i ett lagringskonto bör du tänka på vilka resurser som kommer att vara mycket aktiva och vilka resurser som blir mindre aktiva för att säkerställa att de hetaste filresurserna inte placeras i samma lagringskonto tillsammans.

- Distribuera endast GPv2- och FileStorage-konton och uppgradera GPv1- och klassiska lagringskonton när du hittar dem i din miljö. 

## <a name="identity"></a>Identitet
För att komma åt en Azure-filresurs måste användaren av filresursen autentiseras och ha behörighet att komma åt resursen. Detta görs baserat på identiteten på användaren som använder filresursen. Azure Files integreras med tre huvudsakliga identitetsleverantörer:
- **Lokala Active Directory Domain Services (AD DS eller lokalt AD DS)** (förhandsversion): Azure-lagringskonton kan domänanslutas till en kundägd, Active Directory Domain Services, precis som en Windows Server-filserver eller NAS-enhet. Du kan distribuera en domänkontrollant lokalt, i en virtuell Azure-dator eller till och med som en virtuell dator i en annan molnleverantör. Azure Files är agnostiker till där domänkontrollanten är värd. När ett lagringskonto är domänanslutet kan slutanvändaren montera en filresurs med användarkontot som de loggade in på sin dator med. AD-baserad autentisering använder Kerberos-autentiseringsprotokollet.
- **Azure Active Directory Domain Services (Azure AD DS):** Azure AD DS tillhandahåller en Microsoft-hanterad domänkontrollant som kan användas för Azure-resurser. Domän som ansluter ditt lagringskonto till Azure AD DS ger liknande fördelar som domän som ansluter det till en kundägd Active Directory. Det här distributionsalternativet är mest användbart för scenarier för lyft och skift som kräver AD-baserade behörigheter. Eftersom Azure AD DS tillhandahåller AD-baserad autentisering använder det här alternativet även Kerberos-autentiseringsprotokollet.
- **Azure storage account key**: Azure-filresurser kan också monteras med en Azure-lagringskontonyckel. För att montera en filresurs på det här sättet används lagringskontonamnet som användarnamn och lagringskontonyckeln används som lösenord. Att använda lagringskontonyckeln för att montera Azure-filresursen är i själva verket en administratörsåtgärd, eftersom den monterade filresursen har fullständig behörighet till alla filer och mappar på resursen, även om de har ACL:er. När du använder lagringskontonyckeln för att montera över SMB används NTLMv2-autentiseringsprotokollet.

För kunder som migrerar från lokala filservrar eller skapar nya filresurser i Azure Files som är avsedda att fungera som Windows-filservrar eller NAS-enheter, är domän som ansluter ditt lagringskonto till **Kundägda Active Directory** det rekommenderade alternativet. Mer information om domän som ansluter ditt lagringskonto till en kundägd Active Directory finns i [Azure Files Active Directory översikt](storage-files-active-directory-overview.md).

Om du tänker använda lagringskontonyckeln för att komma åt dina Azure-filresurser rekommenderar vi att du använder tjänstslutpunkter enligt beskrivningen i avsnittet [Nätverk.](#networking)

## <a name="networking"></a>Nätverk
Azure-filresurser är tillgängliga var som helst via lagringskontots offentliga slutpunkt. Det innebär att autentiserade begäranden, till exempel begäranden som auktoriserats av en användares inloggningsidentitet, kan komma säkert inifrån eller utanför Azure. I många kundmiljöer misslyckas en första montering av Azure-filresursen på din lokala arbetsstation, även om fästen från virtuella Azure-datorer lyckas. Anledningen till detta är att många organisationer och Internet-leverantörer (IsPs) blockera den port som SMB använder för att kommunicera, port 445. En översikt över Internetleverantörer som tillåter och inte tillåter åtkomst från port 445 finns på [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Om du vill avblockera åtkomsten till din Azure-filresurs har du två huvudalternativ:

- Avblockera port 445 för organisationens lokala nätverk. Azure-filresurser kan endast nås externt via den offentliga slutpunkten med hjälp av internetsäkra protokoll som SMB 3.0 och FileREST API. Det här är det enklaste sättet att komma åt din Azure-filresurs från lokala eftersom den inte kräver avancerad nätverkskonfiguration utöver att ändra organisationens regler för utgående port, men vi rekommenderar att du tar bort äldre och inaktuella versioner av SMB-protokollet, nämligen SMB 1.0. Mer information om hur du gör detta finns i [Skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) och [Skydda Linux](storage-how-to-use-files-linux.md#securing-linux).

- Få åtkomst till Azure-filresurser via en ExpressRoute- eller VPN-anslutning. När du öppnar din Azure-filresurs via en nätverkstunnel kan du montera din Azure-filresurs som en lokal filresurs eftersom SMB-trafik inte passerar organisationsgränsen.   

Även om det ur ett tekniskt perspektiv är betydligt enklare att montera dina Azure-filresurser via den offentliga slutpunkten, förväntar vi oss att de flesta kunder väljer att montera sina Azure-filresurser via en ExpressRoute- eller VPN-anslutning. För att göra detta måste du konfigurera följande för din miljö:  

- **Nätverkstunneler med ExpressRoute, Plats-till-plats eller Punkt-till-plats-VPN:** Tunneling till ett virtuellt nätverk gör det möjligt att komma åt Azure-filresurser från lokala, även om port 445 är blockerad.
- **Privata slutpunkter:** Privata slutpunkter ger ditt lagringskonto en dedikerad IP-adress inifrån det virtuella nätverkets adressutrymme. Detta möjliggör nätverkstunnlar utan att behöva öppna lokala nätverk upp till alla IP-adressintervall som ägs av Azure-lagringskluster. 
- **DNS-vidarebefordran:** Konfigurera din lokala DNS för att matcha namnet på `storageaccount.file.core.windows.net` ditt lagringskonto (dvs. för de offentliga molnregionerna) för att matcha till IP-adressen för dina privata slutpunkter.

Information om hur du planerar för de nätverk som är associerade med distribution av en Azure-filresurs finns i [Azure Files networking considerations](storage-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
Azure Files stöder två olika typer av kryptering: kryptering under överföring, som relaterar till den kryptering som används vid montering/åtkomst till Azure-filresursen och kryptering i vila, vilket relaterar till hur data krypteras när de lagras på disken. 

### <a name="encryption-in-transit"></a>Kryptering under överföring
Som standard har alla Azure-lagringskonton kryptering i transit aktiverat. Det innebär att när du monterar en filresurs över SMB eller kommer åt den via FileREST-protokollet (till exempel via Azure-portalen, PowerShell/CLI eller Azure SDK) tillåter Azure-filer endast anslutningen om den görs med SMB 3.0+ med kryptering eller HTTPS. Klienter som inte stöder SMB 3.0 eller klienter som stöder SMB 3.0 men inte SMB-kryptering kan inte montera Azure-filresursen om kryptering under överföring är aktiverad. Mer information om vilka operativsystem som stöder SMB 3.0 med kryptering finns i vår detaljerade dokumentation för [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md). Alla aktuella versioner av PowerShell, CLI och SDK-erna stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure-lagringskonto. När kryptering är inaktiverat tillåter Azure Files också SMB 2.1, SMB 3.0 utan kryptering och okrypterade FileREST API-anrop via HTTP. Den främsta orsaken till att inaktivera kryptering under överföring är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure-filer tillåter endast SMB 2.1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2.1-klient utanför Azure-regionen i Azure-filresursen, till exempel lokalt eller i en annan Azure-region, kan inte komma åt filresursen.

Vi rekommenderar starkt att du ser till att kryptering av data under överföringen är aktiverad.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure-lagring](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Vilande kryptering
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

I allmänhet är Azure Files-funktioner och interoperabilitet med andra tjänster desamma mellan premiumfilresurser och standardfilresurser, men det finns några viktiga skillnader:
- **Faktureringsmodell**
    - Premium-filresurser faktureras med hjälp av en etablerad faktureringsmodell, vilket innebär att du betalar för hur mycket lagringsutrymme du etablerar i stället för hur mycket lagringsutrymme du faktiskt begär. 
    - Standardfilresurser faktureras med hjälp av en användningsbaserad modell, som innehåller en baskostnad för lagring för hur mycket lagringsutrymme du faktiskt förbrukar och sedan en extra transaktionskostnad baserat på hur du använder resursen. Med standardfilresurser ökar fakturan om du använder (läs/skriv/montera) Azure-filresursen mer.
- **Alternativ för redundans**
    - Premium-filresurser är endast tillgängliga för lokalt redundant (LRS) och zon redundant (ZRS) lagring. 
    - Standardfilresurser är tillgängliga för lokalt redundant, zon redundant, geo-redundant (GRS) och geo-zon redundant (GZRS) lagring.
- **Maximal storlek på filresurs**
    - Premium filaktier kan etableras för upp till 100 TiB utan ytterligare arbete.
    - Som standard filresurser kan sträcka sig endast över upp till 5 TiB, även om delningsgränsen kan ökas till 100 TiB genom att välja den *stora filresurslagringskontons* funktionsflagga. Standardfilresurser får endast sträcka sig över upp till 100 TiB för lokalt redundanta eller zon redundanta lagringskonton. Mer information om hur du ökar filresursstorlekarna finns i [Aktivera och skapa stora filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Regional tillgänglighet**
    - Premium-filresurser är inte tillgängliga i alla regioner och zon redundant stöd är tillgängligt i en mindre delmängd av regioner. Information om hur du tar reda på om premiumfilresurser för närvarande är tillgängliga i din region finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) för Azure. Information om vilka regioner som stöder ZRS finns i [Azure Availability Zone-stöd efter region](../../availability-zones/az-overview.md#services-support-by-region). Fyll i den här [undersökningen](https://aka.ms/pfsfeedback)för att hjälpa oss att prioritera nya regioner och premiumnivåfunktioner.
    - Standardfilresurser är tillgängliga i alla Azure-regioner.
- Azure Kubernetes Service (AKS) stöder premiumfilresurser i version 1.13 och senare.

När en filresurs har skapats som en premium- eller standardfilresurs kan du inte automatiskt konvertera den till den andra nivån. Om du vill växla till den andra nivån måste du skapa en ny filresurs på den nivån och manuellt kopiera data från den ursprungliga resursen till den nya resursen som du skapade. Vi rekommenderar `robocopy` att `rsync` du använder för Windows eller macOS och Linux för att utföra den kopian.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Förstå etablering för premiumfilresurser
Premium-filaktier etableras baserat på ett fast GiB/IOPS/dataflödesförhållande. För varje GiB-avsättning kommer aktien att emitteras en IOPS och 0,1 MiB/s dataflöde upp till maxgränserna per aktie. Minsta tillåtna etablering är 100 GiB med min IOPS/dataflöde.

Efter bästa möjliga ansträngning kan alla aktier brista upp till tre IOPS per GiB för etablerad lagring i 60 minuter eller längre beroende på aktiens storlek. Nya aktier börjar med full burst-kredit baserad på den avstämda kapaciteten.

Aktier måste etableras i 1 GiB-steg. Minsta storlek är 100 GiB, nästa storlek är 101 GiB och så vidare.

> [!TIP]
> Baslinje IOPS = 1 * etablerad GiB. (Upp till maximalt 100 000 IOPS).
>
> Burst Gräns = 3 * Baslinje IOPS. (Upp till maximalt 100 000 IOPS).
>
> utgående ränta = 60 MiB/s + 0,06 * etablerad GiB
>
> ingressränta = 40 MiB/s + 0,04 * etablerad GiB

Etablerat resursstorlek anges per aktiekvot. Aktiekvoten kan ökas när som helst men kan bara minskas efter 24 timmar sedan den senaste ökningen. När du har väntat i 24 timmar utan en kvotökning kan du minska kvoten så många gånger du vill, tills du ökar den igen. IOPS/Throughput-skalningsändringar kommer att träda i kraft inom några minuter efter storleksändringen.

Det är möjligt att minska storleken på din etablerade aktie under din använda GiB. Om du gör detta kommer du inte att förlora data, men du kommer fortfarande att faktureras för den storlek som används och får prestanda (baslinje IOPS, dataflöde och burst IOPS) för den etablerade resursen, inte den storlek som används.

I följande tabell visas några exempel på dessa formler för de etablerade resursstorlekarna:

|Kapacitet (GiB) | Baslinje IOPS | Burst IOPS | Utgående (MiB/s) | Inträngning (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Upp till 300     | 66   | 44   |
|500         | 500     | Upp till 1 500   | 90   | 60   |
|1,024       | 1,024   | Upp till 3 072   | 122   | 81   |
|5,120       | 5,120   | Upp till 15 360  | 368   | 245   |
|10,240      | 10,240  | Upp till 30 720  | 675 | 450   |
|33,792      | 33,792  | Upp till 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Upp till 100 000 | 3,132 | 2,088   |
|102,400     | 100 000 | Upp till 100 000 | 6,204 | 4,136   |

> [!NOTE]
> Filresurser prestanda är föremål för maskinnätverk gränser, tillgängliga nätverksbandbredd, IO storlekar, parallellism, bland många andra faktorer. Till exempel, baserat på interna tester med 8 KiB läs / skriv IO storlekar, en enda Windows virtuell dator, *Standard F16s_v2*, ansluten till premium filresurs över SMB kan uppnå 20K läsa IOPS och 15K skriva IOPS. Med 512 MiB läs/ skriv IO-storlekar kan samma virtuella dator uppnå 1,1 GiB/s utgående och 370 MiB/s ingående dataflöde. För att uppnå maximal prestandaskala sprider du belastningen över flera virtuella datorer. Se [felsökningsguiden](storage-troubleshooting-files-performance.md) för några vanliga prestandaproblem och lösningar.

#### <a name="bursting"></a>Spricker
Premium fil aktier kan brista sina IOPS upp till en faktor tre. Bursting är automatiserad och fungerar baserat på ett kreditsystem. Bursting fungerar på bästa sätt och burst gränsen är inte en garanti, filresurser kan brista *upp till* gränsen.

Krediter ackumuleras i en burst-bucket när trafiken för filresursen är lägre än baslinje IOPS. En 100 GiB-resurs har till exempel 100 baslinje-IOPS. Om den faktiska trafiken på resursen var 40 IOPS för ett visst 1-sekundersintervall krediteras 60 oanvända IOPS till en burst-bucket. Dessa krediter kommer sedan att användas senare när operationer skulle överstiga baslinjen IOPs.

> [!TIP]
> Sprängskopans storlek = Baslinje IOPS * 2 * 3600.

När en aktie överskrider baslinjen IOPS och har krediter i en burst-bucket, kommer den att brista. Aktier kan fortsätta att brista så länge krediter är kvar, men aktier mindre än 50 TiB kommer bara att stanna vid burst gränsen i upp till en timme. Aktier större än 50 TiB kan tekniskt överstiga denna entimme gräns, upp till två timmar, men detta är baserat på antalet burst krediter upplupna. Varje IO utöver baslinjen IOPS förbrukar en kredit och när alla krediter förbrukas skulle resursen återgå till baslinjen IOPS.

Aktiekrediter har tre delstater:

- Periodisering, när filresursen använder mindre än baslinjen IOPS.
- Avböjer när filresursen spricker.
- Återstående konstant, när det antingen inte finns några krediter eller baslinje IOPS används.

Nya filresurser börjar med det fulla antalet krediter i sin burst bucket. Burst-krediter kommer inte att periodiseras om resursen IOPS faller under baslinjen IOPS på grund av begränsning av servern.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standardfilresurser för att sträcka sig över upp till 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Begränsningar
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migrering
I många fall kommer du inte att upprätta en ny nettofilresurs för din organisation, utan i stället migrera en befintlig filresurs från en lokal filserver eller NAS-enhet till Azure Files. Det finns många verktyg, som tillhandahålls både av Microsoft och tredje part, för att göra en migrering till en filresurs, men de kan grovt delas in i två kategorier:

- **Verktyg som underhåller filsystemattribut som ACL:er och tidsstämplar:**
    - **[Azure File Sync](storage-sync-files-planning.md)**: Azure File Sync kan användas som en metod för att få in data i en Azure-filresurs, även när den önskade slutdistributionen inte är att upprätthålla en lokal närvaro. Azure File Sync kan installeras på befintliga distributioner av Windows Server 2012 R2, Windows Server 2016 och Windows Server 2019. En fördel med att använda Azure File Sync som en intvald mekanism är att slutanvändare kan fortsätta att använda den befintliga filresursen på plats. cut-over till Azure-filresursen kan inträffa när alla data har laddats upp i bakgrunden.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy är ett välkänt kopieringsverktyg som levereras med Windows och Windows Server. Robocopy kan användas för att överföra data till Azure-filer genom att montera filresursen lokalt och sedan använda den monterade platsen som mål i robocopy-kommandot.

- **Verktyg som inte underhåller filsystemattribut:**
    - **Data Box**: Data Box tillhandahåller en mekanism för överföring av offlinedata för fysiska leveransdata till Azure. Den här metoden är utformad för att öka dataflödet och spara bandbredd, men stöder för närvarande inte filsystemattribut som tidsstämplar och ACL:er.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: AzCopy är ett kommandoradsverktyg som utformats för att kopiera data till och från Azure-filer, samt Azure Blob-lagring, med hjälp av enkla kommandon med optimal prestanda.

## <a name="next-steps"></a>Nästa steg
* [Planera för en Azure File Sync-distribution](storage-sync-files-planning.md)
* [Distribuera Azure-filer](storage-files-deployment-guide.md)
* [Distribuera Synkronisering av Azure-filer](storage-sync-files-deployment-guide.md)
