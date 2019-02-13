---
title: Skapa din första Azure Service Fabric reliable service i Java | Microsoft Docs
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 0b044b15b41e2d74f08c4bc989e22b6a19949445
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170932"
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

Den här artikeln förklarar grunderna i Azure Service Fabric Reliable Services och vägleder dig genom att skapa och distribuera en enkel och tillförlitlig tjänst-App skriven i Java. 

## <a name="installation-and-setup"></a>Installations- och
Innan du börjar bör du kontrollera att du har Service Fabric-utvecklingsmiljö på din dator.
Om du vill konfigurera det kan gå till [komma igång på Mac](service-fabric-get-started-mac.md) eller [komma igång på Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Grundläggande begrepp
Om du vill komma igång med Reliable Services, behöver du bara förstå några grundläggande begrepp:

* **Typ av tjänst**: Det här är din implementering. Den definieras av den klass som du skriver och som utökar `StatelessService` och eventuella andra koden och beroenden som används i, tillsammans med ett namn och ett versionsnummer.
* **Namngiven tjänstinstans**: Om du vill köra tjänsten skapar du namngivna instanser i din tjänsttyp mycket som du skapar objektinstanser av en klasstyp. Instanser av tjänsten är i själva verket objektinstansieringar på din tjänstklass som du skriver.
* **Tjänstevärden**: Namngiven tjänst-instanser som du skapar måste du köra i en värd. Tjänstevärden är bara en process där instanser av din tjänst kan köras.
* **Registrering av tjänst**: Registrering samlar allt. Tjänsttypen måste vara registrerad med Service Fabric-körning i en tjänstevärd så att Service Fabric för att skapa instanser av den att köra.  

## <a name="create-a-stateless-service"></a>Skapa en tillståndslös tjänst
Börja med att skapa ett Service Fabric-program. Service Fabric SDK för Linux finns en Yeoman generator att tillhandahålla ställningarna för ett Service Fabric-program med en tillståndslös tjänst. Starta genom att köra följande Yeoman kommando:

```bash
$ yo azuresfjava
```

Följ anvisningarna för att skapa en **tillståndslös och tillförlitlig tjänst**. Den här självstudien ett namn för programmet ”HelloWorldApplication” och ”HelloWorld”-tjänsten. Resultatet innehåller kataloger för den `HelloWorldApplication` och `HelloWorld`.

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
### <a name="service-registration"></a>Registrering av tjänst
Tjänsttyper måste registreras med Service Fabric-körningen. Tjänsttypen har definierats i den `ServiceManifest.xml` och din tjänstklass som implementerar `StatelessService`. Registrering av tjänst utförs i den huvudsakliga startpunkten i processen. I det här exemplet är den viktigaste process startpunkten `HelloWorldServiceHost.java`:

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

Open **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Den här klassen definierar tjänsttypen och kan köra valfri kod. Tjänst-API: et tillhandahåller två ställen för din kod:

* En kunskapsuppsättning metoden, kallas `runAsync()`, där du kan börja köra alla arbetsbelastningar, inklusive tidskrävande beräkning av arbetsbelastningar.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* En kommunikation startpunkt där du kan ansluta din kommunikationsstack val. Det här är där du kan börja ta emot begäranden från användare och andra tjänster.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

Den här självstudien fokuserar på de `runAsync()` metoden. Det här är där du kan omedelbart börja köra din kod.

### <a name="runasync"></a>RunAsync
Plattformen anropar den här metoden när en instans av en tjänst är placerade och redo att köra. Det innebär att när tjänstinstansen öppnas för för en tillståndslös tjänst. En annullering token har angetts för samordning när din tjänstinstans måste stängas. I Service Fabric, kan den här första/sista cykeln av en tjänstinstans inträffa flera gånger under livslängden för tjänsten som helhet. Detta kan inträffa av olika anledningar, inklusive:

* Systemet flyttar dina service-instanser för resurser.
* Fel uppstår i din kod.
* Programmets eller systemets uppgraderas.
* Den underliggande maskinvaran uppstår ett avbrott.

Denna orkestrering hanteras av Service Fabric för att hålla din tjänst med hög tillgänglighet och korrekt belastningsutjämnade.

`runAsync()` bör inte blockera synkront. Implementeringen av runAsync ska returnera en CompletableFuture för att tillåta körning för att fortsätta. Om din arbetsbelastning behöver implementera en tidskrävande uppgift som bör göras inuti CompletableFuture.

#### <a name="cancellation"></a>Annulleringen
Annullering av din arbetsbelastning är en samverkande ansträngning dirigeras genom den tillhandahållna annullering-token. Systemet väntar tills uppgiften att avsluta (efter slutförande, uppsägning eller fel) innan flyttas på. Det är viktigt att respektera annullering token, Slutför allt arbete och avsluta `runAsync()` så snabbt som möjligt när systemet begär annullering. I följande exempel visar hur du hanterar en annullering händelsen:

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

I det här exemplet tillståndslös tjänst lagras antalet i en lokal variabel. Men eftersom detta är en tillståndslös tjänst kan det värde som lagras finns bara för den aktuella livscykeln för dess tjänstinstans. När tjänsten flyttar eller startar om kan förloras värdet.

## <a name="create-a-stateful-service"></a>Skapa en tillståndskänslig tjänst
Service Fabric introducerar en ny typ av tjänst som är tillståndskänslig. En tillståndskänslig tjänst kan underhålla tillståndet på ett tillförlitligt sätt inom tjänsten samordnad med kod som använder den. Tillstånd görs med hög tillgänglighet av Service Fabric utan att behöva bevara tillstånd till en extern lagring.

Om du vill konvertera ett värde för prestandaräknaren från tillståndslösa till hög tillgänglighet och beständig, även när tjänsten flyttar eller startar om, måste en tillståndskänslig tjänst.

I samma katalog som HelloWorld-program, kan du lägga till en ny tjänst genom att köra den `yo azuresfjava:AddService` kommando. Välj ”tillförlitlig tillståndskänslig tjänst” för ditt ramverk och namnge tjänsten ”HelloWorldStateful”. 

Ditt program bör nu ha två tjänster: den tillståndslösa tjänsten HelloWorld och tillståndskänsliga tjänsten HelloWorldStateful.

En tillståndskänslig tjänst har samma startpunkter som en tillståndslös tjänst. Den största skillnaden är tillgängligheten för en tillståndsprovider som lagrar tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en implementering av databasleverantör som tillståndet kallas tillförlitliga samlingar som du kan skapa replikerade datastrukturer via Reliable State Manager. En tillståndskänslig tillförlitlig tjänst använder den här tillståndsprovider som standard.

Öppna HelloWorldStateful.java i **HelloWorldStateful src ->**, som innehåller följande RunAsync-metod:

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
`RunAsync()` fungerar på samma sätt i tillståndskänsliga och tillståndslösa tjänster. Men i en tillståndskänslig tjänst plattformen utför ytterligare arbete för din räkning innan den kan köras `RunAsync()`. Arbetet kan inkludera vilket säkerställer att Reliable State Manager och Reliable Collections är redo att använda.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap) är en ordlista-implementering som du kan använda för att på ett tillförlitligt sätt lagrar tillstånd i tjänsten. Du kan lagra data direkt i din tjänst utan att behöva en extern beständigt Arkiv med Service Fabric och tillförlitlig HashMaps. Tillförlitlig HashMaps gör dina data med hög tillgänglighet. Service Fabric åstadkommer detta genom att skapa och hantera flera *repliker* av din tjänst för dig. Det ger också ett API som avlägsnar komplex hantering av dessa repliker och ändrar deras status.

Tillförlitliga samlingar kan lagra alla Java-datatypen, inklusive din anpassade typer med ett par varningar:

* Service Fabric gör din delstat med hög tillgänglighet av *replikera* tillstånd på noder och tillförlitlig HashMap lagrar dina data till lokal disk på varje replik. Det innebär att allt som är lagrad i tillförlitliga HashMaps måste vara *serialiserbara*. 
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på tillförlitliga HashMaps. Objekt som lagras i tillförlitliga HashMaps sparas i lokalt minne i din tjänst. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte mutera lokala instanser av dessa objekt utan att utföra en Uppdateringsåtgärd i tillförlitlig samling i en transaktion. Det beror på att ändringar i lokala instanser av objekt inte replikeras automatiskt. Du måste komma in objektet i ordlistan igen eller Använd en av de *uppdatera* metoder i ordlistan.

Reliable State Manager hanterar tillförlitlig HashMaps åt dig. Du kan be Reliable State Manager för en tillförlitlig samling med namnet när som helst och var som helst i din tjänst. Reliable State Manager ser till att du får en referens tillbaka. Vi rekommenderar inte att du sparar referenser till tillförlitlig samling instanser i klassmedlemsvariabler eller egenskaper. Särskild försiktighet måste vidtas för att se till att referensen har angetts till en instans vid alla tidpunkter i livscykeln för tjänsten. Reliable State Manager hanterar arbetet åt dig och tjänsten är optimerad för upprepningar besök.


### <a name="transactional-and-asynchronous-operations"></a>Transaktions- och asynkrona åtgärder
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

Åtgärder för tillförlitlig HashMaps är asynkrona. Det beror på att skrivåtgärder med Reliable Collections utföra i/o-åtgärder för att replikera och bevara data till disk.

Tillförlitlig HashMap åtgärder är *transaktionell*, så att du kan hålla tillstånd konsekvent över flera tillförlitlig HashMaps och åtgärder. Du kan till exempel få ett arbetsobjekt från en tillförlitlig ordlista, utföra en åtgärd på den och spara resultatet i en annan tillförlitlig HashMap alla inom en enda transaktion. Detta kommer att behandlas som en atomisk åtgärd och det garanterar att hela åtgärden lyckas eller hela åtgärden återställs. Om felet inträffar efter att du ta bort från kön objektet, men innan du sparar resultatet, återställs hela transaktionen och objektet som finns kvar i kön för bearbetning.


## <a name="build-the-application"></a>Skapa programmet

Yeoman ställningar innehåller ett gradle-skript för att skapa programmet och bash-skript för att distribuera och ta bort programmet. Om du vill köra programmet måste du först skapa programmet med gradle:

```bash
$ gradle
```

Detta ger ett Service Fabric-programpaket som kan distribueras med hjälp av Service Fabric CLI.

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
> Om du vill distribuera programmet till ett säkert Linux-kluster i Azure måste du konfigurera ett certifikat för att verifiera ditt program med Service Fabric-körningen. På så sätt kan dina Reliable Services-tjänster att kommunicera med underliggande Service Fabric-körningen API: er. Mer information finns i [konfigurera en Reliable Services-app som körs i Linux-kluster](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Service Fabric CLI](service-fabric-cli.md)
