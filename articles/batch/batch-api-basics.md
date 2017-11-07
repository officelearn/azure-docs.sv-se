---
title: "Översikt över Azure Batch för utvecklare | Microsoft Docs"
description: "Lär dig mer om funktionerna i Batch-tjänsten och dess API:er ur ett utvecklingsperspektiv."
services: batch
documentationcenter: .net
author: v-dotren
manager: timlt
editor: 
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 10/12/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e9f098bedf2c4dfb27a27d028b7bd87782516c7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Utveckla storskaliga parallella beräkningslösningar med Batch

I den här översikten över huvudkomponenterna i tjänsten Azure Batch diskuterar vi de viktigaste tjänstfunktionerna och resurserna som Batch-utvecklare kan använda för att skapa storskaliga parallella beräkningslösningar.

Oavsett om du utvecklar ett distribuerat beräkningsprogram eller en tjänst som skickar direkta [REST API][batch_rest_api]-API-anrop eller om du använder någon av [Batch SDK:erna](batch-apis-tools.md#azure-accounts-for-batch-development) så använder du många av resurserna och funktionerna som beskrivs i den här artikeln.

> [!TIP]
> En mer översiktlig introduktion till Batch-tjänsten finns i [Grunderna i Azure Batch](batch-technical-overview.md).
>
>

## <a name="batch-service-workflow"></a>Batch-tjänstens arbetsflöde
Följande allmänna arbetsflöde är typiskt i praktiskt taget alla program och tjänster som använder Batch-tjänsten för att bearbeta parallella arbetsbelastningar:

1. Ladda upp **datafilerna** som du vill bearbeta till ett [Azure Storage][azure_storage]-konto. Batch innehåller inbyggt stöd för åtkomst till Azure Blob Storage, och dina aktiviteter kan hämta dessa filer till [beräkningsnoder](#compute-node) när aktiviteterna körs.
2. Ladda upp **programfilerna** som dina aktiviteter ska köra. Dessa filer kan vara binärfiler eller skript och deras beroenden, och körs av aktiviteterna i dina jobb. Dina aktiviteter kan hämta dessa filer från ditt lagringskonto, eller så kan du använda funktionen för [programpaket](#application-packages) i Batch för att hantera och distribuera program.
3. Skapa en [pool](#pool) med beräkningsnoder. När du skapar en pool anger du antalet beräkningsnoder för poolen, deras storlek och operativsystemet. När varje aktivitet i jobbet körs tilldelas det och körs på en av noderna i poolen.
4. Skapa ett [jobb](#job). Ett jobb hanterar en samling aktiviteter. Du associerar varje jobb med en specifik pool där jobbets aktiviteter ska köras.
5. Lägg till [aktiviteter](#task) till jobbet. Varje aktivitet kör programmet eller skriptet som du laddade upp för att bearbeta datafilerna som den hämtar från ditt lagringskonto. När en aktivitet är klar kan den överföra sina utdata till Azure Storage.
6. Övervaka jobbförloppet och hämta aktivitetsresultatet från Azure Storage.

I följande avsnitt beskrivs dessa och andra resurser i Batch som lägger grunden till ditt distribuerade beräkningsscenario.

> [!NOTE]
> Du behöver ett [Batch-konto](#account) för att använda Batch-tjänsten. De flesta Batch-lösningar använder ett kopplat [Azure Storage][azure_storage]-konto för fillagring och filhämtning. 
>
>

## <a name="batch-service-resources"></a>Resurser i Batch-tjänsten
Några av följande resurser – konton, beräkningsnoder, pooler, jobb och aktiviteter – krävs av alla lösningar som använder Batch-tjänsten. Andra, till exempel jobbscheman och programpaket, är användbara men valfria funktioner.

* [Konto](#account)
* [Beräkningsnod](#compute-node)
* [Pool](#pool)
* [Jobb](#job)

  * [Jobbscheman](#scheduled-jobs)
* [Aktivitet](#task)

  * [Startaktivitet](#start-task)
  * [Job Manager-aktivitet](#job-manager-task)
  * [Jobbförberedelse- och jobbpubliceringsaktiviteter](#job-preparation-and-release-tasks)
  * [Aktivitet med flera instanser (MPI)](#multi-instance-tasks)
  * [Aktivitetsberoenden](#task-dependencies)
* [Programpaket](#application-packages)

## <a name="account"></a>Konto
Ett Batch-konto är en unikt identifierad entitet i Batch-tjänsten. All bearbetning är associerad med ett Batch-konto.

Du kan skapa ett Azure Batch-konto med [Azure Portal](batch-account-create-portal.md) eller programmässigt som med [Batch Management .NET-biblioteket](batch-management-dotnet.md). När du skapar kontot kan du koppla ett Azure-lagringskonto för att lagra jobbrelaterade in- och utdata eller program.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

> [!NOTE]
> När du skapar ett Batch-konto ska du normalt välja standardläget för **Batch-tjänsten**. Där allokeras pooler bakom kulisserna i Azure-hanterade prenumerationer. I det alternativa läget **Användarprenumeration**, som inte längre rekommenderas, skapas virtuella Batch-datorer och andra resurser direkt i prenumerationen när en pool skapas. Du måste koppla kontot till ett Azure Key Vault för att kunna skapa ett Batch-konto i läget Användarprenumeration.
>


## <a name="azure-storage-account"></a>Azure-lagringskonto

De flesta Batch-lösningar använder Azure Storage för lagring av resursfiler och utdatafiler.  

Batch stöder för närvarande endast allmänna lagringskontotyper, enligt beskrivningen i steg 5 i [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) i [Om lagringskonton i Azure](../storage/common/storage-create-storage-account.md). Batch-aktiviteterna (inklusive standardaktiviteter, startaktiviteter, jobbförberedelse- och jobbpubliceringsaktiviteter) måste definiera resursfiler som finns i allmänna lagringskonton.


## <a name="compute-node"></a>Beräkningsnod
En beräkningsnod är en virtuell Azure-dator eller molntjänstdator som är dedikerad för bearbetning av en del av ditt programs arbetsbelastning. Storleken på en nod avgör antalet CPU-kärnor, minneskapaciteten och storleken på det lokala filsystemet som allokeras till noden. Du kan skapa pooler för Windows- eller Linux-noder med Azure Cloud Services eller Marketplace-avbildningar för [Azure Virtual Machines][vm_marketplace] eller anpassade avbildningar du förbereder. Mer information om dessa alternativ finns i följande [poolavsnitt](#pool).

Noder kan köra alla körbara filer eller skript som stöds av nodens operativsystemmiljö, inklusive \*.exe-, \*.cmd-, \*.bat- och PowerShell-skript för Windows och binär-, shell- och Python-skript för Linux.

Alla beräkningsnoder i Batch innehåller också:

* En fördefinierad [mappstruktur](#files-and-directories) och associerade [miljövariabler](#environment-settings-for-tasks) som är tillgängliga som referens för aktiviteterna.
* **Brandväggsinställningar** som konfigureras för att styra åtkomsten.
* [Fjärråtkomst](#connecting-to-compute-nodes) till både Windows-noder (RDP, Remote Desktop Protocol) och Linux-noder (SSH, Secure Shell).

## <a name="pool"></a>Pool
En pool är en samling noder som ditt program körs på. Poolen kan skapas manuellt av dig eller automatiskt av Batch-tjänsten när du anger arbetet som ska utföras. Du kan skapa och hantera en pool som uppfyller resurskraven för ditt program. En pool kan endast användas av det Batch-konto som den skapades under. Ett Batch-konto kan ha mer än en pool.

Azure Batch-pooler baseras på den grundläggande Azure-beräkningsplattformen. De tillhandahåller storskalig allokering, programinstallation, datadistribution, övervakning av hälsotillstånd och flexibel justering av antalet beräkningsnoder i en pool ([skalning](#scaling-compute-resources)).

Varje nod som läggs till i en pool tilldelas ett unikt namn och en IP-adress. När en nod tas bort från en pool försvinner alla ändringar som gjorts i operativsystemet eller filer, och dess namn och IP-adress frisläpps för framtida användning. När en nod lämnar poolen är dess livslängd över.

När du skapar en pool kan du ange följande attribut:

- Beräkningsnodens operativsystem och version
- Typ av beräkningsnod och antal målnoder
- Beräkningsnodernas storlek
- Skalningsprincip
- Schemaläggningsprincip för aktiviteter
- Kommunikationsstatus för beräkningsnoder
- Startaktiviteter för beräkningsnoder
- Programpaket
- Nätverkskonfiguration

Dessa inställningar beskrivs mer ingående i avsnitten nedan.

> [!IMPORTANT]
> Batch-konton har en standardkvot som begränsar antalet kärnor i ett Batch-konto. Antalet kärnor motsvarar antalet beräkningsnoder. Du hittar standardkvoterna och instruktioner om hur du [ökar en kvot](batch-quota-limit.md#increase-a-quota) i [Kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md). Om din pool inte uppnår antalet målnoder kan det bero på kärnkvoten.
>


### <a name="compute-node-operating-system-and-version"></a>Beräkningsnodens operativsystem och version

När du skapar en Batch-pool kan du ange en konfiguration för virtuella Azure-datorer och ange på vilket operativsystem som du vill köra varje beräkningsnod i poolen. Det finns två typer av konfigurationer i Batch:

- **Konfiguration av virtuell dator**, som anger att poolen består av virtuella Azure-datorer. Dessa virtuella datorer kan skapas från Linux- eller Windows-avbildningar. 

    När du skapar en pool med noder i en konfiguration av en virtuell dator måste du inte bara ange storleken på noderna och källan för avbildningarna som användes för att skapa dem, utan även **referensen till avbildningen av den virtuella datorn** och Batch-**nodagentens SKU** som ska installeras på noderna. Mer information om hur du anger dessa poolegenskaper finns i [Etablera Linux-beräkningsnoder i Azure Batch-pooler](batch-linux-nodes.md). Om du vill kan du koppla en eller flera tomma datadiskar till en pool med virtuella datorer som skapats av Microsoft Azure Marketplace-avbildningar. Alternativt kan du inkludera datadiskar i de anpassade avbildningar som används för att skapa de virtuella datorerna.

- **Cloud Service-konfiguration**, som anger att poolen består av Azure Cloud Services-noder. Cloud Services tillhandahåller *endast* Windows-beräkningsnoder.

    Tillgängliga operativsystem för pooler med Cloud Services-konfiguration finns i [Kompatibilitetstabell för versioner av Azure-gästoperativsystem och SDK:er](../cloud-services/cloud-services-guestos-update-matrix.md). När du skapar en pool som innehåller Cloud Services-noder måste du ange nodstorleken och dess *operativsystemsfamilj*. Cloud Services distribueras snabbare till Azure än virtuella datorer som kör Windows. Om du vill ha pooler med Windows-beräkningsnoder får du bättre distributionstid med Cloud Services.

    * *Operativsystemfamiljen* avgör också vilka versioner av .NET som installeras med operativsystemet.
    * Precis som med arbetarroller i Cloud Services kan du ange en *operativsystemversion* (mer information om arbetarroller finns i avsnittet [Berätta mer om Cloud Services](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) i [Översikt över Cloud Services](../cloud-services/cloud-services-choose-me.md)).
    * Som med arbetarroller rekommenderar vi att du anger `*` för *operativsystemversionen* så att noderna uppgraderas automatiskt och så att inget extra arbete krävs för att hantera nya versioner. Det huvudsakliga skälet till att välja en viss operativsystemversion är att säkerställa programkompatibiliteten, så att du kan testa bakåtkompatibiliteten innan versionen uppdateras. Efter valideringen kan *operativsystemversionen* för poolen uppdateras och den nya operativsystemavbildningen kan installeras – eventuella aktiviteter som körs avbryts och placeras i kö.

När du skapar en pool måste du välja lämplig **nodeAgentSkuId**, beroende på vilket operativsystem din VHD-basavbildning har. Du kan hämta en mappning av tillgängliga nodagent-SKU-ID:n mot OS-avbildningsreferenserna genom att anropa [List Supported Node Agent SKUs](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus) (Visa nodagent-SKU:er som stöds).


#### <a name="custom-images-for-virtual-machine-pools"></a>Anpassade avbildningar för virtuell datorpooler

Om du vill använda en anpassad avbildning måste du förbereda avbildningen genom att generalisera den. Information om hur man förbereder anpassade Linux-avbildningar från virtuella Azure-datorer finns i [Så här skapar du en avbildning av en virtuell dator eller VHD](../virtual-machines/linux/capture-image.md). Information om att förbereda anpassade Windows-avbildningar från virtuella Azure-datorer finns i [Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md). 

Detaljerade krav och steg finns i [Use a custom image to create a pool of virtual machines](batch-custom-images.md) (Använda en anpassad avbildning för att skapa en pool med virtuella datorer).

#### <a name="container-support-in-virtual-machine-pools"></a>Stöd för behållare i pooler med virtuella datorer

När du skapar en pool med virtuella datorer med hjälp av Batch-API:er kan du ställa in poolen på att köra uppgifter i Docker-behållare. För närvarande måste du skapa poolen med hjälp av Windows Server 2016 Datacenter och en behållaravbildning från Microsoft Azure Marketplace. Alternativt kan du ange en anpassad VM-avbildning som innehåller Docker Community Edition och alla nödvändiga drivrutiner. Poolinställningarna måste innehålla en [behållarkonfiguration](/rest/api/batchservice/pool/add#definitions_containerconfiguration) som kopierar behållaravbildningar till de virtuella datorerna när poolen skapas. Uppgifter som körs i poolen kan sedan referera till behållaravbildningar och körningsalternativ för behållare.

## <a name="compute-node-type-and-target-number-of-nodes"></a>Typ av beräkningsnod och antal målnoder

När du skapar en pool kan du ange vilka typer av beräkningsnoder du vill ha och antal målnoder för var och en av dem. Det finns två typer av beräkningsnoder:

- **Dedikerade beräkningsnoder.** Dedikerade beräkningsnoder är reserverade för dina arbetsbelastningar. De kostar mer än noder med låg prioritet, men de avbryts aldrig.

- **Beräkningsnoder med låg prioritet.** Noder med låg prioritet utnyttjar överkapacitet i Azure för att köra Batch-arbetsbelastningar. Noder med låg prioritet är billigare per timme än dedikerade noder och gör att du kan använda arbetsbelastningar som kräver mycket beräkningskraft. Mer information finns i [Use low-priority VMs with Batch](batch-low-pri-vms.md) (Använda virtuella datorer med låg prioritet med Batch).

    Beräkningsnoder med låg prioritet kan avbrytas om det inte finns tillräckligt med överkapacitet. Om en nod avbryts när aktiviteter körs placeras aktiviteterna i kö igen och körs när en beräkningsnod blir tillgänglig igen. Noder med låg prioritet är ett bra alternativ för arbetsbelastningar om tiden för slutförande av jobbet är flexibelt och om arbetet är fördelat på flera noder. Innan du bestämmer dig för att använda noder med låg prioritet för ditt scenario måste du se till att arbetet som går förlorat på grund av avbrott är minimalt och enkelt att återskapa.

    
Du kan ha både noder med låg prioritet och dedikerade beräkningsnoder i samma pool. Varje typ av nod &mdash; låg prioritet och dedikerad &mdash; har en egen inställning för antal målnoder. 
    
Antalet beräkningsnoder kallas *mål* eftersom din pool i vissa fall kanske inte når det önskade antalet noder. Detta kan hända om poolen först nått [kärnkvoten](batch-quota-limit.md) för ditt Batch-konto. Det kan också hända att poolen inte når målantalet om du använder en formel för automatisk skalning för poolen som begränsar det högsta antalet noder.

Information om priser för beräkningsnoder med låg prioritet och dedikerade beräkningsnoder finns i [Batch-priser](https://azure.microsoft.com/pricing/details/batch/).

### <a name="size-of-the-compute-nodes"></a>Beräkningsnodernas storlek

Information om storleken på beräkningsnoder med **Cloud Services-konfiguration** finns i [Storlekar för Cloud Services](../cloud-services/cloud-services-sizes-specs.md). Batch stöder alla Cloud Services-storlekar utom `ExtraSmall`, `STANDARD_A1_V2` och `STANDARD_A2_V2`.

Information om storleken på beräkningsnoder med **VM-konfiguration** finns i [Storlekar för virtuella datorer i Azure](../virtual-machines/linux/sizes.md) (Linux) och [Storlekar för virtuella datorer i Azure](../virtual-machines/windows/sizes.md) (Windows). Batch har stöd för alla storlekar för virtuella datorer i Azure utom `STANDARD_A0` och de med Premium Storage (`STANDARD_GS`, `STANDARD_DS`- och `STANDARD_DSV2`-serien).

När datorns nodstorlek beräknas bör egenskaperna och kraven hos programmen som ska köras på noderna betraktas. Det är lämpligt att ha i åtanke när du väljer den lämpligaste och mest kostnadseffektiva nodstorleken huruvida programmet är flertrådat och hur mycket minne det förbrukar. Du väljer normalt en nodstorlek under antagandet att en aktivitet i taget ska köras på noden. Du kan dock välja att flera aktiviteter, och därmed flera programinstanser, ska kunna [köras parallellt](batch-parallel-node-tasks.md) på datornoder i samband med utförandet av åtgärder. I det här fallet är det vanligt att välja en större nod för att hantera det utökade behovet av att köra åtgärder parallellt. Se [Schemaläggningsprincip](#task-scheduling-policy) för mer information.

Alla noderna i en pool har samma storlek. Om du avser att köra program med olika systemkrav och/eller belastningsnivåer rekommenderar vi att du använder olika pooler.

### <a name="scaling-policy"></a>Skalningsprincip

När det gäller dynamiska arbetsbelastningar kan du skriva och använda en [formel för automatisk skalning](#scaling-compute-resources) en pool. Batch-tjänsten utvärderar med jämna mellanrum din formel och justerar antalet noder i poolen baserat på olika pool-, jobb- och aktivitetsparametrar som du kan ange.

### <a name="task-scheduling-policy"></a>Schemaläggningsprincip för aktiviteter

Konfigurationsalternativet för [högsta antal aktiviteter per nod](batch-parallel-node-tasks.md) anger det högsta antal aktiviteter som kan köras parallellt på varje beräkningsnod i poolen.

Standardkonfigurationen specificerar att en aktivitet i taget ska köras på en nod, men det finns scenarier där det kan vara bra om två eller fler aktiviteter kan köras samtidigt på en nod. Information om hur du kan dra fördel av flera aktiviteter per nod finns i [exempelscenariot](batch-parallel-node-tasks.md#example-scenario) i artikeln om [samtidiga nodaktiviteter](batch-parallel-node-tasks.md).

Du kan också ange en *fyllningstyp* som bestämmer om Batch-tjänsten sprider ut aktiviteterna jämnt över alla noder i en pool eller om den distribuerar det högsta antalet aktiviteter till varje nod innan aktiviteter tilldelas till en annan nod.

### <a name="communication-status-for-compute-nodes"></a>Kommunikationsstatus för beräkningsnoder

I de flesta fall körs aktiviteterna oberoende av varandra och behöver inte kommunicera med varandra. Det kan dock finnas program där aktiviteterna måste kommunicera (till exempel i [MPI-scenarier](batch-mpi.md)).

Du kan konfigurera en pool att tillåta **internodkommunikation**, så att noderna i en pool kan kommunicera under körning. När kommunikation mellan noder är aktiverat kan noderna i pooler med Cloud Services-konfiguration kommunicera med varandra på portar som är större än 1100, och pooler med VM-konfiguration begränsar inte trafiken på någon port.

Observera att nodernas placering i kluster kan påverkas om du aktiverar kommunikation mellan noder, samt att det högsta antalet noder i en pool kan begränsas på grund av distributionsrestriktioner. Om programmet inte kräver kommunikation mellan noder kan Batch-tjänsten allokera ett potentiellt stort antal noder till poolen från många olika kluster och datacenter för ökad parallell bearbetningskapacitet.

### <a name="start-tasks-for-compute-nodes"></a>Startaktiviteter för beräkningsnoder

Den valfria *startaktiviteten* körs på varje nod när noden ansluter till poolen, samt varje gång noden startas om och varje gång dess avbildning återställs. Startaktiviteten är särskilt användbar för att förbereda beräkningsnoder för körningen av aktiviteter, till exempel installationen av de program som aktiviteterna kör på beräkningsnoderna.

### <a name="application-packages"></a>Programpaket

Du kan ange [programpaket](#application-packages) som ska distribueras till beräkningsnoderna i poolen. Programpaket underlättar distributionen och versionshanteringen för de program som dina aktiviteter kör. Programpaket som du anger för en pool installeras på varje nod som ansluter till den poolen, samt varje gång en nod startas om och varje gång nodens avbildning återställs.

> [!NOTE]
> Programpaket kan användas för alla Batch-pooler som skapats efter 5 juli 2017. De kan användas för Batch-pooler som skapats mellan 10 mars 2016 och 5 juli 2017, men endast om poolen skapades med en molntjänstkonfiguration. Programpaket kan inte användas för Batch-pooler som har skapats före 10 mars 2016. Mer information om hur du använder programpaket för att distribuera program till dina Batch-noder finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
>
>

### <a name="network-configuration"></a>Nätverkskonfiguration

Du kan ange undernätet för ett [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md) i Azure där poolens beräkningsnoder ska skapas. Mer information finns i avsnittet om [nätverkskonfiguration för pooler](#pool-network-configuration).


## <a name="job"></a>Jobb
Ett jobb är en samling aktiviteter. Det hanterar hur beräkningen utförs av dess aktiviteter på beräkningsnoderna i en pool.

* Jobbet anger i vilken **pool** som arbetet ska köras. Du kan skapa en ny pool för varje jobb eller använda en pool för många jobb. Du kan skapa en pool för varje jobb som associeras med ett jobbschema eller för alla jobb som associeras med ett jobbschema.
* Du kan ange en valfri **jobbprioritet**. När ett jobb skickas med en högre prioritet än pågående jobb placeras aktiviteterna för jobbet med högre prioritet i kön före aktiviteter för jobben med lägre prioritet. Aktiviteter med lägre prioritet som redan körs avbryts inte.
* Du kan använda **jobbegränsningar** för att ange vissa begränsningar för dina jobb:

    Du kan ställa in en **högsta wall-clock-tid** så att ett jobb och alla dess aktiviteter avslutas om jobbet körs under längre tid än den angivna wall-clock-tiden.

    Batch kan identifiera och försöka köra aktiviteter som misslyckats igen. Du kan ange det **högsta antalet omförsök för aktiviteter** som en begränsning. Du kan också ange om nya försök *alltid* eller *aldrig* ska göras för en aktivitet. Omförsök innebär att aktiviteten placeras i körningskön igen.
* Klientprogrammet kan lägga till aktiviteter till ett jobb eller så kan du ange en [Job Manager-aktivitet](#job-manager-task). En Job Manager-aktivitet innehåller den information som krävs för att skapa de nödvändiga aktiviteterna för ett jobb, där Job Manager-aktiviteten körs på en av beräkningsnoderna i poolen. Job Manager-aktiviteten hanteras av Batch – den placeras i kö så fort jobbet skapas och startas om ifall den misslyckas. En Job Manager-aktivitet *krävs* för jobb som skapas av ett [jobbschema](#scheduled-jobs) eftersom det är det enda sättet att definiera aktiviteterna innan jobbet instantieras.
* Som standard är jobben fortfarande i aktivt läge när alla aktiviteter i jobbet har slutförts. Du kan ändra det här beteendet så att jobbet avbryts automatiskt när alla aktiviteter i jobbet har slutförts. Ange jobbets **onAllTasksComplete**-egenskap ([OnAllTasksComplete][net_onalltaskscomplete] i Batch .NET) till *terminatejob* så att jobbet avbryts automatiskt när alla dess aktiviteter har slutförts.

    Observera att Batch-tjänsten betraktar alla aktiviteter i ett jobb som slutförda om jobbet inte har *några* aktiviteter. Därför används det här alternativet oftast med en [Job Manager-aktivitet](#job-manager-task). Om du vill avsluta jobben automatiskt utan en jobbhanterare börjar du med att ange **onAllTasksComplete**-egenskapen för ett nytt jobb till *noaction* och anger den sedan till *terminatejob* efter att du har lagt till aktiviteterna till jobbet.

### <a name="job-priority"></a>Jobbprioritet
Du kan tilldela en prioritet till jobb som du skapar i Batch. Batch-tjänsten använder prioritetsvärdet för jobbet för att fastställa ordningen för schemaläggningen av jobb inom ett konto (detta ska inte förväxlas med ett [schemalagt jobb](#scheduled-jobs)). Prioritetsvärdesintervallet är -1 000 till 1 000, där -1 000 är lägst prioritet och 1 000 högst. Om du vill uppdatera ett jobbs prioritet så anropa åtgärden [Uppdatera egenskaperna för ett jobb][rest_update_job] (Batch REST) eller ändra egenskapen [CloudJob.Priority][net_cloudjob_priority] (Batch .NET).

Inom samma konto har jobb med högre prioritet schemaläggningsprioritet framför jobb med lägre prioritet. Ett jobb med ett högre prioritetsvärde i ett konto har inte schemaläggningsprioritet över ett annat jobb med ett lägre prioritetsvärde i ett annat konto.

Schemaläggningen av jobb mellan pooler är oberoende av varandra. Mellan olika pooler är det inte säkert att ett jobb med högre prioritet schemaläggs först om dess associerade pool har ont om inaktiva noder. I samma pool har jobb med samma prioritetsnivå samma chans att schemaläggas.

### <a name="scheduled-jobs"></a>Schemalagda jobb
Med [jobbscheman][rest_job_schedules] kan du skapa återkommande jobb i Batch-tjänsten. Ett jobbschema anger när jobb ska köras och innehåller specifikationerna för jobben som ska köras. Du kan ange schemats varaktighet – hur länge och när schemat gäller – och hur ofta jobb skapas under den schemalagda perioden.

## <a name="task"></a>Aktivitet
En aktivitet är en beräkningsenhet som associeras med ett jobb. Den körs på en nod. Aktiviteter tilldelas till en nod för körning eller placeras i kö tills en nod blir ledig. Enkelt beskrivet kör en aktivitet ett eller flera program eller skript på en beräkningsnod för att utföra det arbete som du vill ha gjort.

När du skapar en aktivitet kan du ange:

* Uppgiftens **kommandoraden**. Det här är kommandoraden som kör ditt program eller skript på beräkningsnoden.

    Det är viktigt att notera att kommandoraden egentligen inte körs under ett gränssnitt. Därför har den inte inbyggt stöd för shell-funktioner som tillägg i [miljövariabler](#environment-settings-for-tasks) (till exempel `PATH`). Om du vill dra nytta av den här typen av funktioner måste du anropa gränssnittet på kommandoraden, till exempel genom att starta `cmd.exe` på Windows-noder eller `/bin/sh` på Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Om dina aktiviteter måste köra ett program eller skript som inte finns i nodens `PATH` eller referensmiljövariabler anropar du gränssnittet uttryckligen på aktivitetens kommandorad.
* **Resursfiler** som innehåller de data som ska bearbetas. Dessa filer kopieras automatiskt till noden från Blob Storage i ett allmänt Azure Storage-konto innan uppgiftens kommandorad körs. Mer information finns i avsnitten [Startaktivitet](#start-task) och [Filer och kataloger](#files-and-directories).
* **Miljövariablerna** som krävs av programmet. Mer information finns i avsnittet [Miljöinställningar för aktiviteter](#environment-settings-for-tasks).
* **Begränsningarna** som aktiviteten ska köras under. Begränsningarna omfattar t.ex. den längsta tid som aktiviteten kan köras, det högsta antal omförsök som ska göras för en aktivitet som misslyckats, samt den längsta tid som filer i aktivitetens arbetskatalog ska bevaras.
* **Programpaket** för distribution till beräkningsnoden som aktiviteten har schemalagts att köra på. Med [programpaket](#application-packages) blir det lättare att distribuera och hantera versionerna av de program som dina aktiviteter kör. Programpaket på aktivitetsnivå är särskilt användbara i miljöer med delade pooler där olika jobb körs i en pool och där poolen inte tas bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
* En **behållaravbildning** refererar till Docker Hub eller ett privat register och andra inställningar som behövs för att skapa en Docker-behållare där uppgifter kan köras på noden. Den här informationen behöver du bara ange om poolen har skapats med en behållarkonfiguration.

Utöver de aktiviteter som du definierar för att utföra beräkningen på en nod tillhandahålls även följande särskilda aktiviteter av Batch-tjänsten:

* [Startaktivitet](#start-task)
* [Job Manager-aktivitet](#job-manager-task)
* [Jobbförberedelse- och jobbpubliceringsaktiviteter](#job-preparation-and-release-tasks)
* [Aktiviteter med flera instanser (MPI)](#multi-instance-tasks)
* [Aktivitetsberoenden](#task-dependencies)

### <a name="start-task"></a>Startaktivitet
Genom att associera en **startaktivitet** med en pool kan du förbereda driftsmiljön för dess noder. Du kan t.ex. installera de program som dina aktiviteter ska köra eller starta bakgrundsprocesser. Startaktiviteten körs varje gång en nod startar så länge den finns kvar i poolen – även när noden läggs till i poolen, när den startas om eller när dess avbildning återställs.

En viktig fördel med startaktiviteten är att den kan innehålla all information som krävs för att konfigurera en beräkningsnod och installera de program som krävs för att köra aktiviteterna. Att öka antalet noder i en pool är därför lika enkelt som att ange antalet nya målnoder. Startuppgiften förser batchtjänsten med den information som behövs för att konfigurera nya noder och förbereda dem för att acceptera uppgifter.

Som med andra Azure Batch-aktiviteter kan du ange en lista med **resursfiler** i [Azure Storage][azure_storage], förutom den **kommandorad** som ska köras. Batchtjänsten kopierar först resursfilerna till noden från Azure Storage och kör sedan kommandoraden. När det gäller en pools startaktivitet innehåller fillistan vanligtvis aktivitetens program och dess beroenden.

Startuppgiften kan dock även innehålla referensdata som ska användas av alla aktiviteter som körs på beräkningsnoden. En startaktivitets kommandorad kan exempelvis köra en `robocopy`-åtgärd för att kopiera programfiler (som anges som resursfiler och laddas ned till noden) från startaktivitetens [arbetskatalog](#files-and-directories) till den [delade mappen](#files-and-directories) och sedan köra en MSI eller `setup.exe`.

Normalt bör Batch-tjänsten vänta tills startaktiviteten har slutförts innan noden tilldelas aktiviteter, men detta kan konfigureras vid behov.

Om startaktiviteten misslyckas på en beräkningsnod uppdateras nodens status med feltillståndet och noden är inte tilldelad till någon aktivitet. Startaktiviteten kan misslyckas om det inte går att kopiera aktivitetens resursfiler från lagringen, eller om processen som körs av aktivitetens kommandorad returnerar en slutkod som inte är noll.

Om du lägger till eller uppdaterar startaktiviteten för en befintlig pool måste du starta om dess beräkningsnoder för att startaktiviteten ska tillämpas på noderna.

>[!NOTE]
> Den totala storleken på en startaktivitet måste vara mindre än eller lika med 32768 tecken, inklusive resursfiler och miljövariabler. Du kan använda en av följande två metoder för att säkerställa att startaktiviteten uppfyller detta krav:
>
> 1. Du kan använda programpaket för att distribuera program eller data i varje nod i Batch-poolen. Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
> 2. Du kan manuellt skapa ett zippat arkiv som innehåller dina programfiler. Ladda upp ditt zippade arkiv till Azure Storage som en blob. Ange det zippade arkivet som en resursfil för startaktiviteten. Innan du kör kommandoraden för din startaktivitet packar du upp arkivet från kommandoraden. 
>
>    Du kan packa upp arkivet med valfritt arkiveringsverktyg. Du måste ta med ett verktyg för att packa upp arkivet som en resursfil för startaktiviteten.
>
>

### <a name="job-manager-task"></a>Job Manager-aktivitet
Du använder vanligtvis en **Job Manager-aktivitet** för att styra och/eller övervaka körningen av jobb, till exempel för att skapa och skicka aktiviteterna för ett jobb, fastställa ytterligare aktiviteter som ska köras samt för att avgöra när arbetet är klart. En Job Manager-aktivitet är dock inte begränsad till dessa aktiviteter. Det är en fullständig aktivitet som kan köra alla åtgärder som krävs för jobbet. Den kan till exempel ladda ned en fil som angetts som en parameter, analysera innehållet i filen och skicka fler aktiviteter baserat på innehållet.

En Job Manager-aktivitet startar innan alla andra aktiviteter. Den tillhandahåller följande funktioner:

* Den skickas automatiskt som en aktivitet av Batch-tjänsten när jobbet skapas.
* Den är schemalagd att köras innan andra aktiviteter i ett jobb.
* Dess associerade nod är den sista som tas bort från en pool om poolens storlek minskas.
* Dess avslut kan vara kopplat till avslutet av alla aktiviteter i jobbet.
* En Job Manager-aktivitet får högsta prioritet om den behöver startas om. Om en inaktiv nod inte är tillgänglig kan Batch-tjänsten avsluta någon av de andra aktiviteterna som körs i poolen för att göra plats för Job Manager-aktiviteten så att den kan köras.
* En Job Manager-aktivitet i ett jobb har inte prioritet över aktiviteter i andra jobb. Mellan jobb gäller endast prioriteringar på jobbnivå.

### <a name="job-preparation-and-release-tasks"></a>Jobbförberedelse- och jobbpubliceringsaktiviteter
Batch innehåller jobbförberedelseaktiviteter för konfiguration av jobb före körningen. Jobbpubliceringsaktiviteter är avsedda för underhåll och rensning när jobbet har körts.

* **Jobbförberedelseaktivitet**: En jobbförberedelseaktivitet körs på alla beräkningsnoder som schemalagts att köra aktiviteter, innan andra jobbaktiviteter körs. Du kan till exempel använda en jobbförberedelseaktivitet för att kopiera data som delas av alla aktiviteter, men som är unika för jobbet.
* **Jobbpubliceringsaktivitet**: När ett jobb har slutförts körs en jobbpubliceringsaktivitet på alla noder i poolen som har kört minst en aktivitet. Du kan till exempel använda en jobbpubliceringsaktivitet om du vill ta bort data som kopieras av jobbförberedelseaktiviteten, eller för att komprimera och ladda upp loggar med diagnostikdata.

Du kan ange en kommandorad som ska köras när aktiviteten anropas både med jobbförberedelse- och jobbpubliceringsaktiviteter. De stöder funktioner som filhämtning, upphöjd körning, anpassade miljövariabler, högsta körningstid, antal omförsök och kvarhållningstid för filer.

Mer information om jobbförberedelse- och jobbpubliceringsaktiviteter finns i [Köra jobbförberedelse- och jobbpubliceringsaktiviteter på Azure Batch-beräkningsnoder](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Aktivitet med flera instanser
En [aktivitet med flera instanser](batch-mpi.md) är en aktivitet som är konfigurerad att köras på mer än en beräkningsnod samtidigt. Aktiviteter med flera instanser stöder scenarier för databehandling med höga prestanda som kräver en grupp med beräkningsnoder som allokeras tillsammans för att bearbeta en enskild arbetsbelastning (t.ex. MPI, Message Passing Interface).

Detaljerad information om hur du kör MPI-jobb i Batch med hjälp av Batch .NET-biblioteket finns i [Använda aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Aktivitetsberoenden
Med [aktivitetsberoenden](batch-task-dependencies.md) kan du, som namnet antyder, ange att en aktivitet är beroende av att andra aktiviteter slutförs innan den kan köras. Den här funktionen har stöd för situationer där en ”underordnad” aktivitet använder utdata från en ”överordnad” aktivitet, eller där en överordnad aktiviteten utför viss initiering som krävs av en underordnad aktivitet. Innan du kan använda den här funktionen måste du aktivera aktivitetsberoenden i Batch-jobbet. För varje aktivitet som är beroende av en annan (eller flera andra) anger du sedan de aktiviteter som den aktiviteten är beroende av.

Med aktivitetsberoenden kan du konfigurera scenarier som följande:

* *aktivitetB* är beroende av *aktivitetA* (*aktivitetB* kan inte köra förrän *aktivitetA* har slutförts).
* *aktivitetC* är beroende av både *aktivitetA* och *aktivitetB*.
* *aktivitetD* är beroende av en serie aktiviteter, till exempel aktivitet *1* till och med *10*, innan den kan köras.

Mer detaljerad information om den här funktionen finns i [Aktivitetsberoenden i Azure Batch](batch-task-dependencies.md) och i kodexemplet [TaskDependencies][github_sample_taskdeps] i [azure-batch-samples][github_samples]-databasen på GitHub.

## <a name="environment-settings-for-tasks"></a>Miljöinställningar för aktiviteter
Varje aktivitet som utförs av Batch-tjänsten har åtkomst till miljövariabler som anges på datornoder. Detta inkluderar miljövariabler som definieras av Batch-tjänsten ([-definierade][msdn_env_vars]) och anpassade miljövariabler som du kan definiera för dina aktiviteter. Programmen och skripten som dina aktiviteter utför har åtkomst till dessa miljövariabler under körning.

Du kan ange anpassade miljövariabler på aktivitets- eller jobbnivå genom att fylla i egenskapen för *miljöinställningar* för dessa entiteter. Se till exempel åtgärden [Lägg till en aktivitet till ett jobb][rest_add_task] (Batch REST API) eller egenskaperna [CloudTask.EnvironmentSettings][net_cloudtask_env] och [CloudJob.CommonEnvironmentSettings][net_job_env] i Batch .NET.

Klientprogrammet eller tjänsten kan hämta en aktivitets miljövariabler, både tjänstdefinierade och anpassade, med hjälp av åtgärden [Hämta information om en aktivitet][rest_get_task_info] (Batch REST) eller genom att komma åt egenskapen [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET). Processer som körs på en beräkningsnod kan komma åt dessa och andra miljövariabler på noden, till exempel genom att använda vanlig `%VARIABLE_NAME%`- (Windows) eller `$VARIABLE_NAME`-syntax (Linux).

Du hittar en fullständig lista över alla definierade miljövariabler i [Beräkna nodmiljövariabler][msdn_env_vars].

## <a name="files-and-directories"></a>Filer och kataloger
Varje aktivitet har en *arbetskatalog* där den skapar inga eller flera filer och kataloger. Den här arbetskatalogen kan användas för att lagra programmet som körs av aktiviteten, de data som bearbetas och resultatet från bearbetningen. Alla filer och kataloger för en aktivitet ägs av aktivitetsanvändaren.

Batch-tjänsten exponerar en del av filsystemet på en nod som *rotkatalogen*. Aktiviteter kan komma åt rotkatalogen genom att referera till `AZ_BATCH_NODE_ROOT_DIR`-miljövariabeln. Mer information om hur du använder miljövariabler finns i [Miljöinställningar för aktiviteter](#environment-settings-for-tasks).

Rotkatalogen innehåller följande katalogstruktur:

![Katalogstrukturen på beräkningsnoder][1]

* **shared**: Den här katalogen ger läs-/skrivbehörighet till *alla* aktiviteter som körs på en nod. Alla aktiviteter som körs på noden kan skapa, läsa, uppdatera och ta bort filer i den här katalogen. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_NODE_SHARED_DIR`-miljövariabeln.
* **startup**: Den här katalogen används av en startaktivitet som dess arbetskatalog. Alla filer som laddas ned till noden av startaktiviteten lagras här. Startaktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_NODE_STARTUP_DIR`-miljövariabeln.
* **Aktiviteter**: En katalog skapas för varje aktivitet som körs på noden. Aktiviteter kan komma åt den här katalogen genom att referera till `AZ_BATCH_TASK_DIR`-miljövariabeln.

    I varje aktivitetskatalog skapar Batch-tjänsten en arbetskatalog (`wd`) vars unika sökväg anges av `AZ_BATCH_TASK_WORKING_DIR`-miljövariabeln. Den här katalogen ger läs-/skrivbehörighet till aktiviteten. Aktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen. Den här katalogen lagras baserat på *RetentionTime*-begränsningen som angetts för aktiviteten.

    `stdout.txt`och `stderr.txt`: Dessa filer skrivs till aktivitetsmappen när aktiviteten körs.

> [!IMPORTANT]
> När en nod tas bort från poolen tas *alla* filer som lagras på noden bort.
>
>

## <a name="application-packages"></a>Programpaket
Funktionen för [programpaket](batch-application-packages.md) gör det enkelt att hantera och distribuera program till beräkningsnoder i dina pooler. Du kan överföra och hantera flera versioner av programmet som körs av dina aktiviteter, inklusive deras binär- och stödfiler. Du kan sedan automatiskt distribuera ett eller flera av dessa program till beräkningsnoderna i poolen.

Du kan ange programpaket på pool- och aktivitetsnivå. När du anger programpaket för pooler distribueras programmet till varje nod i poolen. När du anger programpaket för aktiviteter distribueras programmet endast till noder som är schemalagda att köra minst en av jobbets aktiviteter, precis innan aktivitetens kommandorad körs.

Batch hanterar detaljerna kring lagringen av programpaket i Azure Storage och deras distribution till beräkningsnoder, vilket förenklar både kod- och hanteringsarbetet.

Mer information om programpaketsfunktionen finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

> [!NOTE]
> Om du lägger till programpaket för pooler till en *befintlig* pool måste du starta om dess beräkningsnoder för att programpaketen kan distribueras till noderna.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Livslängd för pooler och beräkningsnoder
När du utvecklar din Azure Batch-lösning måste du tänka igenom hur och när pooler ska skapas och hur länge beräkningsnoderna i poolerna ska vara tillgängliga.

Ett alternativ är att skapa en pool för varje jobb som du skickar och sedan ta bort poolen så fort aktiviteterna har slutförts. Med den här metoden maximeras användningen eftersom noderna bara tilldelas när det är absolut nödvändigt och stängs ned så fort de blir inaktiva. Detta innebär visserligen att jobbet måste vänta tills noderna har allokerats, men det är viktigt att notera att aktiviteterna schemaläggs till noder så fort de är individuellt tillgängliga och startuppgiften har slutförts. Batch väntar *inte* tills alla noderna i en pool är tillgängliga innan aktiviteterna tilldelas till noderna. vilket säkerställer maximal användning av alla tillgängliga noder.

Å andra sidan, om det är högsta prioritet att jobben startar direkt, kan en pool skapas i förväg och dess noder göras tillgängliga innan jobben skickas. I det här scenariot kan aktiviteterna starta direkt, men noderna kan vara inaktiva medan de väntar på att aktiviteter ska tilldelas.

En kombinerad metod, som normalt används för att hantera en varierande men kontinuerlig belastning. är att ha en pool som flera jobb skickas till, men skala upp eller ned antalet noder baserat på arbetsbelastningen (se [Skala beräkningsresurser](#scaling-compute-resources) i avsnittet nedan). Detta kan göras reaktivt baserat på den aktuella belastningen eller proaktivt om det går att förutse belastningen.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Konfiguration av virtuella nätverk (VNet) och brandväggar 

När du etablerar en pool av beräkningsnoder i Batch kan du associera poolen med ett undernät för ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).  

### <a name="vnet-requirements"></a>VNet-krav
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Läs mer om hur du konfigurerar en Batch-pool i ett VNet i [Create a pool of virtual machines with your virtual network](batch-virtual-network.md) (Skapa en pool av virtuella datorer med det virtuella nätverket).

## <a name="scaling-compute-resources"></a>Skala beräkningsresurser
Med [automatisk skalning](batch-automatic-scaling.md) kan Batch-tjänsten dynamiskt justera antalet beräkningsnoder i en pool baserat på den aktuella arbetsbelastningen och resursanvändningen i beräkningsscenariot. På så sätt kan du sänka den totala kostnaden för programkörning genom att endast använda de resurser som du behöver och frisläppa de som du inte behöver.

Du aktiverar automatisk skalning genom att skriva en [formel för automatisk skalning](batch-automatic-scaling.md#automatic-scaling-formulas) och associera formeln med en pool. Batch-tjänsten använder formeln för att bestämma antalet noder i poolen för nästa skalningsintervall (ett intervall som du kan konfigurera). Du kan ange inställningarna för automatisk skalning för en pool när du skapar den eller aktivera skalning för en pool senare. Du kan också uppdatera skalningsinställningarna för en skalningsaktiverad pool.

Exempelvis kanske ett jobb kräver att du skickar ett mycket stort antal aktiviteter som ska köras. Du kan tilldela en skalningsformel till poolen som justerar antalet noder i poolen baserat på antalet köade aktiviteter och aktiviteternas slutförandefrekvens. Batch-tjänsten utvärderar med jämna mellanrum formeln och ändrar storlek på poolen baserat på arbetsbelastningen och dina övriga formelinställningar. Tjänsten lägger till noder efter behov när det finns ett stort antal köade aktiviteter och tar bort noder när det inte finns några uppgifter i kö eller inte körs några uppgifter.

En skalningsformel kan baseras på följande mått:

* **Tidsmått** baseras på statistik som samlas in var femte minut under angivet antalet timmar.
* **Resursmått** baseras på processoranvändning, bandbreddsanvändning, minnesanvändning och antalet noder.
* **Aktivitetsmått** baseras på aktivitetens tillstånd, t.ex. *Aktiv* (köad), *Körs* eller *Slutförd*.

Om den automatiska skalningen minskar antalet beräkningsnoder i en pool måste du bestämma hur pågående aktiviteter ska hanteras vid nedskalningen. I Batch kan detta kan hanteras med en *nodavallokeringsprincip* som du lägger till i formeln. Du kan till exempel ange att pågående aktiviteter ska stoppas direkt och sedan placeras i kö för att köras på en annan nod eller att de ska slutföras innan noden tas bort från poolen.

Mer information om automatisk skalning av program finns i [Skala beräkningsnoder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md).

> [!TIP]
> Du kan maximera användningen av beräkningsresurserna genom att ange målantalet noder till noll i slutet av ett jobb, men tillåta att aktiviteter som körs slutförs.
>
>

## <a name="security-with-certificates"></a>Säkerhet med certifikat
Vanligtvis måste du använda certifikat när du krypterar eller avkrypterar känslig aktivitetsinformation, till exempel nyckeln för ett [Azure Storage-konto][azure_storage]. För detta ändamål kan du installera certifikat på noderna. Krypterade hemligheter skickas till aktiviteter via kommandoradsparametrar eller bäddas in i någon av aktivitetsresurserna, och de installerade certifikaten kan användas för att dekryptera dem.

Du använder åtgärden [Lägg till certifikat][rest_add_cert] (Batch REST) eller metoden [CertificateOperations.CreateCertificate][net_create_cert] (Batch .NET) för att lägga till ett certifikat till ett Batch-konto. Därefter kan du associera certifikatet med en ny eller befintlig pool. När ett certifikat associeras med en pool installeras certifikatet av Batch-tjänsten på varje nod i poolen. Batch-tjänsten installerar relevanta certifikat när noden startar, innan andra aktiviteter startas (inklusive Job Manager- och startaktiviteter).

Om du lägger till certifikat till en *befintlig* pool måste du starta om dess beräkningsnoder för att certifikaten ska tillämpas på noderna.

## <a name="error-handling"></a>Felhantering
Du kan behöva hantera både aktivitets- och programfel i din Batch-lösning.

### <a name="task-failure-handling"></a>Hantering av aktivitetsfel
Aktivitetsfel kan delas in i följande kategorier:

* **Förbearbetningsfel**

    Om det inte går att starta en aktivitet anges ett förbearbetningsfel för aktiviteten.  

    Förbearbetningsfel kan till exempel bero på att aktivitetens resursfiler har flyttats, att Storage-kontot inte längre är tillgängligt eller på ett annat problem som gjorde att det inte gick att kopiera filer till noden.

* **Filöverföringsfel**

    Om överföringen av filer som anges för en aktivitet misslyckas märks aktiviteten med ett filöverföringsfel.

    Filöverföringsfel kan inträffa om signaturen för delad åtkomst (SAS) som angetts för att få åtkomst till Azure Storage är felaktig eller om den inte ger skrivbehörighet, om lagringskontot inte längre är tillgängligt eller om det uppstår något annat problem som gör att filerna inte kan kopieras från noden.    

* **Programfel**

    Processen som definieras av aktivitetens kommandorad kan också misslyckas. Processen misslyckas om en slutkod som inte är noll returneras av processen som körs av aktiviteten (se *Slutkoder för aktiviteter* i nästa avsnitt).

    För programfel kan du konfigurera Batch så att nya försök görs automatiskt upp till ett angivet antal försök.

* **Begränsningsfel**

    Du kan definiera ett villkor som anger den högsta körningstiden för ett jobb eller en aktivitet, *maxWallClockTime*. Detta kan vara användbart för att avsluta aktiviteter som har hängt sig.

    När den högsta körningstiden överskrids markeras aktiviteten som *slutförd* men slutkoden är `0xC000013A` och fältet *schedulingError* markeras som `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Felsöka programfel
* `stderr` och `stdout`

    När ett program körs kan det generera diagnostikutdata som du kan använda för att felsöka problem. Som vi nämnde i avsnittet [Filer och kataloger](#files-and-directories) ovan skriver Batch-tjänsten standardutdata och standardfelutdata till `stdout.txt`- och `stderr.txt`-filer i aktivitetskatalogen på beräkningsnoden. Du kan ladda ned dessa filer med hjälp av Azure-portalen eller något av Batch-SDK:erna. Du kan till exempel hämta dessa och andra filer för felsökningsändamål genom att använda [ComputeNode.GetNodeFile][net_getfile_node] och [CloudTask.GetNodeFile][net_getfile_task] i Batch .NET-biblioteket.

* **Slutkoder för aktiviteter**

    Som vi nämnt tidigare markerar Batch-tjänsten processen som misslyckad om processen som körs av aktiviteten returnerar en slutkod som inte är noll. När en aktivitet kör en process lägger Batch till *processens returkod* i aktivitetens slutkodsegenskap. Observera att en uppgifts slutkod **inte** bestäms av Batch-tjänsten. En uppgifts slutkod bestäms av processen i sig eller av det operativsystem där processen körs.

### <a name="accounting-for-task-failures-or-interruptions"></a>Ta med aktivitetsfel eller avbrott i beräkningen
Aktiviteter kan ibland misslyckas eller avbrytas. Själva aktivitetsprogrammet kan få problem, noden som aktiviteten körs på kanske startar om eller måste tas bort från poolen under en storleksändring om poolens avallokeringsprincip är inställd på att ta bort noder direkt utan att vänta tills aktiviteterna har slutförts. I samtliga fall kan aktiviteten placeras i kö igen av Batch för körning på en annan nod.

Det är också möjligt att ett tillfälligt problem gör att en aktivitet slutar svara eller tar för lång tid att köra. Du kan ange maximalt körningsintervall för en uppgift. Om det maximala körningsintervallet har överskridits avbryter batchtjänsten uppgiftsprogrammet.

### <a name="connecting-to-compute-nodes"></a>Ansluta till beräkningsnoder
Du kan utföra ytterligare felsökning genom att logga in till en beräkningsnod via en fjärranslutning. Du kan använda Azure-portalen för att hämta en RDP-fil (Remote Desktop Protocol) för Windows-noder och hämta SSH-anslutningsinformation (Secure Shell) för Linux-noder. Du kan också göra detta med hjälp av Batch-API:er, t.ex. med [Batch .NET][net_rdpfile] eller [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Om du vill ansluta till en nod via RDP eller SSH måste du först skapa en användare på noden. Du kan göra detta genom att använda Azure Portal, [lägga till ett användarkonto till en nod][rest_create_user] med hjälp av Batch REST-API:et, anropa [ComputeNode.CreateComputeNodeUser][net_create_user]-metoden i Batch .NET eller genom att anropa [add_user][py_add_user]-metoden i Batch Python-modulen.
>
>

### <a name="troubleshooting-problematic-compute-nodes"></a>Felsöka problematiska beräkningsnoder
Om vissa av dina aktiviteter misslyckas kan Batch-klientprogrammet eller Batch-tjänsten undersöka de misslyckade aktiviteternas metadata för att identifiera en skadad nod. Varje nod i poolen tilldelas ett unikt ID och den nod som en aktivitet körs på tas med i aktivitetens metadata. När du har identifierat en nod som har problem kan du utföra flera åtgärder:

* **Starta om noden** ([REST][rest_reboot] | [.NET][net_reboot])

    En omstart av noden kan ibland rensa latenta problem, t.ex. processer som har fastnat eller kraschat. Observera att om din pool har en startaktivitet eller om jobbet har en jobbförberedelseaktivitet så körs dessa när noden startar om.
* **Återställ avbildningen av noden** ([REST][rest_reimage] | [.NET][net_reimage])

    Den här åtgärden installerar om operativsystemet på noden. Precis som vid omstarten av en nod körs startaktiviteter och jobbförberedelseaktiviteter när nodens avbildning har återställts.
* **Ta bort noden från poolen** ([REST][rest_remove] | [.NET][net_remove])

    Ibland är det nödvändigt att helt ta bort noden från poolen.
* **Inaktivera schemaläggninga av aktiviteter på noden** ([REST][rest_offline] | [.NET][net_offline])

    Den här åtgärden kopplar effektivt bort noden så att den inte tilldelas några ytterligare aktiviteter, samtidigt som den kan fortsätta köras och finnas kvar i poolen. På så sätt kan du fortsätta att undersöka orsaken till felen utan att den misslyckade aktivitetens data går förlorade, och utan att noden orsakar ytterligare aktivitetsfel. Du kan till exempel inaktivera schemaläggning på noden och sedan [logga in via en fjärranslutning](#connecting-to-compute-nodes) för att granska nodens händelseloggar eller utföra annan felsökning. När du är klar med din undersökning kan du ansluta noden igen genom att aktivera schemaläggning ([REST][rest_online] | [.NET][net_online]) eller utföra någon av de övriga åtgärderna som beskrivs ovan.

> [!IMPORTANT]
> Med varje åtgärd ovan – Starta om, Återställ avbildning, Ta bort, Inaktivera aktivitetsschemaläggning – kan du ange hur aktiviteter som körs på noden hanteras när du utför åtgärden. Om du inaktiverar schemaläggning på en nod med Batch-klientbiblioteket för .NET kan du till exempel ange ett [DisableComputeNodeSchedulingOption][net_offline_option]-uppräkningsvärde för att ange om aktiviteter som körs ska **avslutas**, **placeras i kö igen** för schemaläggning på andra noder eller om de ska slutföras innan åtgärden körs (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Nästa steg
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Gå igenom ett Batch-exempelprogram steg för steg i [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md). Det finns också en [Python-version](batch-python-tutorial.md) i självstudien som kör en arbetsbelastning på Linux-beräkningsnoder.
* Hämta och installera [BatchLabs] [ batch_labs] och använd verktyget i arbetet med att utveckla Batch-lösningar. Använd BatchLabs för att skapa, felsöka och övervaka Azure Batch-program. 
* Lär dig hur du [skapar pooler för Linux-beräkningsnoder](batch-linux-nodes.md).
* Besök [Azure Batch-forumet][batch_forum] på MSDN. Forumet är en bra plats om du vill ställa frågor, oavsett om du är helt ny eller expert på Batch.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchLabs/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
