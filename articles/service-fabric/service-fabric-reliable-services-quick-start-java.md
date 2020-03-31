---
title: Skapa din första pålitliga tjänst i Java
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: c3b301a7a9039f1fe8095950f0a5a4e23eb52a9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614221"
---
# <a name="get-started-with-reliable-services-in-java"></a>Komma igång med reliable services i Java
> [!div class="op_single_selector"]
> * [C# på Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

I den här artikeln beskrivs grunderna i Azure Service Fabric Reliable Services och du får hjälp med att skapa och distribuera ett enkelt reliable service-program skrivet i Java. 

## <a name="installation-and-setup"></a>Installation och installation
Innan du börjar kontrollerar du att du har konfigurerat utvecklingsmiljön för Service Fabric på datorn.
Om du behöver konfigurera den går du till komma [igång på Mac](service-fabric-get-started-mac.md) eller kommer igång på [Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundläggande begrepp
För att komma igång med Reliable Services behöver du bara förstå några grundläggande begrepp:

* **Tjänsttyp**: Det här är din tjänstimplementering. Den definieras av den klass du `StatelessService` skriver som utökar och alla andra kod eller beroenden som används där, tillsammans med ett namn och ett versionsnummer.
* **Namngiven tjänstinstans:** Om du vill köra tjänsten skapar du namngivna instanser av tjänsttypen, ungefär som du skapar objektinstanser av en klasstyp. Tjänstinstanser är i själva verket objektinpassningar för din tjänstklass som du skriver.
* **Tjänstvärd**: De namngivna tjänstinstanser som du skapar måste köras inuti en värd. Tjänstvärden är bara en process där instanser av tjänsten kan köras.
* **Serviceregistrering**: Registrering samlar allt. Tjänsttypen måste vara registrerad med Service Fabric-körningen i en tjänstvärd för att Service Fabric ska kunna skapa instanser av den för att köras.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst
Börja med att skapa ett Service Fabric-program. Service Fabric SDK för Linux innehåller en Yeoman-generator för att förse byggnadsställningar för ett Service Fabric-program med en tillståndslös tjänst. Börja med att köra följande Yeoman-kommando:

```bash
$ yo azuresfjava
```

Följ instruktionerna för att skapa en **tillförlitlig tillståndslös tjänst**. För den här guiden, namnge programmet "HelloWorldApplication" och tjänsten "HelloWorld". Resultatet inkluderar kataloger `HelloWorldApplication` för `HelloWorld`och .

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
### <a name="service-registration"></a>Registrering av tjänsten
Tjänsttyper måste registreras hos Service Fabric-körningen. Tjänsttypen definieras i `ServiceManifest.xml` den och din `StatelessService`serviceklass som implementerar . Serviceregistrering utförs i processens huvudstartpunkt. I det här exemplet är `HelloWorldServiceHost.java`processens huvudstartpunkt:

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

Öppna **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Den här klassen definierar tjänsttypen och kan köra vilken kod som helst. Tjänst-API:et innehåller två startpunkter för koden:

* En öppen startpunktsmetod, `runAsync()`kallad , där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkningsarbetsbelastningar.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* En kommunikationsinträde där du kan koppla in din kommunikationsstack. Här kan du börja ta emot förfrågningar från användare och andra tjänster.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Den här självstudien fokuserar på startpunktsmetoden. `runAsync()` Här kan du omedelbart börja köra koden.

### <a name="runasync"></a>RunAsync (KörAsync)
Plattformen anropar den här metoden när en instans av en tjänst är placerad och redo att köras. För en tillståndslös tjänst innebär det när tjänstinstansen öppnas. En avbokningstoken tillhandahålls för att samordna när din tjänstinstans måste stängas. I Service Fabric kan den här öppna/nära cykeln för en tjänstinstans inträffa många gånger under tjänstens livstid som helhet. Detta kan hända av olika skäl, inklusive:

* Systemet flyttar dina tjänstinstanser för resursbalansering.
* Fel uppstår i koden.
* Programmet eller systemet uppgraderas.
* Den underliggande maskinvaran upplever ett avbrott.

Denna orkestrering hanteras av Service Fabric för att hålla din tjänst mycket tillgänglig och korrekt balanserad.

`runAsync()`bör inte blockera synkront. Implementeringen av runAsync bör returnera en CompletableFuture så att körningen kan fortsätta. Om din arbetsbelastning behöver implementera en tidskrävande uppgift som ska göras inuti CompletableFuture.

#### <a name="cancellation"></a>Annullering
Annullering av din arbetsbelastning är ett samarbete som iscensatts av den angivna annulleringstoken. Systemet väntar på att aktiviteten ska avslutas (genom att slutföra, avbryta eller fel) innan den går vidare. Det är viktigt att du respekterar annulleringstoken, slutför allt arbete och avslutar `runAsync()` så snabbt som möjligt när systemet begär annullering. I följande exempel visas hur du hanterar en avbokningshändelse:

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

I det här tillståndslösa serviceexemplet lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst finns det värde som lagras endast för den aktuella livscykeln för tjänstinstansen. När tjänsten flyttas eller startas om går värdet förlorat.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig tjänst kan underhålla tillståndet på ett tillförlitligt sätt inom själva tjänsten, samlokaliserade med koden som använder den. Tillståndet görs mycket tillgängligt av Service Fabric utan att behöva spara tillstånd till ett externt arkiv.

Om du vill konvertera ett räknarvärde från tillståndsfritt till högtillgängligt och beständigt, även när tjänsten flyttas eller startas om, behöver du en tillståndskänslig tjänst.

I samma katalog som HelloWorld-programmet kan du lägga `yo azuresfjava:AddService` till en ny tjänst genom att köra kommandot. Välj "Reliable Stateful Service" för ditt ramverk och namnge tjänsten "HelloWorldStateful". 

Din ansökan bör nu ha två tjänster: den statslösa tjänsten HelloWorld och den tillståndskänsliga tjänsten HelloWorldStateful.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för en tillståndsprovider som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en tillståndsprovider implementering som kallas Tillförlitliga samlingar, som låter dig skapa replikerade datastrukturer via Reliable State Manager. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovidern som standard.

Öppna HelloWorldStateful.java i **HelloWorldStateful -> src**, som innehåller följande RunAsync-metod:

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

### <a name="runasync"></a>RunAsync (KörAsync)
`RunAsync()`fungerar på samma sätt i statskänsliga och statslösa tjänster. Men i en tillståndskänslig tjänst utför plattformen ytterligare arbete för `RunAsync()`din räkning innan den körs. Det här arbetet kan omfatta att se till att Reliable State Manager och tillförlitliga samlingar är redo att användas.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och den pålitliga statschefen
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) är en ordlisteimplementering som du kan använda för att lagra tillståndet på ett tillförlitligt sätt i tjänsten. Med Service Fabric och Reliable HashMaps kan du lagra data direkt i din tjänst utan att behöva ett externt beständigt arkiv. Tillförlitliga HashMaps gör dina data mycket tillgängliga. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av din tjänst åt dig. Det ger också ett API som abstraherar bort komplexiteten i att hantera dessa repliker och deras tillståndsövergångar.

Tillförlitliga samlingar kan lagra alla Java-typer, inklusive dina anpassade typer, med ett par förbehåll:

* Service Fabric gör ditt tillstånd mycket tillgängligt genom *att replikera* tillstånd över noder, och Reliable HashMap lagrar dina data till lokal disk på varje replik. Detta innebär att allt som lagras i Reliable HashMaps måste vara *serialiserbara*. 
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på tillförlitliga HashMaps. Objekt som lagras i Tillförlitliga HashMaps lagras i det lokala minnet i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte muterar lokala instanser av dessa objekt utan att utföra en uppdateringsåtgärd på den tillförlitliga samlingen i en transaktion. Detta beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste återföra objektet till ordlistan igen eller använda någon av *uppdateringsmetoderna* i ordlistan.

Reliable State Manager hanterar tillförlitliga HashMaps åt dig. Du kan när som helst och var som helst i tjänsten be Reliable State Manager om en tillförlitlig samling efter namn. Reliable State Manager ser till att du får en referens tillbaka. Vi rekommenderar inte att du sparar referenser till tillförlitliga samlingsinstanser i klassmedlemsvariabler eller egenskaper. Särskild försiktighet måste iakttas för att säkerställa att referensen alltid är inställd på en instans under tjänstens livscykel. Reliable State Manager hanterar det här arbetet åt dig och är optimerad för upprepade besök.


### <a name="transactional-and-asynchronous-operations"></a>Transaktionella och asynkrona åtgärder
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

Åtgärder på tillförlitliga HashMaps är asynkrona. Detta beror på att skrivåtgärder med tillförlitliga samlingar utför I/O-åtgärder för att replikera och bevara data till disken.

Tillförlitliga HashMap-åtgärder är *transaktionella*, så att du kan hålla tillståndet konsekvent över flera tillförlitliga HashMaps och åtgärder. Du kan till exempel hämta ett arbetsobjekt från en tillförlitlig ordlista, utföra en åtgärd på den och spara resultatet i en annan tillförlitlig HashMap, allt inom en enda transaktion. Detta behandlas som en atomoperation och garanterar att antingen hela operationen lyckas eller att hela operationen återställs. Om ett fel uppstår efter att du har dequeue objektet men innan du sparar resultatet, hela transaktionen återställs och objektet finns kvar i kön för bearbetning.


## <a name="build-the-application"></a>Skapa programmet

Yeoman-byggnadsställningen innehåller ett gradle-skript för att skapa programmet och bash-skript för att distribuera och ta bort programmet. Om du vill köra programmet skapar du först programmet med gradle:

```bash
$ gradle
```

Detta skapar ett Programpaket för Service Fabric som kan distribueras med Service Fabric CLI.

## <a name="deploy-the-application"></a>Distribuera programmet

När du har skapat programmet kan du distribuera det till det lokala klustret.

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Du hittar parametrarna till de här kommandona i de genererade manifesten i programpaketet.

När programmet har distribuerats öppnar du en webbläsare och går till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) på [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expandera sedan noden **Program** och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

> [!IMPORTANT]
> Om du vill distribuera programmet till ett säkert Linux-kluster i Azure måste du konfigurera ett certifikat för att validera ditt program med Service Fabric-körningen. Om du gör det kan dina Reliable Services-tjänster kommunicera med de underliggande API:erna för service fabric-körning. Mer information finns i [Konfigurera en reliable services-app som ska köras på Linux-kluster](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
