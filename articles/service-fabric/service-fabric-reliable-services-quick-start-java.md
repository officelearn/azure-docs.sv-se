---
title: "Skapa din första tillförlitliga mikrotjänster för Azure i Java | Microsoft Docs"
description: "Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillståndslösa och tillståndskänsliga tjänster."
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 1ebabe4844732412e04bab8c277f7ebbc4a5737c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
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
