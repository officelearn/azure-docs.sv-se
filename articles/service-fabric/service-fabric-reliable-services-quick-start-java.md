---
title: "Skapa din första tillförlitliga mikrotjänster för Azure i Java | Microsoft Docs"
description: "Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: ab675207094bc8ee317573192c33c20039780fe2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Den här artikeln beskriver grunderna i Azure Service Fabric Reliable Services och vägleder dig genom att skapa och distribuera en enkel tillförlitliga tjänstprogrammet skriven i Java. Den här Microsoft Virtual Academy video visar även hur du skapar en tillståndslös tillförlitlig tjänst:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Installation och konfiguration
Innan du börjar bör du kontrollera du har Service Fabric-utvecklingsmiljö ställa in på din dator.
Om du behöver konfigurera den går du till [komma igång på Mac](service-fabric-get-started-mac.md) eller [komma igång med Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundläggande begrepp
Om du vill komma igång med Reliable Services, behöver du bara förstå några grundläggande begrepp:

* **Typen tjänst**: det här är din implementering. Den definieras av den klass som utökar du skriver `StatelessService` och annan kod eller beroenden, används tillsammans med ett namn och ett versionsnummer.
* **Med namnet tjänstinstans**: för att köra din tjänst måste du skapa namngivna instanser av service-typen mycket som du skapar objektinstanser av en klasstyp. Instanser av tjänsten är i själva verket objektinstansieringar av din tjänstklass som du skriver.
* **Tjänstvärden**: namngiven tjänstinstanser som du skapar måste köras i en värd. Tjänstevärden är bara en process där instanser av tjänsten kan köras.
* **Registrering av tjänst**: registrering sammanför allt. Tjänsttypen måste registreras med Service Fabric-körning i en tjänstevärd för att tillåta Service Fabric skapar instanser av den att köra.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst
Börja med att skapa ett Service Fabric-program. Service Fabric-SDK för Linux innehåller en Yeoman generator att tillhandahålla scaffold-teknik för ett Service Fabric-program med en tillståndslös tjänst. Starta genom att köra följande Yeoman kommando:

```bash
$ yo azuresfjava
```

Följ instruktionerna för att skapa en **tillförlitliga tillståndslösa tjänsten**. Den här självstudien namn för programmet ”HelloWorldApplication” och ”HelloWorld”-tjänsten. Resultatet innehåller kataloger för den `HelloWorldApplication` och `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>Tjänstregistrering
Tjänsttyper måste registreras med Service Fabric-körning. Service-typen är definierad i den `ServiceManifest.xml` och tjänstklassen som implementerar `StatelessService`. Registreringen för tjänsten utförs i den huvudsakliga startpunkten i processen. I det här exemplet är den process startpunkten `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>Implementera tjänsten

Öppna **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Den här klassen definierar tjänsttypen och köra all kod. API för tjänsten tillhandahåller två startpunkter för din kod:

* En öppen metoden, kallas `runAsync()`, där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkning av arbetsbelastningar.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* En kommunikation startpunkt där du kan ansluta i din kommunikation stack med val. Det är där du kan börja ta emot begäranden från användare och andra tjänster.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

I den här självstudiekursen kommer vi fokusera på den `runAsync()` metoden. Det är där du kan starta direkt köra din kod.

### <a name="runasync"></a>RunAsync
Plattformen som anropar den här metoden när en instans av en tjänst är monterade och redo att köra. För en tillståndslös tjänst innebär som bara när tjänstinstansen öppnas. En token för annullering tillhandahålls koordinera när service-instans måste stängas. I Service Fabric inträffa cykeln Öppna/stänga av en tjänstinstans många gånger under livslängden för tjänsten som helhet. Detta kan inträffa av olika orsaker, bland annat:

* Systemet flyttar instanser av tjänsten för resurser.
* Fel uppstår i koden.
* Programmets eller systemets uppgraderas.
* Den underliggande maskinvaran skulle få ett avbrott.

Den här orchestration hanteras av Service Fabric att hålla din tjänst hög tillgänglighet och korrekt belastningsutjämnade.

`runAsync()`bör inte blockera synkront. Implementeringen av runAsync ska returnera en CompletableFuture för att tillåta körning fortsätta. Om din arbetsbelastning behöver för att implementera en tidskrävande uppgift som bör göras i CompletableFuture.

#### <a name="cancellation"></a>Annullering
Annullering av din arbetsbelastning är en samverkande ansträngning styrd av den angivna annullering-token. Systemet väntar på att aktiviteten slutar (lyckades, avbokning eller fel) innan den flyttas på. Det är viktigt att respektera cancellation-token, Slutför allt arbete och avsluta `runAsync()` så snabbt som möjligt när systemet begär annullering. Exemplet nedan visar hur du hanterar en annullering händelse:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

I det här exemplet tillståndslösa tjänsten lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst är det värde som lagras finns bara för den aktuella livscykeln för dess tjänstinstansen. När tjänsten startas om flyttar går värdet förlorad.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig service kan upprätthålla tillstånd på ett tillförlitligt sätt inom tjänsten samordnad med kod som använder den. Tillstånd görs högtillgänglig av Service Fabric utan att behöva spara tillstånd för att en extern butik.

Om du vill konvertera ett värde för prestandaräknaren från tillståndslös till hög tillgänglighet och beständiga, även när tjänsten flyttas eller startas om, behöver du en tillståndskänslig tjänst.

I samma katalog som programmet HelloWorld, kan du lägga till en ny tjänst genom att köra den `yo azuresfjava:AddService` kommando. Välj ”tillförlitliga Stateful tjänsten” för din framework och namnet tjänsten ”HelloWorldStateful”. 

Ditt program bör nu ha två tjänster: tillståndslösa tjänsten HelloWorld och stateful-HelloWorldStateful.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för tillståndsprovidern som lagrar tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en implementering av provider för tillstånd kallas tillförlitliga samlingar, som kan du skapa replikerade datastrukturer via Hanteraren för tillförlitlig tillstånd. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovidern som standard.

Öppna HelloWorldStateful.java i **HelloWorldStateful src ->**, som innehåller följande RunAsync metod:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()`fungerar på samma sätt i tillståndskänsliga och tillståndslösa tjänster. Dock i en tillståndskänslig service plattformen utför ytterligare arbete för din räkning innan den kör `RunAsync()`. Detta verk kan inkludera att säkerställa att tillförlitliga Tillståndshanterare och tillförlitlig samlingar är redo att användas.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och hanteraren för tillförlitlig tillstånd
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) är en dictionary-implementering som du kan använda för att lagra på ett tillförlitligt sätt tillstånd i tjänsten. Med Service Fabric och tillförlitlig Hashmaps kan lagra du data direkt i din tjänst utan behov av en extern beständiga arkivet. Tillförlitliga Hashmaps gör att dina data är hög tillgänglighet. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av tjänsten för dig. Det ger också en API som avlägsnar direkt svårigheter för att hantera dessa repliker och deras tillståndsövergångar.

Tillförlitliga samlingar kan lagra alla Java-typ, inklusive din anpassade typer med några varningar.

* Service Fabric gör ditt tillstånd hög tillgänglighet av *replikerar* tillstånd mellan noder och tillförlitlig Hashmap lagrar dina data till lokal disk på varje replik. Det innebär att allt som lagras i tillförlitliga Hashmaps måste vara *serialiserbara*. 
* Objekt replikeras för hög tillgänglighet när du gör transaktioner på tillförlitliga Hashmaps. Objekt som lagras i tillförlitliga Hashmaps sparas i lokalt minne i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte mutera lokala instanser av dessa objekt utan att utföra en Uppdateringsåtgärd i den tillförlitliga samlingen i en transaktion. Det beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste infoga objektet tillbaka till ordlistan igen eller Använd en av de *uppdatera* metoder i ordlistan.

Tillförlitliga Tillståndshanterarens hanterar tillförlitliga Hashmaps åt dig. Du kan bara be tillförlitliga Tillståndshanterarens för en tillförlitlig samling med namnet när som helst och var som helst i din tjänst. Den tillförlitliga tillstånd Manager ser till att du får en referens tillbaka. Inte rekommenderar vi att du sparar referenser till tillförlitliga samling instanser i klassmedlem egenskaper eller variabler. Särskild försiktighet måste vidtas för att säkerställa att referensen har angetts till en instans alltid i livscykeln för tjänsten. Tillförlitliga Tillståndshanterarens hanterar det här fungerar och har optimerats för Upprepa besök.


### <a name="transactional-and-asynchronous-operations"></a>Transaktionell och asynkrona åtgärder
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Åtgärder på tillförlitliga Hashmaps är asynkron. Det beror på att skrivåtgärder med tillförlitlig samlingar utföra i/o-åtgärder för att replikera och spara data till disk.

Den tillförlitliga Hashmap åtgärder är *transaktionella*, så att du kan behålla tillstånd konsekvent över flera tillförlitliga Hashmaps och åtgärder. Du kan till exempel få ett arbetsobjekt från en tillförlitlig ordlista, utföra en åtgärd på den och spara resultatet i anoter tillförlitliga Hashmap alla inom en enskild transaktion. Detta behandlas som en atomisk åtgärd och det garanterar att hela åtgärden lyckas eller hela åtgärden återställs. Om ett fel inträffar efter att objektet har status Created men innan du sparar resultatet hela transaktionen återställs och artikeln finns kvar i kön för bearbetning.


## <a name="run-the-application"></a>Köra programmet

Yeoman scaffold-teknik innehåller ett gradle-skript för att skapa programmet och bash-skript för att distribuera och ta bort programmet. Om du vill köra programmet först skapa programmet med gradle:

```bash
$ gradle
```

Detta ger ett Service Fabric-programpaket som kan distribueras med hjälp av Service Fabric CLI.

### <a name="deploy-with-service-fabric-cli"></a>Distribuera med Service Fabric CLI

Install.sh skriptet innehåller nödvändiga Service Fabric CLI-kommandona för att distribuera programpaketet. Kör skriptet install.sh om du vill distribuera programmet.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
