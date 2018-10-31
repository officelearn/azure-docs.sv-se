---
title: Diagnostisera och felsöka Azure Cosmos DB Java Async SDK | Microsoft Docs
description: Använd funktioner som loggning på klientsidan och andra tredjepartsverktyg för att identifiera, diagnostisera och felsöka problem med Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshoot
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ef1d2d0751bf1b1a7ee88fbf37e44e6316dee8f8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249885"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Felsökning av problem när du använder Async-SDK för Java med Azure Cosmos DB SQL API-konton
Den här artikeln beskriver vanliga problem, lösningar, diagnostik steg och verktyg när du använder [Java Async ADK](sql-api-sdk-async-java.md) med Azure Cosmos DB SQL API-konton.
Java Async SDK tillhandahåller klientsidan logisk representation för att komma åt Azure Cosmos DB SQL API. Den här artikeln beskrivs de verktyg och metoder för att hjälpa dig om du stöter på problem.

Börja med den här listan:
    * Ta en titt på de [vanliga problem och lösningar] i den här artikeln.
    * Vår SDK är [öppen källkod på github](https://github.com/Azure/azure-cosmosdb-java) har vi [utfärdar avsnittet](https://github.com/Azure/azure-cosmosdb-java/issues) som vi övervakar aktivt. Kontrollera om du hittar alla liknande problem med en lösning som redan har arkiverats.
    * Granska [prestandatips](performance-tips-async-java.md) och följ de föreslagna metoderna.
    * Följ resten av den här artikeln om du inte hittar en lösning, en [GitHub-ärende](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Vanliga problem och lösningar

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Nätverksproblem, Netty läsa timeout-fel, lågt dataflöde, svarstider

#### <a name="general-suggestions"></a>Allmänna förslag
* Kontrollera att programmet körs i samma region som ditt Cosmos DB-konto. 
* Kontrollera processoranvändningen på den värd där appen körs. Om processoranvändningen är 90% eller mer, Överväg att köra din app på en värd med högre konfiguration eller distribuera belastningen på flera datorer.

#### <a name="connection-throttling"></a>Anslutningsbegränsning
Anslutningsbegränsning kan inträffa på grund av något [anslutningsgränsen på värddatorn], eller [Portöverbelastning Azure SNAT (PAT)]:

##### <a name="connection-limit-on-host"></a>Anslutningsgränsen på värddatorn
Vissa Linux-system (till exempel ”Red Hat”) har en övre gräns för totalt antal öppna filer. Sockets i Linux implementeras som filer, så det här talet begränsar det totala antalet anslutningar för.
Kör följande kommando:

```bash
ulimit -a
```
Antal öppna filer (”nofile”) måste vara tillräckligt stor för (på minst som dubbelt som poolstorlek för användaranslutning). Läs mer ingående i [prestandatips](performance-tips-async-java.md).

##### <a name="snat"></a>Portöverbelastning Azure SNAT (PAT)

Om din app distribueras på Azure-dator, som standard [Azure SNAT portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) används för att upprätta anslutningar till valfri slutpunkt utanför den virtuella datorn. Antalet tillåtna anslutningar från den virtuella datorn till Cosmos DB-slutpunkten är begränsat av den [Azure SNAT configuration](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

Azure SNAT-portar används endast när den virtuella Azure-datorn har en privat IP-adress och en process från den virtuella datorn försöker upprätta en anslutning till en offentlig IP-adress. Det finns därför två lösningar för att undvika Azure SNAT begränsning:
    * Lägger till ditt Azure Cosmos DB-tjänstslutpunkt i undernät för ditt Azure VM-VNET som beskrivs i [aktiverar VNET-tjänstslutpunkt](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). När tjänsteslutpunkt har aktiverats kan begäranden inte längre skickas från en offentlig IP-adress till cosmos DB i stället det virtuella nätverket och undernätet identitet skickas. Den här ändringen kan resultera i brandväggen släpper om endast offentliga IP-adresser tillåts. Om du använder-brandväggen när du aktiverar tjänstslutpunkten, Lägg till undernät om du vill använda i brandväggen [VNET ACL: er](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
    * Tilldela en offentlig IP-adress till den virtuella Azure-datorn.

#### <a name="http-proxy"></a>HTTP-proxy

Om du använder en HttpProxy, se till att antalet anslutningar som konfigurerats i SDK har stöd för din HttpProxy `ConnectionPolicy`.
Annars kan du står inför problem med anslutningen.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ogiltig kodning mönster: blockerar Netty i/o-tråd

SDK: N använder [Netty](https://netty.io/) i/o-biblioteket för att kommunicera med Azure Cosmos DB-tjänsten. Vi har Async-API och vi använder icke-blockerande i/o-API: er för netty. SDK-i/o-arbetet utförs i netty i/o-trådar. Antalet i/o netty trådar är konfigurerad för att vara samma som antalet CPU-kärnor för app-datorn. Netty i/o-trådar är endast avsedda att användas för icke-blockerande netty i/o-arbete. SDK: N returnerar resultatet för API-anrop på en av de netty i/o-trådarna i appar-kod. Om appen när du har fått resultat på netty tråden utför en hållbara för netty tråden, som kan leda till SDK för att inte har tillräckligt med antal i/o-trådar för att utföra sitt internt i/o-jobb. Sådana app kodning kan leda till lågt dataflöde, lång svarstid och `io.netty.handler.timeout.ReadTimeoutException` fel. Lösningen är att växla tråden när du vet att åtgärden tar tid.

   Följande kodavsnitt visar till exempel att om du utför hållbara arbetet, vilket tar mer än några millisekunder för netty tråden du så småningom hamna i ett tillstånd där det finns ingen netty i/o-tråd att bearbeta arbetsuppgifter för i/o, och därför får du ReadTimeou tException:
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
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
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
   Lösningen är att ändra tråd som du utför tid med arbete. Definiera en singleton-instans av Scheduler för din app:
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   När du behöver tid att möjliggöra arbete (till exempel beräkningsmässigt tunga arbetet, blockerar i/o) kan du växla tråden till en arbetsroll som tillhandahålls av din `customScheduler` med `.observeOn(customScheduler)` API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
Med hjälp av `observeOn(customScheduler)`, du släpper den netty i/o-tråden och växla till dina egna anpassade tråd som tillhandahålls av customScheduler. Den här ändringen kommer att lösa problemet och du inte får `io.netty.handler.timeout.ReadTimeoutException` fel längre.

### <a name="connection-pool-exhausted-issue"></a>Anslutningspool förbrukat problemet

`PoolExhaustedException` är ett fel på klientsidan. Om du får det här felet ofta är indikation på att din app-arbetsbelastning är högre än vad SDK anslutningspoolen kan fungera. Öka poolstorlek för användaranslutning eller distribuering av belastningen på flera appar kan hjälpa.

### <a name="request-rate-too-large"></a>Frågehastigheten är för stor
Det här felet är ett serversidan fel som indikerar att du förbrukat det etablerade dataflödet och bör försöka senare. Om du får det här felet ofta kan du överväga att öka på samlingsdataflödet.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Går inte att ansluta till Azure Cosmos DB-emulator

Cosmos DB-emulatorn HTTPS-certifikatet är självsignerat. För SDK om du vill arbeta med emulatorn som du ska importera emulatorn certifikatet till Java TrustStore. Enligt beskrivningen [här](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Aktivera loggning för klient-SDK

Async Java SDK använder SLF4j som loggning fasad som stöder inloggning till populära loggningsramverk, till exempel log4j och logback.

Till exempel om du vill använda log4j som vilket loggningsramverk lägger du till följande libs i Java-klassökvägen:

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

Lägg även till en log4j-konfiguration:
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

Granska [sfl4j loggning manuell](https://www.slf4j.org/manual.html) för mer information.

## <a name="netstats"></a>OS-nätverksstatistik
Kör netstat-kommando för att få en uppfattning om hur många anslutningar finns i `Established` tillstånd, `CLOSE_WAIT` tillstånd osv.

I Linux kan du köra följande kommando:
```bash
netstat -nap
```
Filtrera resultatet ska endast anslutningar till Cosmos DB-slutpunkt.

Uppenbarligen, antalet anslutningar till Cosmos DB-slutpunkt i `Established` tillstånd bör inte vara större än den konfigurerade storleken på administratörsanslutningspool.

Om det finns många anslutningar till Cosmos DB-slutpunkt i `CLOSE_WAIT` tillstånd, för exempel som är mer än 1000 anslutningar, som är en indikation på anslutningar upprättas och datakanalen snabbt, vilket kan medföra problem. Granska [vanliga problem och lösningar] avsnitt för mer information.

 <!--Anchors-->
[Vanliga problem och lösningar]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Anslutningsgränsen på värddatorn]: #connection-limit-on-host
[Portöverbelastning Azure SNAT (PAT)]: #snat


