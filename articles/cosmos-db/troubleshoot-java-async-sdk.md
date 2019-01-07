---
title: Diagnostisera och felsöka Azure Cosmos DB Java Async SDK
description: Använda funktioner som loggning på klientsidan och andra tredjepartsverktyg för att identifiera, diagnostisera och felsöka problem med Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 86e5a0a0cf4c820efdcc65505d11e2fb0c198f0b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039851"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Felsöka problem när du använder Async Java-SDK med Azure Cosmos DB SQL API-konton
Den här artikeln beskriver vanliga problem, lösningar, diagnos och verktyg när du använder den [Java Async SDK](sql-api-sdk-async-java.md) med Azure Cosmos DB SQL API-konton.
Async Java SDK tillhandahåller klientsidan logisk representation för att komma åt Azure Cosmos DB SQL API. Den här artikeln beskriver verktyg och metoder för att hjälpa dig om du stöter på problem.

Börja med den här listan:

* Ta en titt på de [vanliga problem och lösningar] i den här artikeln.
* Titta på SDK, som är tillgänglig [öppen källkod på GitHub](https://github.com/Azure/azure-cosmosdb-java). Den har en [utfärdar avsnittet](https://github.com/Azure/azure-cosmosdb-java/issues) som övervakas aktivt. Kontrollera om alla liknande problem med en lösning redan har arkiverats.
* Granska den [prestandatips](performance-tips-async-java.md), och följ de föreslagna metoderna.
* Läsa resten av den här artikeln om du inte hittar en lösning. Sedan filens en [GitHub-ärende](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Vanliga problem och lösningar

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Nätverksproblem, Netty läsa timeout-fel, lågt dataflöde, svarstider

#### <a name="general-suggestions"></a>Allmänna förslag
* Kontrollera att programmet körs i samma region som Azure Cosmos DB-kontot. 
* Kontrollera processoranvändningen på den värd där appen körs. Om processoranvändningen är 90 procent eller mer, kan du köra din app på en värd med en högre konfiguration. Eller du kan distribuera belastningen på flera datorer.

#### <a name="connection-throttling"></a>Anslutningsbegränsning
Anslutningsbegränsning kan inträffa på grund av antingen en [anslutningsgränsen på en värddator] eller [Portöverbelastning Azure SNAT (PAT)].

##### <a name="connection-limit-on-host"></a>Anslutningsgränsen på en värddator
Vissa Linux-system, till exempel Red Hat, har en övre gräns för totalt antal öppna filer. Sockets i Linux implementeras som filer, så det här talet begränsar det totala antalet anslutningar, för.
Kör följande kommando.

```bash
ulimit -a
```
Antal max tillåtna öppna filer, som identifieras som ”nofile” måste vara minst dubbelt din poolstorlek för användaranslutning. Mer information finns i [prestandatips](performance-tips-async-java.md).

##### <a name="snat"></a>Portöverbelastning Azure SNAT (PAT)

Om din app distribueras på Azure virtuella datorer utan en offentlig IP-adress som standard [Azure SNAT portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) upprätta anslutningar till valfri slutpunkt utanför den virtuella datorn. Antalet tillåtna anslutningar från den virtuella datorn till Azure Cosmos DB-slutpunkten är begränsat av den [Azure SNAT configuration](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT portar används endast när den virtuella datorn har en privat IP-adress och en process från den virtuella datorn försöker ansluta till en offentlig IP-adress. Det finns två sätt att undvika Azure SNAT begränsning:

* Lägga till ditt Azure Cosmos DB-tjänstslutpunkt i undernätet för det virtuella nätverket i Azure Virtual Machines. Mer information finns i [Azure Virtual Network-tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    När tjänsteslutpunkt är aktiverat skickas inte längre begäranden från en offentlig IP-adress till Azure Cosmos DB. Istället skickas det virtuella nätverket och undernätet identitet. Den här ändringen kan resultera i brandväggen släpper om endast offentliga IP-adresser tillåts. Om du använder en brandvägg, när du aktiverar tjänstslutpunkten, lägga till ett undernät i brandväggen med hjälp av [ACL: er med virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Tilldela en offentlig IP-adress till den virtuella Azure-datorn.

#### <a name="http-proxy"></a>HTTP-proxy

Om du använder en HTTP-proxy, kontrollera att den har stöd för antalet anslutningar som konfigurerats i SDK `ConnectionPolicy`.
Annars kan du står inför problem med anslutningen.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ogiltig kodning mönster: Blockera Netty i/o-tråd

SDK: N använder den [Netty](https://netty.io/) i/o-biblioteket för att kommunicera med Azure Cosmos DB. SDK: N har Async APIs och icke-blockerande Netty-i/o-API: er. SDK-i/o arbetet utförs i i/o-Netty trådar. Antalet i/o-Netty trådar är konfigurerad för att vara samma som antalet CPU-kärnor för app-datorn. 

Netty-i/o-trådar är avsedda att användas endast för icke-blockerande Netty i/o-arbete. SDK: N returnerar resultatet för API-anrop på en av Netty i/o-trådar appens kod. Om appen utför en åtgärd för långvariga när den har fått resultat på Netty tråden, kanske inte tillräckligt många i/o-trådar för att utföra arbetet internt i/o i SDK: N. Sådana app kodning kan leda till lågt dataflöde, lång svarstid och `io.netty.handler.timeout.ReadTimeoutException` fel. Lösningen är att växla tråden när du vet att åtgärden tar tid.

Till exempel ta en titt på följande kodavsnitt. Du kan utföra långvariga arbete som tar mer än några millisekunder för Netty tråden. I så, fall får så småningom du i ett tillstånd där ingen Netty-i/o-tråd förekommer att bearbeta i/o-arbetsuppgifter. Därför kan få du ett ReadTimeoutException-fel.
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
   Lösningen är att ändra den tråd som du utför arbete som tar tid. Definiera en singleton-instans av scheduler för din app.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Du kan behöva fungerar som tar tid, till exempel beräkningsmässigt tung arbets- eller blockerar IO. I det här fallet växla tråden till en arbetsroll som tillhandahålls av din `customScheduler` med hjälp av den `.observeOn(customScheduler)` API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Med hjälp av `observeOn(customScheduler)`, du släpper Netty i/o-trådar och växla till dina egna anpassade tråd som tillhandahålls av anpassade scheduler. Den här ändringen löser problemet. Du kommer inte få en `io.netty.handler.timeout.ReadTimeoutException` fel längre.

### <a name="connection-pool-exhausted-issue"></a>Anslutningspool förbrukat problemet

`PoolExhaustedException` är ett fel på klientsidan. Det här felet indikerar att din app-arbetsbelastning är högre än vad anslutningspoolen SDK kan fungera. Öka storlek eller distribuera belastningen på flera appar.

### <a name="request-rate-too-large"></a>Frågehastigheten är för stor
Det här felet är ett fel på serversidan. Det anger du förbrukat det etablerade dataflödet. Försök igen senare. Om du får det här felet ofta kan du överväga att en ökning på samlingsdataflödet.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Går inte att ansluta till Azure Cosmos DB-emulator

Azure Cosmos DB-emulatorn HTTPS-certifikatet är självsignerat. Importera emulatorn certifikatet till en Java-TrustStore för SDK att arbeta med emulatorn. Mer information finns i [exportera Azure Cosmos DB-emulatorcertifikat](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Aktivera loggning för klient-SDK

SDK för Java-Async-använder SLF4j som loggning fasad som stöder inloggning till populära loggningsramverk, till exempel log4j och logback.

Till exempel om du vill använda log4j som vilket loggningsramverk lägger du till följande libs i Java-klassökvägen.

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

Lägg även till en log4j-konfiguration.
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

Mer information finns i den [sfl4j loggning manuell](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>OS-nätverksstatistik
Kör netstat-kommando för att få en uppfattning om hur många anslutningar är i ett tillstånd som `ESTABLISHED` och `CLOSE_WAIT`.

På Linux, kan du köra följande kommando.
```bash
netstat -nap
```
Filtrera resultatet ska endast anslutningar till Azure Cosmos DB-slutpunkten.

Antalet anslutningar till Azure Cosmos DB-slutpunkt i den `ESTABLISHED` tillstånd får inte vara större än den konfigurerade storleken på administratörsanslutningspool.

Många anslutningar till Azure Cosmos DB-slutpunkten kan finnas i den `CLOSE_WAIT` tillstånd. Det kan finnas fler än 1 000. Ett tal som är så hög indikerar att anslutningar har upprättats och datakanalen snabbt. Den här situationen orsakar potentiellt problem. Mer information finns i den [vanliga problem och lösningar] avsnittet.

 <!--Anchors-->
[Vanliga problem och lösningar]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Anslutningsgränsen på en värddator]: #connection-limit-on-host
[Portöverbelastning Azure SNAT (PAT)]: #snat


