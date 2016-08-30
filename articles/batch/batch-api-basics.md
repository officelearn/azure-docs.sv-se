<properties
    pageTitle="Översikt över Azure Batch-funktioner | Microsoft Azure"
    description="Lär dig mer om funktionerna i Batch-tjänsten och dess API:er ur ett utvecklingsperspektiv."
    services="batch"
    documentationCenter=".net"
    authors="yidingzhou"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/12/2016"
    ms.author="yidingz;marsma"/>

# Översikt över Azure Batch-funktioner

Den här artikeln innehåller en grundläggande översikt över viktiga API-funktioner i tjänsten Azure Batch. Oavsett om du utvecklar en distribuerad beräkningslösning med [Batch REST][batch_rest_api]- eller [Batch .NET][batch_net_api]-API:erna använder du många av entiteterna och funktionerna som beskrivs nedan.

> [AZURE.TIP] En teknisk översikt över Batch på högre nivå finns i [Grunderna om Azure Batch](batch-technical-overview.md).

## <a name="workflow"></a>Arbetsflödet i Batch-tjänsten

Följande generella arbetsflöde tillämpas i nästan alla distribuerade beräkningsscenarier som utvecklas med Batch-tjänsten:

1. Ladda upp *datafilerna* som du vill använda i ditt distribuerade beräkningsscenario till ett [Azure Storage][azure_storage]-konto. De här filerna måste finnas i Storage-kontot så att Batch-tjänsten kan komma åt dem. Aktiviteterna hämtar dessa filer till [beräkningsnoder](#computenode) när de körs.

2. Ladda upp de beroende *binärfilerna* till Storage-kontot. Dessa binära filer omfattar programmet som ska köras av aktiviteterna, och alla dess beroende sammansättningar. De här filerna måste också kunna nås från Storage-kontot så att aktiviteterna kan hämta dem till beräkningsnoderna.

3. Skapa en [pool](#pool) med beräkningsnoder. Du anger [storleken på beräkningsnoderna][cloud_service_sizes] när poolen skapas, och när en aktivitet körs så tilldelas den en nod i poolen.

4. Skapa ett [jobb](#job). Med ett jobb kan du hantera en samling aktiviteter.

5. Lägg till [aktiviteter](#task) till jobbet. Varje aktivitet använder programmet som du laddat upp för att bearbeta information i datafilerna som du överförde till ditt Storage-konto.

6. Övervaka jobbförloppet och hämta resultaten.

> [AZURE.NOTE] Du behöver ett [Batch-konto](batch-account-create-portal.md) för att använda Batch-tjänsten, och nästan alla lösningar använder ett [Azure Storage][azure_storage]-konto för fillagring och filhämtning. Batch stöder för närvarande endast den **allmänna** lagringskontotypen, vilket förklaras i steg 5, [Skapa ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account), i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md).

I avsnitten nedan lär du dig mer om de resurser som ingår i arbetsflödet ovan, samt om många andra funktioner i Batch som du kan använda för distribuerade beräkningsscenarier.

## <a name="resource"></a> Resurser i Batch-tjänsten

När du använder Batch använder du flera av följande resurser. Vissa av dessa resurser, t.ex konton, beräkningsnoder, pooler, jobb och aktiviteter, används i alla Batch-lösningar. Andra, till exempel jobbscheman och programpaket, är användbara men valfria funktioner.

- [Konto](#account)
- [Beräkningsnod](#computenode)
- [Pool](#pool)
- [Jobb](#job)
- [Aktivitet](#task)
    - [Startaktivitet](#starttask)
    - [Job Manager-aktivitet](#jobmanagertask)
    - [Jobbförberedelse- och jobbpubliceringsaktiviteter](#jobpreprelease)
    - [Aktiviteter med flera instanser](#multiinstance)
    - [Aktivitetsberoenden](#taskdep)
- [Jobbscheman](#jobschedule)
- [Programpaket](#appkg)

### <a name="account"></a>Konto

Ett Batch-konto är en unikt identifierad entitet i Batch-tjänsten. All bearbetning är associerad med ett Batch-konto. När du utför åtgärder med Batch-tjänsten behöver du både kontonamnet och kontonyckeln. Anvisningar för hur du skapar ett Batch-konto finns i [Skapa och hantera ett Azure Batch-konto på Azure-portalen](batch-account-create-portal.md).

### <a name="computenode"></a>Beräkningsnod

En beräkningsnod är en virtuell Azure-dator som är dedikerad till en viss arbetsbelastning för ditt program. Storleken på en nod avgör antalet CPU-kärnor, minneskapaciteten och storleken på det lokala filsystemet som allokeras till noden. En nod stöder alla [nodstorlekar för molntjänster][cloud_service_sizes], utom A0.

Noder kan köra exekverbara filer och skript, inklusive körbara filer (.exe), kommandofiler (.cmd), batchfiler (.bat) och PowerShell-skript. En nod har dessutom följande attribut:

- En **standardmappstruktur** och associerade **miljövariabler** som anger deras sökvägar skapas på varje beräkningsnod. Mer information finns i [Filer och kataloger](#files) nedan.
- **Miljövariabler** som kan användas som referens av aktiviteter.
- **Brandväggsinställningar** som konfigureras för att styra åtkomsten.
- Om **fjärråtkomst** till en beräkningsnod krävs (till exempel för felsökning) kan en RDP-fil hämtas som sedan kan användas för att komma åt noden via *Fjärrskrivbord*.

### <a name="pool"></a>Pool

En pool är en samling noder som programmet körs på. Poolen kan skapas manuellt av dig eller automatiskt av Batch-tjänsten när du anger arbetet som ska utföras. Du kan skapa och hantera en pool som uppfyller kraven för ditt program, och pooler kan endast användas av det Batch-konto som de har skapats i. Ett Batch-konto kan ha mer än en pool.

Azure Batch-pooler bygger på den grundläggande Azure-beräkningsplattformen: Batch-pooler tillhandahåller storskalig allokering, programinstallation, datadistribution och övervakning av hälsotillstånd, samt flexibel justering av antalet beräkningsnoder i en pool (skalning).

Varje nod som läggs till i en pool tilldelas ett unikt namn och en IP-adress. När en nod tas bort från en pool försvinner alla ändringar som gjorts i operativsystemet eller filer, och dess namn och IP-adress frisläpps för framtida användning. När en nod lämnar poolen är dess livslängd över.

Du kan konfigurera en pool att tillåta kommunikation mellan noderna i poolen. Om kommunikation inom en pool krävs kan Batch-tjänsten aktivera portar högre än 1100 på varje nod i poolen. Varje nod i poolen konfigureras att tillåta inkommande anslutningar inom endast detta portintervall, och endast från andra noder i poolen. Om programmet inte kräver kommunikation mellan noder kan Batch-tjänsten allokera ett potentiellt stort antal noder till poolen från många olika kluster och datacenter för ökad parallell bearbetningskapacitet.

När du skapar en pool kan du ange följande attribut:

- **Storleken på noderna** i poolen
    - Välj lämplig nodstorlek baserat på egenskaperna och kraven för det eller de program som ska köras på noderna. Normalt fastställs nodstorleken utifrån antagandet att en aktivitet körs på noden i taget. Andra viktiga aspekter att ha i åtanke för att välja den lämpligaste och mest kostnadseffektiva nodstorleken är huruvida programmet är flertrådat och hur mycket minne det förbrukar. Det går att ha flera tilldelade aktiviteter och flera programinstanser som körs parallellt. I så fall väljs oftast en större nod. Mer information finns i ”Schemaläggningsprincip för aktiviteter” nedan.
    - Alla noder i poolen måste ha samma storlek. Om olika program ska köras med olika systemkrav och/eller belastningsnivåer skapar du separata pooler.
    - Alla [nodstorlekar för molntjänster][cloud_service_sizes] kan konfigureras för en pool, utom A0.

- **Operativsystemfamilj** och **version** som körs på noderna
    - Precis som med arbetarroller i Cloud Services kan du ange *operativsystemsfamilj* och *operativsystemversion*. (Mer information om arbetarroller finns i avsnittet [Berätta mer om molntjänster][about_cloud_services] i *Värdtjänster för beräkning i Azure*.)
    - Operativsystemfamiljen avgör också vilka versioner av .NET som installeras med operativsystemet.
    - Som med arbetarroller rekommenderar vi att `*` anges för operativsystemversionen så att noderna uppgraderas automatiskt och så att inget extra arbete krävs för att hantera nya versioner. Det främsta syftet med att välja en specifik operativsystemversion är att se till att programkompatibiliteten upprätthålls, så att bakåtkompatibiliteten kan testas innan den nya versionen uppdateras. När den har verifierats kan operativsystemversionen för poolen uppdateras och den nya operativsystemavbildningen installeras. Eventuella aktiviteter som körs avbryts och placeras i kö.

- **Antal noder** som ska vara tillgängligt för poolen.

- **Skalningsprincip** för poolen.
    - Förutom antalet noder kan du även ange en [automatisk skalningsformel](batch-automatic-scaling.md) för en pool. Batch-tjänsten kör formeln och justerar antalet noder i poolen baserat på olika pool-, jobb- och aktivitetsparametrar som du kan ange.

- **Schemaläggningsprincip för aktiviteter**
    - Konfigurationsalternativet för [högsta antal aktiviteter per nod](batch-parallel-node-tasks.md) anger det högsta antal aktiviteter som kan köras parallellt på varje nod i poolen.
    - Med standardkonfigurationen kan en aktivitet köras på en beräkningsnod i taget, men det finns scenarier då det kan vara bra att låta flera aktiviteter köra på en nod samtidigt. Det kan till exempel vara smart att öka nodanvändningen om ett program måste vänta på I/O. Processoranvändningen ökar om flera program körs samtidigt. Ett annat alternativ är att minska antalet noder i poolen. På så sätt kan du minska dataöverföringsmängden som krävs för stora uppsättningar referensdata – om nodstorlek A1 är tillräcklig för ett program kan du i stället välja nodstorlek A4 och konfigurera poolen för åtta parallella aktiviteter, som var och en använder en kärna.
    - En ”fyllningstyp” kan också anges som bestämmer om Batch-tjänsten sprider ut aktiviteterna jämnt över alla noder eller om den distribuerar det högsta antalet aktiviteter till varje nod innan aktiviteter tilldelas till en annan nod i poolen.

- **Kommunikationsstatus** för noderna i poolen
    - En pool kan konfigureras att tillåta kommunikation mellan dess noder, vilket avgör poolens underliggande nätverksinfrastruktur. Observera att detta också påverkar nodernas placering i kluster.
    - I de flesta fall fungerar aktiviteter oberoende av varandra och behöver inte kommunicera med varandra, men det kan finnas program där de måste kunna kommunicera.

- **Startaktivitet** för noder i poolen
    - Du kan ange en *startaktivitet* som körs varje gång en datornod ansluter till poolen, och när en nod startas om. En startaktivitet används ofta för att installera ett program som ska användas av aktiviteterna som körs på noden.

### <a name="job"></a>Jobb

Ett jobb är en samling aktiviteter och anger hur beräkningen utförs på beräkningsnoderna i en pool.

- Jobbet anger vilken **pool** som arbetet ska köras i. Poolen kan vara en befintlig pool, som har skapats för användning av flera jobb, eller skapas på begäran för varje jobb som associeras med ett jobbschema eller för alla jobb som är associerade med ett jobbschema.
- En valfri **jobbprioritet** kan anges. När ett jobb skickas med en högre prioritet än pågående jobb placeras aktiviteterna för jobbet med den högre prioriteten i kön före aktiviteter för jobb med lägre prioritet. Aktiviteter med lägre prioritet som redan körs avbryts inte.
- **Jobbegränsningar** anger särskilda gränser för dina jobb.
    - Du kan ange en **högsta wall-clock-tid** för jobb. Om ett jobb körs längre än den högsta angivna wall-clock-tiden avslutas jobbet och alla associerade aktiviteter.
    - Azure Batch kan identifiera aktiviteter som misslyckas och försöka köra dem igen. Du kan ange det **högsta antalet omförsök för aktiviteter** som en begränsning. Du kan också ange om nya försök alltid eller aldrig ska göras för en aktivitet. Omförsök innebär att aktiviteten placeras i körningskön igen.
- Aktiviteter kan läggas till för jobbet av ditt klientprogram eller så kan en [Job Manager-aktivitet](#jobmanagertask) anges. En Job Manager-aktivitet använder Batch-API:et och innehåller den information som krävs för att skapa de nödvändiga aktiviteterna för ett jobb, där aktiviteten körs på en av beräkningsnoderna i poolen. Job Manager-aktiviteten hanteras av Batch – den placeras i kö så fort jobbet skapas och startas om ifall den misslyckas. En Job Manager-aktivitet krävs för jobb som skapas av ett jobbschema eftersom det är det enda sättet att definiera aktiviteterna innan jobbet instantieras. Mer information om Job Manager-aktiviteter finns nedan.

### <a name="task"></a>Aktivitet

En aktivitet är en beräkningsenhet som är associerad med ett jobb och som körs på en nod. Aktiviteter tilldelas till en nod för körning eller placeras i kö tills en nod blir ledig. En aktivitet använder följande resurser:

- Programmet som anges på **kommandoraden** för aktiviteten.

- **Resursfiler** som innehåller de data som ska bearbetas. Filerna kopieras automatiskt till noden från Blob Storage i ett **allmänt** Azure Storage-konto. Mer information finns i *Startaktivitet* och [Filer och kataloger](#files) nedan.

- De **miljövariabler** som krävs av programmet. Mer information finns i [Miljöinställningar för aktiviteter](#environment) nedan.

- De **begränsningar** som ska tillämpas vid beräkningen. Till exempel den längsta tid som aktiviteten tillåts att köra, det högsta antalet nya försök som ska göras om en aktivitet misslyckas eller den längsta tid som filer i arbetskatalogen bevaras.

Utöver de aktiviteter som du definierar för att utföra beräkningen på en nod tillhandahålls även följande särskilda aktiviteter av Batch-tjänsten:

- [Startaktivitet](#starttask)
- [Job Manager-aktivitet](#jobmanagertask)
- [Jobbförberedelse- och jobbpubliceringsaktiviteter](#jobmanagertask)
- [Aktiviteter med flera instanser](#multiinstance)
- [Aktivitetsberoenden](#taskdep)

#### <a name="starttask"></a>Startaktivitet

Genom att associera en **startaktivitet** med en pool kan du konfigurera driftmiljön för dess noder och till exempel installera programvara eller starta bakgrundsprocesser. Startaktiviteten körs varje gång en nod startas så länge den finns kvar i poolen. Startaktiviteten körs också när noden läggs till i poolen. En viktig fördel med startaktiviteten är att den innehåller all information som behövs för att konfigurera beräkningsnoder och installera de program som krävs för att köra jobbaktiviteter. Detta betyder att du enkelt kan öka antalet noder i en pool genom att bara ange det nya antalet målnoder – Batch har redan all information som behövs för att konfigurera de nya noderna och förbereda dem så att de kan ta emot aktiviteter.

Som med alla Batch-aktiviteter kan du ange en lista med **resursfiler** i [Azure Storage][azure_storage], förutom en **kommandorad** som du vill köra. Azure Batch kopierar först filerna från Azure Storage och kör sedan kommandoraden. För startaktiviteten för en pool innehåller fillistan vanligtvis programpaketet eller programfilerna, men den kan också innehålla referensdata som ska användas av alla aktiviteter som körs på beräkningsnoderna. Startaktivitetens kommandorad kan köra ett PowerShell-skript eller utföra en `robocopy`-åtgärd, till exempel för att kopiera programfiler till mappen ”delade”, och sedan köra en MSI-fil eller `setup.exe`.

> [AZURE.IMPORTANT] Batch stöder för närvarande *endast* den **allmänna** lagringskontotypen, vilket förklaras i steg 5, [Skapa ett lagringskonto](../storage/storage-create-storage-account.md#create-a-storage-account), i [Om Azure-lagringskonton](../storage/storage-create-storage-account.md). Batch-aktiviteterna (inklusive standardaktiviteter, startaktiviteter, jobbförberedelser och jobbpubliceringsaktiviteter) måste definiera resursfiler som ** finns i **allmänna** lagringskonton.

Normalt bör Batch-tjänsten vänta tills startaktiviteten har slutförts innan noden tilldelas aktiviteter, men detta kan konfigureras vid behov.

Om startaktiviteten misslyckas på en beräkningsnod uppdateras nodens status med feltillståndet och noden är inte längre tillgänglig för aktivitetstilldelning. Startaktiviteten kan misslyckas om det inte går att kopiera aktivitetens resursfiler från lagringen, eller om processen som körs av aktivitetens kommandorad returnerar en slutkod som inte är noll.

#### <a name="jobmanagertask"></a>Job Manager-aktivitet

En **Job Manager-aktivitet** används normalt för att kontrollera och övervaka körningen av jobb. Den kan till exempel användas för att skapa och skicka aktiviteterna för ett jobb, avgöra om fler aktiviteter ska köras eller fastställa när arbetet är klart. En Job Manager-aktivitet är inte begränsad till dessa aktiviteter. Det är en fullständig aktivitet som kan köra alla åtgärder som krävs för jobbet. Den kan till exempel ladda ned en fil som angetts som en parameter, analysera innehållet i filen och skicka fler aktiviteter baserat på innehållet.

En Job Manager-aktivitet startar innan andra aktiviteter och tillhandahåller följande funktioner:

- Den skickas automatiskt som en aktivitet av Batch-tjänsten när jobbet skapas.

- Den är schemalagd att köras innan andra aktiviteter i ett jobb.

- Dess associerade nod är den sista som tas bort från en pool om poolens storlek minskas.

- Dess avslut kan vara kopplat till avslutet av alla aktiviteter i jobbet.

- Job Manager-aktiviteten får högsta prioritet om den behöver startas om. Om en inaktiv nod inte är tillgänglig kan Batch-tjänsten avsluta någon av de andra aktiviteterna som körs i poolen för att göra plats för Job Manager-aktiviteten så att den kan köras.

- En Job Manager-aktivitet i ett jobb har inte prioritet över aktiviteter i andra jobb. Mellan jobb gäller endast prioriteringar på jobbnivå.

#### <a name="jobpreprelease"></a>Jobbförberedelse- och jobbpubliceringsaktiviteter

Batch tillhandahåller en jobbförberedelseaktivitet för konfiguration innan jobbet körs och en jobbpubliceringsaktivitet för underhåll och rensning efter jobbet.

- **Jobbförberedelseaktivitet** – Jobbförberedelseaktiviteten körs på alla beräkningsnoder som schemalagts att köra aktiviteter, innan andra jobbaktiviteter körs. Du kan till exempel använda jobbförberedelseaktiviteten för att kopiera data som delas av alla aktiviteter, men som är unika för jobbet.
- **Jobbpubliceringsaktivitet** – När ett jobb har slutförts körs jobbpubliceringsaktiviteten på alla noder i poolen som har kört minst en aktivitet. Du kan till exempel använda jobbpubliceringsaktiviteten för att ta bort data som kopierades av jobbförberedelseaktiviteten, eller för att komprimera och ladda upp diagnostikloggdata.

Med både jobbförberedelse- och jobbpubliceringsaktiviteter kan du ange en kommandorad som ska köras när aktiviteten anropas. Det finns stöd för flera funktioner, t.ex. filhämtning, upphöjd körning, anpassade miljövariabler, maximal körningstid, antal omförsök och kvarhållningstid för filer.

Mer information om jobbförberedelse- och jobbpubliceringsaktiviteter finns i [Köra jobbförberedelse- och jobbpubliceringsaktiviteter på Azure Batch-beräkningsnoder](batch-job-prep-release.md).

#### <a name="multiinstance"></a>Aktiviteter med flera instanser

En [aktivitet med flera instanser](batch-mpi.md) är en aktivitet som är konfigurerad att köras på mer än en beräkningsnod samtidigt. Aktiviteter med flera instanser stöder scenarier med databehandling med höga prestanda som MPI (Message Passing Interface) som kräver en grupp med beräkningsnoder som allokeras tillsammans för att bearbeta en enskild arbetsbelastning.

Detaljerad information om hur du kör MPI-jobb i Batch med hjälp av Batch .NET-biblioteket finns i [Använda aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i Azure Batch](batch-mpi.md).

#### <a name="taskdep"></a>Aktivitetsberoenden

Med aktivitetsberoenden kan du, som namnet antyder, ange att en aktivitet är beroende av att andra aktiviteter slutförs innan den kan köras. Den här funktionen har stöd för situationer där en ”underordnad” aktivitet använder utdata från en ”överordnad” aktivitet, eller där en överordnad aktiviteten utför viss initiering som krävs av en underordnad aktivitet. Innan du kan använda den här funktionen måste du aktivera aktivitetsberoenden i Batch-jobbet. För varje aktivitet som är beroende av en annan (eller flera andra) anger du sedan de aktiviteter som den aktiviteten är beroende av.

Med aktivitetsberoenden kan du konfigurera scenarier som följande:

* *aktivitetB* är beroende av *aktivitetA* (*aktivitetB* kan inte köra förrän *aktivitetA* har slutförts).
* *aktivitetC* är beroende av både *aktivitetA* och *aktivitetB*.
* *aktivitetD* är beroende av en serie aktiviteter, till exempel aktivitet *1* till och med *10*, innan den kan köras.

Se [TaskDependencies][github_sample_taskdeps]-kodexemplet i [azure-batch-samples][github_samples]-arkivet på GitHub. Där ser du hur du kan konfigurera aktiviteter som är beroende av andra aktiviteter med hjälp av [Batch .NET-biblioteket][batch_net_api].

### <a name="jobschedule"></a>Schemalagda jobb

Med jobbscheman kan du skapa återkommande jobb i Batch-tjänsten. Ett jobbschema anger när jobb ska köras och innehåller specifikationerna för jobben som ska köras. Med ett jobbschema kan du ange schemats varaktighet – hur lång tid och när schemat gäller – och hur ofta under den tidsperioden som jobb ska skapas.

### <a name="appkg"></a>Programpaket

Funktionen för [programpaket](batch-application-packages.md) gör det enkelt att hantera och distribuera program till beräkningsnoder i dina pooler. Med programpaket kan du enkelt ladda upp och hantera flera versioner av programmet som körs av dina aktiviteter, inklusive binärfiler och stödfiler, och sedan automatiskt distribuera en eller flera av dessa program till beräkningsnoderna i poolen.

Batch hanterar informationen om Azure Storage-arbetet i bakgrunden och ser till att dina programpaket lagras och distribueras till beräkningsnoderna på ett säkert sätt, vilket innebär att både kodnings- och hanteringsarbetet kan förenklas.

Mer information om funktionen för programpaket finns i [Programdistribution med Azure Batch-programpaket](batch-application-packages.md).

## <a name="files"></a>Filer och kataloger

Varje aktivitet har en arbetskatalog där den skapar flera (eller inga) filer och kataloger för att lagra programmet som den kör, de data som den bearbetar och bearbetningsresultatet. Dessa filer och kataloger är sedan tillgängliga för användning av andra aktiviteter när ett jobb körs. Alla aktiviteter, filer och kataloger på en nod ägs av ett enskilt användarkonto.

Batch-tjänsten exponerar en del av filsystemet på en nod som ”rotkatalogen”. Rotkatalogen är tillgänglig för en aktivitet via `%AZ_BATCH_NODE_ROOT_DIR%`-miljövariabeln. Mer information om hur du använder miljövariabler finns i [Miljöinställningar för aktiviteter](#environment).

![Katalogstrukturen på beräkningsnoder][1]

Rotkatalogen innehåller följande katalogstruktur:

- **Delad** – Den här platsen är en delad katalog för alla aktiviteter som körs på en nod, oavsett jobb. På noden kan den delade katalogen nås via `%AZ_BATCH_NODE_SHARED_DIR%`. Den här katalogen ger läs-/skrivbehörighet till alla aktiviteter som utförs på noden. Aktiviteter kan skapa, läsa, uppdatera och ta bort filer i den här katalogen.

- **Start** – Den här platsen används av en startaktivitet som dess arbetskatalog. Alla filer som hämtas av Batch-tjänsten för att starta startaktiviteten lagras också under den här katalogen. På noden är startkatalogen tillgänglig via `%AZ_BATCH_NODE_STARTUP_DIR%`-miljövariabeln. Startaktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen och katalogen kan användas av startaktiviteter för att konfigurera operativsystemet.

- **Aktiviteter** – En katalog skapas för varje aktivitet som körs på noden och nås via `%AZ_BATCH_TASK_DIR%`. I varje aktivitetskatalog skapar Batch-tjänsten en arbetskatalog (`wd`) vars unika sökväg anges av `%AZ_BATCH_TASK_WORKING_DIR%`-miljövariabeln. Den här katalogen ger läs-/skrivbehörighet till aktiviteten. Aktiviteten kan skapa, läsa, uppdatera och ta bort filer under den här katalogen och katalogen bevaras baserat på *RetentionTime*-begränsningen som har angetts för aktiviteten.
  - `stdout.txt` och `stderr.txt` – Dessa filer skrivs till aktivitetsmappen när aktiviteten körs.

När en nod tas bort från poolen tas alla filer som lagras på noden bort.

## <a name="lifetime"></a>Livslängd för pooler och beräkningsnoder

När du utvecklar din Azure Batch-lösning måste du tänka igenom hur och när pooler ska skapas och hur länge beräkningsnoderna i poolerna ska vara tillgängliga.

Ett alternativ är att skapa en pool för varje jobb när jobbet skickas och sedan ta bort poolens noder så fort aktiviteterna har slutförts. Med den här metoden maximeras användningen eftersom noderna bara tilldelas när det är absolut nödvändigt och stängs ned så fort de blir inaktiva. Detta innebär visserligen att jobbet måste vänta tills noderna har allokerats, men det är viktigt att notera att aktiviteterna schemaläggs till noder så fort de är individuellt tillgängliga och startuppgiften har slutförts. Batch väntar *inte* tills alla noder i en pool är tillgängliga innan aktiviteterna tilldelas, vilket säkerställer maximal användning av alla tillgängliga noder.

Å andra sidan, om det är högsta prioritet att jobben startar direkt, kan en pool skapas i förväg och dess noder göras tillgängliga innan jobben skickas. I det här scenariot kan jobbaktiviteterna starta direkt, men noderna kan vara inaktiva medan de väntar på att aktiviteter ska tilldelas.

En kombinerad metod, som normalt används för att hantera en varierande men kontinuerlig belastning, är att ha en pool som flera jobb skickas till, men skala upp eller ned antalet noder baserat på arbetsbelastningen (se *Skala program* nedan). Detta kan göras reaktivt baserat på den aktuella belastningen eller proaktivt om det går att förutse belastningen.

## <a name="scaling"></a>Skala program

Med [automatisk skalning](batch-automatic-scaling.md) kan Batch-tjänsten dynamiskt justera antalet beräkningsnoder i en pool baserat på den aktuella arbetsbelastningen och resursanvändningen i beräkningsscenariot. På så sätt kan du sänka den totala kostnaden för programkörning genom att endast använda de resurser som du behöver och frisläppa de som du inte behöver. Du kan ange inställningar för automatisk skalning för en pool när den skapas eller aktivera skalning senare och du kan uppdatera skalningsinställningarna för en pool som du har aktiverat automatisk skalning för.

Automatisk skalning utförs med hjälp av en **formel för automatisk skalning** för en pool. Batch-tjänsten använder den här formeln för att bestämma antalet noder i poolen för nästa skalningsintervall (ett intervall som du kan ange).

Ett jobb kan till exempel kräva att du skickar ett stort antal aktiviteter som ska schemaläggas för körning. Du kan tilldela en skalningsformel till poolen som justerar antalet noder i poolen baserat på antalet väntande aktiviteter och aktiviteternas slutförandefrekvens. Batch-tjänsten utvärderar med jämna mellanrum formeln och ändrar storlek på poolen baserat på arbetsbelastningen och dina formelinställningar.

En skalningsformel kan baseras på följande mått:

- **Tidsmått** – Baseras på statistik som samlas in var femte minut under angivet antalet timmar.

- **Mätvärden för resurs** – Baseras på processoranvändning, bandbreddsanvändning, minnesanvändning och antalet noder.

- **Mätvärden för aktivitet** – Baseras på statusen för aktiviteter, till exempel Aktiv, Väntar och Slutförd.

Om den automatiska skalningen minskar antalet beräkningsnoder i en pool måste hänsyn tas till pågående aktiviteter. Detta kan hanteras med en nodavallokeringsprincip i formeln som anger om pågående aktiviteter ska stoppas direkt, eller om de ska slutföras innan noden tas bort från poolen.

> [AZURE.TIP] Du kan maximera användningen av beräkningsresurserna genom att ange målantalet noder till noll i slutet av ett jobb, men tillåta att aktiviteter som körs slutförs.

Mer information om automatisk skalning av program finns i [Skala beräkningsnoder automatiskt i en Azure Batch-pool](batch-automatic-scaling.md).

## <a name="cert"></a>Säkerhet med certifikat

Vanligtvis måste du använda certifikat när du krypterar eller dekrypterar känslig aktivitetsinformation, till exempel nyckeln för ett [Azure-lagringskonto][azure_storage]. För detta ändamål kan du installera certifikat på noderna. Krypterade hemligheter skickas till aktiviteter via kommandoradsparametrar eller bäddas in i någon av aktivitetsresurserna, och de installerade certifikaten kan användas för att dekryptera dem.

Du använder åtgärden [Lägg till certifikat][rest_add_cert] (Batch REST-API) eller metoden [CertificateOperations.CreateCertificate][net_create_cert] (Batch .NET-API) för att lägga till ett certifikat till ett Batch-konto. Därefter kan du associera certifikatet med en ny eller befintlig pool. När ett certifikat associeras med en pool installeras certifikatet av Batch-tjänsten på varje nod i poolen. Batch-tjänsten installerar relevanta certifikat när noden startar, innan andra aktiviteter startas, inklusive Job Manager- och startaktiviteter.

## <a name="scheduling"></a>Schemaläggningsprioritet

Du kan tilldela en prioritet till jobb som du skapar i Batch. Batch-tjänsten använder jobbets prioritetsvärde för att fastställa i vilken ordning jobben ska schemaläggas i ett konto. Prioritetsvärdesintervallet är -1 000 till 1 000, där -1 000 är lägst prioritet och 1 000 högst. Du kan uppdatera prioriteten för ett jobb med hjälp av åtgärden [Uppdatera egenskaperna för ett jobb][rest_update_job] (REST-API:et för Batch) eller genom att ändra egenskapen [CloudJob.Priority][net_cloudjob_priority] (Batch .NET-API).

Inom samma konto ha jobb med högre prioritet schemaläggningsprioritet framför jobb med lägre prioritet. Ett jobb med ett högre prioritetsvärde i ett konto har inte schemaläggningsprioritet över ett annat jobb med ett lägre prioritetsvärde i ett annat konto.

Schemaläggningen av jobb mellan pooler är oberoende av varandra. Mellan olika pooler är det inte säkert att ett jobb med högre prioritet schemaläggs först om dess associerade pool har ont om inaktiva noder. I samma pool har jobb med samma prioritetsnivå samma chans att schemaläggas.

## <a name="environment"></a>Miljöinställningar för aktiviteter

Varje aktivitet som körs i ett Batch-jobb har åtkomst till miljövariabler som anges både av Batch-tjänsten (systemdefinierade, se tabellen nedan) och med användardefinierade miljövariabler. Program och skript som körs av aktiviteter på beräkningsnoder har åtkomst till dessa miljövariabler under körningen på noden.

Du kan konfigurera användardefinierade miljövariabler med hjälp av åtgärden [Lägg till en aktivitet till ett jobb][rest_add_task] (Batch REST-API) eller genom att ändra egenskapen [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET-API) när du lägger till aktiviteter till ett jobb.

Du kan hämta en aktivitets miljövariabler, både system- och användardefinierade, med hjälp av åtgärden [Hämta information om en aktivitet][rest_get_task_info] (Batch REST-API) eller med egenskapen [CloudTask.EnvironmentSettings][net_cloudtask_env] (Batch .NET-API). Som vi nämnt kan processer som körs på en beräkningsnod även komma åt alla miljövariabler, till exempel med hjälp av den vanliga `%VARIABLE_NAME%`-syntaxen.

För varje aktivitet som schemalagts i ett jobb definierar Batch-tjänsten följande uppsättning systemdefinierade miljövariabler:

| Miljövariabelns namn       | Beskrivning                                                              |
|---------------------------------|--------------------------------------------------------------------------|
| `AZ_BATCH_ACCOUNT_NAME`         | Namnet på det konto som aktiviteten tillhör.                       |
| `AZ_BATCH_JOB_ID`               | ID:t för jobbet som aktiviteten tillhör.                             |
| `AZ_BATCH_JOB_PREP_DIR`         | Den fullständiga sökvägen till katalogen för jobbförberedelseaktiviteten på noden.         |
| `AZ_BATCH_JOB_PREP_WORKING_DIR` | Den fullständiga sökvägen till arbetskatalogen för jobbförberedelseaktiviteten på noden. |
| `AZ_BATCH_NODE_ID`              | ID:t för noden som aktiviteten körs på.                         |
| `AZ_BATCH_NODE_ROOT_DIR`        | Den fullständiga sökvägen till rotkatalogen på noden.                         |
| `AZ_BATCH_NODE_SHARED_DIR`      | Den fullständiga sökvägen till den delade katalogen på noden.                       |
| `AZ_BATCH_NODE_STARTUP_DIR`     | Den fullständiga sökvägen till katalogen för beräkningsnodens startaktivitet på noden.    |
| `AZ_BATCH_POOL_ID`              | ID:t för poolen som aktiviteten körs i.                         |
| `AZ_BATCH_TASK_DIR`             | Den fullständiga sökvägen till aktivitetskatalogen på noden.                         |
| `AZ_BATCH_TASK_ID`              | ID:t för den aktuella aktiviteten.                                              |
| `AZ_BATCH_TASK_WORKING_DIR`     | Den fullständiga sökvägen till arbetskatalogen för aktiviteten på noden.                 |

>[AZURE.NOTE] Du kan inte skriva över någon av ovanstående systemdefinierade variabler – de är skrivskyddade.

## <a name="errorhandling"></a>Felhantering

Du kan behöva hantera både aktivitets- och programfel i din Batch-lösning.

### Hantering av aktivitetsfel
Aktivitetsfel kan delas in i följande kategorier:

- **Schemaläggningsfel**
    - Om överföringen av filer för en aktivitet misslyckas märks aktiviteten med ett ”schemaläggningsfel”.
    - Schemaläggningsfel kan till exempel bero på att filerna har flyttats, att Storage-kontot inte längre är tillgängligt eller på ett annat problem som gjorde att det inte gick att kopiera filer till noden.
- **Programfel**
    - Processen som definieras av aktivitetens kommandorad kan också misslyckas. Detta händer om en slutkod som inte är noll returneras av processen som körs av aktiviteten.
    - För programfel kan du konfigurera Batch så att nya försök görs automatiskt upp till ett angivet antal försök.
- **Begränsningsfel**
    - Du kan definiera ett villkor som anger den högsta körningstiden för ett jobb eller en aktivitet, *maxWallClockTime*. Detta kan vara användbart för att avsluta aktiviteter som har slutat svara.
    - När den högsta körningstiden överskrids markeras aktiviteten som *slutförd* men slutkoden är `0xC000013A` och fältet *schedulingError* markeras som `{ category:"ServerError", code="TaskEnded"}`.

### Felsöka programfel

När ett program körs kan det generera diagnostikutdata som du kan använda för att felsöka problem. Som vi nämnde i [Filer och kataloger](#files) ovan skickar Batch-tjänsten stdout- och stderr-utdata till `stdout.txt`- och `stderr.txt`-filer som finns i aktivitetskatalogen på beräkningsnoden. Genom att använda [ComputeNode.GetNodeFile][net_getfile_node] och [CloudTask.GetNodeFile][net_getfile_task] i Batch .NET-API:et kan du hämta dessa och andra filer för felsökningsändamål.

Du kan utföra ännu mer omfattande felsökning genom att logga in på en beräkningsnod med *Fjärrskrivbord*. Du kan [hämta en protokollfil för fjärrskrivbord från en nod][rest_rdp] (REST-API:et för Batch) eller använda metoden [ComputeNode.GetRDPFile][net_rdp] (Batch .NET-API) för fjärrinloggning.

>[AZURE.NOTE] Om du vill ansluta till en nod via RDP måste du först skapa en användare på noden. [Lägg till ett användarkonto till en nod][rest_create_user] i Batch REST-API:et eller använd metoden [ComputeNode.CreateComputeNodeUser][net_create_user] i Batch .NET.

### Ta med aktivitetsfel eller avbrott i beräkningen

Det händer att aktiviteter misslyckas eller avbryts. Själva aktivitetsprogrammet kan få problem, noden som aktiviteten körs på kanske startar om eller måste tas bort från poolen under en storleksändring om poolens avallokeringsprincip är inställd på att ta bort noder direkt utan att vänta tills aktiviteter slutförts. I samtliga fall kan aktiviteten placeras i kö igen av Batch för körning på en annan nod.

Det är också möjligt att ett tillfälligt problem gör att en aktivitet slutar svara eller tar för lång tid att köra. Den högsta körningstiden kan anges för en aktivitet, och om den överskrids så avbryts aktivitetsprogrammet av Batch.

### Felsöka ”skadade” beräkningsnoder

Om vissa av dina aktiviteter misslyckas kan Batch-klientprogrammet eller Batch-tjänsten undersöka de misslyckade aktiviteternas metadata för att identifiera en skadad nod. Varje nod i poolen tilldelas ett unikt ID och den nod som en aktivitet körs på tas med i aktivitetens metadata. När den har identifierats kan du utföra flera åtgärder:

- **Starta om noden** ([REST][rest_reboot] | [.NET][net_reboot])

    En omstart av noden kan ibland rensa latenta problem, t.ex. processer som har fastnat eller kraschat. Observera att om din pool har en startaktivitet eller om jobbet har en jobbförberedelseaktivitet så körs dessa när noden startar om.

- **Återställ avbildning av nod** ([REST][rest_reimage] | [.NET][net_reimage])

    Den här åtgärden installerar om operativsystemet på noden. Precis som vid omstarten av en nod körs startaktiviteter och jobbförberedelseaktiviteter när nodens avbildning har återställts.

- **Ta bort noden från poolen** ([REST][rest_remove] | [.NET][net_remove])

    Ibland är det nödvändigt att helt ta bort noden från poolen.

- **Inaktivera aktivitetsschemaläggning på noden** ([REST][rest_offline] | [.NET][net_offline])

    Den här åtgärden kopplar effektivt bort noden så att inga ytterligare aktiviteter tilldelas till den, men noden kan fortsätta köra och finnas kvar i poolen. På så sätt kan du fortsätta att undersöka orsaken till felen utan att den misslyckade aktivitetens data går förlorade, och utan att noden orsakar ytterligare aktivitetsfel. Du kan till exempel inaktivera schemaläggning på noden och sedan logga in via en fjärranslutning för att granska nodens händelseloggar eller utföra annan felsökning. När du är klar med din undersökning kan du ansluta noden igen genom att aktivera schemaläggning ([REST][rest_online], [.NET][net_online]) eller utföra någon av de övriga åtgärderna som beskrivs ovan.

> [AZURE.IMPORTANT] Med varje åtgärd ovan – omstart, återställ avbildning, ta bort, inaktivera aktivitetsschemaläggning – kan du ange hur aktiviteter som körs på noden hanteras när du utför åtgärden. Om du inaktiverar schemaläggning på en nod med Batch-klientbiblioteket för .NET kan du till exempel ange ett [DisableComputeNodeSchedulingOption][net_offline_option]-uppräkningsvärde för att ange om aktiviteter som körs ska **avslutas**, **placeras i kö igen** för schemaläggning på andra noder eller om de ska slutföras innan åtgärden körs (**TaskCompletion**).

## Nästa steg

- Skapa ditt första Batch-program genom att följa stegen i [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md)
- Ladda ned och skapa [Batch Explorer][batch_explorer_project]-exempelprojektet för användning när du utvecklar dina Batch-lösningar. Med Batch Explorer kan du göra följande och mer:
  - Övervaka och hantera pooler, jobb och aktiviteter i ditt Batch-konto
  - Ladda ned `stdout.txt`, `stderr.txt` och andra filer från noder
  - Skapa användare på noder och ladda ned RDP-filer för fjärrinloggning

[1]: ./media/batch-api-basics/node-folder-structure.png

[about_cloud_services]: ../cloud-services/cloud-services-choose-me.md
[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies

[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx



<!--HONumber=jun16_HO2-->


