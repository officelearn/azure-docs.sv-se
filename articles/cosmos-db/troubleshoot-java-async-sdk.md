---
title: Diagnostisera och felsöka Azure Cosmos DB Java Async SDK
description: Använd funktioner som loggning på klientsidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Cosmos DB-problem.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65519986"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Felsöka problem när du använder Java Async SDK med Azure Cosmos DB SQL API-konton
Den här artikeln innehåller vanliga problem, lösningar, diagnostiksteg och verktyg när du använder [Java Async SDK](sql-api-sdk-async-java.md) med Azure Cosmos DB SQL API-konton.
Java Async SDK tillhandahåller logisk representation på klientsidan för att få åtkomst till Azure Cosmos DB SQL API. I den här artikeln beskrivs verktyg och metoder för att hjälpa dig om du stöter på problem.

Börja med den här listan:

* Ta en titt på avsnittet [Vanliga problem och lösningar] i den här artikeln.
* Titta på SDK, som är tillgänglig [öppen källkod på GitHub](https://github.com/Azure/azure-cosmosdb-java). Den har ett [avsnitt om problem](https://github.com/Azure/azure-cosmosdb-java/issues) som övervakas aktivt. Kontrollera om något liknande problem med en lösning redan har arkiverats.
* Granska [resultattipsen](performance-tips-async-java.md)och följ de föreslagna metoderna.
* Läs resten av den här artikeln om du inte hittade en lösning. Arkivera sedan ett [GitHub-problem](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Vanliga fel och lösningar

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Nätverksproblem, Netty-avläsningstidsfel, lågt dataflöde, hög latens

#### <a name="general-suggestions"></a>Allmänna förslag
* Kontrollera att appen körs på samma region som ditt Azure Cosmos DB-konto. 
* Kontrollera CPU-användningen på värden där appen körs. Om CPU-användningen är 90 procent eller mer kör du appen på en värd med en högre konfiguration. Eller så kan du fördela belastningen på fler maskiner.

#### <a name="connection-throttling"></a>Begränsning av anslutning
Anslutningsbegränsning kan inträffa på grund av antingen en [anslutningsgräns för en värddator] eller [utmattning av Azure SNAT (PAT).]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Anslutningsgräns för en värddator
Vissa Linux-system, till exempel Red Hat, har en övre gräns för det totala antalet öppna filer. Sockets i Linux implementeras som filer, så detta nummer begränsar det totala antalet anslutningar också.
Kör följande kommando.

```bash
ulimit -a
```
Antalet tillåtna öppna filer som tillåts maximalt, som identifieras som "nofile", måste vara minst dubbelt så stort som anslutningspoolen. Mer information finns i [Resultattips](performance-tips-async-java.md).

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Utmattning av Azure SNAT(PAT) port

Om din app distribueras på virtuella Azure-datorer utan en offentlig IP-adress upprättar [Azure SNAT-portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) som standard anslutningar till valfri slutpunkt utanför den virtuella datorn. Antalet anslutningar som tillåts från den virtuella datorn till Azure Cosmos DB-slutpunkten begränsas av [Azure SNAT-konfigurationen](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT-portar används endast när den virtuella datorn har en privat IP-adress och en process från den virtuella datorn försöker ansluta till en offentlig IP-adress. Det finns två lösningar för att undvika Azure SNAT-begränsning:

* Lägg till din Azure Cosmos DB-tjänstslutpunkt i undernätet i ditt virtuella Azure-datorer. Mer information finns i [Slutpunkter för Azure Virtual Network-tjänsten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    När tjänstslutpunkten är aktiverad skickas begärandena inte längre från en offentlig IP till Azure Cosmos DB. I stället skickas det virtuella nätverket och undernätsidentiteten. Den här ändringen kan resultera i att brandväggen sjunker om endast offentliga IPs tillåts. Om du använder en brandvägg lägger du till ett undernät i brandväggen när du aktiverar tjänstens slutpunkt med hjälp av [ACL:er för virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Tilldela en offentlig IP till din virtuella Azure-dator.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Kan inte nå Tjänsten - brandvägg
``ConnectTimeoutException``anger att SDK inte kan nå tjänsten.
Du kan få ett fel som liknar följande när du använder direktläget:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Om du har en brandvägg som körs på appdatorn öppnar du portintervallet 10 000 till 20 000 som används i direktläget.
Följ även [anslutningsgränsen på en värddator](#connection-limit-on-host).

#### <a name="http-proxy"></a>HTTP-proxy

Om du använder en HTTP-proxy kontrollerar du att den kan stödja `ConnectionPolicy`antalet anslutningar som konfigurerats i SDK .
Annars står du inför anslutningsproblem.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ogiltigt kodningsmönster: Blockera Netty IO-tråd

SDK använder [Netty](https://netty.io/) IO-biblioteket för att kommunicera med Azure Cosmos DB. SDK har Async API:er och använder icke-blockerande IO-API:er för Netty. SDK:s IO-arbete utförs på IO Netty-trådar. Antalet IO Netty-trådar är konfigurerat för att vara detsamma som antalet CPU-kärnor på appmaskinen. 

Netty IO-trådarna är endast avsedda att användas för icke-blockerande Netty IO-arbete. SDK returnerar API-anropsresultatet på en av Netty IO-trådarna till appens kod. Om appen utför en långvarig åtgärd efter att den har fått resultat på Netty-tråden kanske SDK inte har tillräckligt med IO-trådar för att utföra sitt interna IO-arbete. Sådan appkodning kan resultera i lågt dataflöde, hög latens och `io.netty.handler.timeout.ReadTimeoutException` fel. Lösningen är att byta tråd när du vet att åtgärden tar tid.

Ta till exempel en titt på följande kodavsnitt. Du kan utföra ett långvarigt arbete som tar mer än några millisekunder på Netty-tråden. Om så är fallet kan du så småningom komma in i ett tillstånd där ingen Netty IO tråd är närvarande för att bearbeta IO arbete. Därför får du ett ReadTimeoutException-fel.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   Lösningen är att ändra tråden där du utför arbete som tar tid. Definiera en singleton-instans av schemaläggaren för din app.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Du kan behöva göra arbete som tar tid, till exempel beräkningsmässigt tungt arbete eller blockering av IO. I det här fallet växlar du tråden till en arbetare som tillhandahålls av din `customScheduler` med hjälp av API: et. `.observeOn(customScheduler)`
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Genom `observeOn(customScheduler)`att använda släpper du Netty IO-tråden och växlar till din egen anpassade tråd som tillhandahålls av den anpassade schemaläggaren. Den här ändringen löser problemet. Du kommer inte `io.netty.handler.timeout.ReadTimeoutException` att få ett misslyckande längre.

### <a name="connection-pool-exhausted-issue"></a>Problem med anslutningspoolen

`PoolExhaustedException`är ett fel på klientsidan. Det här felet indikerar att din apparbetsbelastning är högre än vad SDK-anslutningspoolen kan betjäna. Öka storleken på anslutningspoolen eller fördela belastningen på flera appar.

### <a name="request-rate-too-large"></a>Begärsfrekvensen är för stor
Det här felet är ett fel på serversidan. Det indikerar att du har förbrukat ditt etablerade dataflöde. Försök igen senare. Om du får det här felet ofta bör du överväga en ökning av insamlingsdataflödet.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Det gick inte att ansluta till Azure Cosmos DB-emulatorn

Azure Cosmos DB-emulatorn HTTPS-certifikatet är självsignerat. Importera emulatorcertifikatet till en Java TrustStore för att SDK ska fungera med emulatorn. Mer information finns i [Exportera Azure Cosmos DB-emulatorcertifikat](local-emulator-export-ssl-certificates.md).

### <a name="dependency-conflict-issues"></a>Problem med beroendekonflikt

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

Ovanstående undantag tyder på att du är beroende av en äldre version av RxJava lib (t.ex. 1.2.2). Vår SDK förlitar sig på RxJava 1.3.8 som har API:er som inte är tillgängliga i tidigare versioner av RxJava. 

Lösningen för sådana utfärdanden är att identifiera vilket annat beroende som ger RxJava-1.2.2 och utesluta det transitiva beroendet av RxJava-1.2.2 och tillåta CosmosDB SDK att ta med den nyare versionen.

Om du vill identifiera vilket bibliotek som tar in RxJava-1.2.2 kör du följande kommando bredvid filen project pom.xml:
```bash
mvn dependency:tree
```
Mer information finns i [maven beroende träd guide](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

När du har identifierat RxJava-1.2.2 är transitivt beroende av vilket annat beroende av ditt projekt, kan du ändra beroendet av lib i din pom-fil och utesluta RxJava transitivt beroende det:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Mer information finns i [guiden uteslut transitivt beroende](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>Aktivera klient-SDK-loggning

Java Async SDK använder SLF4j som loggningsfasad som stöder loggning i populära loggningsramverk som log4j och logback.

Om du till exempel vill använda log4j som loggningsramverk lägger du till följande libs i javaklasssökvägen.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Lägg också till en log4j config.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Mer information finns i [loggningshandboken för Sfl4j](https://www.slf4j.org/manual.html).

## <a name="os-network-statistics"></a><a name="netstats"></a>Statistik för os-nätverk
Kör kommandot netstat för att få en känsla för `ESTABLISHED` hur `CLOSE_WAIT`många anslutningar som finns i stater som och .

På Linux kan du köra följande kommando.
```bash
netstat -nap
```
Filtrera resultatet till endast anslutningar till Azure Cosmos DB-slutpunkten.

Antalet anslutningar till Azure Cosmos DB-slutpunkten `ESTABLISHED` i tillståndet kan inte vara större än din konfigurerade anslutningspoolstorlek.

Många anslutningar till Azure Cosmos DB-slutpunkten `CLOSE_WAIT` kan vara i tillståndet. Det kan finnas mer än 1000. Ett tal som högt indikerar att anslutningar upprättas och rivs snabbt. Denna situation kan orsaka problem. Mer information finns i avsnittet [Vanliga problem och lösningar.]

 <!--Anchors-->
[Vanliga fel och lösningar]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Anslutningsgräns för en värddator]: #connection-limit-on-host
[Utmattning av Azure SNAT(PAT) port]: #snat


