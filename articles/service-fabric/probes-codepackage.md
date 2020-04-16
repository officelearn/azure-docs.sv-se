---
title: Azure Service Fabric-avsökningar
description: Så här modellerar du Liveness Probe i Azure Service Fabric med hjälp av program- och tjänstmanifestfiler.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431220"
---
# <a name="liveness-probe"></a>Liveness Probe
Från och med 7.1 Service Fabric stöder Liveness Probe mekanism för [containeriserade][containers-introduction-link] applikationer. Liveness Probe hjälper till att tillkännage liveness av containerized ansökan och när de inte svarar i tid, kommer det att resultera i en omstart.
Den här artikeln innehåller en översikt över hur du definierar en Liveness Probe via manifestfiler.

Innan du fortsätter med den här artikeln rekommenderar vi att du bekantar dig med [programmodellen Service Fabric][application-model-link] och [service fabric-värdmodellen][hosting-model-link].

> [!NOTE]
> Liveness Probe stöds endast för behållare i NAT-nätverksläge.

## <a name="semantics"></a>Semantics
Du kan bara ange 1 Liveness Probe per behållare och kan styra dess beteende med dessa fält:

* `initialDelaySeconds`: Den första fördröjningen i sekunder för att börja köra sonden när behållaren har startat. Värdet som stöds är int. Standard är 0. Minimum är 0.

* `timeoutSeconds`: Period i sekunder varefter vi anser att avsökningen misslyckades om den inte har slutförts. Värdet som stöds är int. Standard är 1. Minimum är 1.

* `periodSeconds`: Period i sekunder för att ange hur ofta vi sond. Värdet som stöds är int. Standard är 10. Minimum är 1.

* `failureThreshold`: När vi träffar FailureThreshold startar behållaren om. Värdet som stöds är int. Standard är 3. Minimum är 1.

* `successThreshold`: Vid fel, för att sonden ska anses vara framgångsrik måste den köras framgångsrikt för SuccessThreshold. Värdet som stöds är int. Standard är 1. Minimum är 1.

Det kommer att finnas högst 1 sond till behållare vid ett tillfälle. Om sonden inte slutförs i **timeoutSeconds** vi fortsätter att vänta och räknar den mot **misslyckandetThreshold**. 

Dessutom kommer ServiceFabric att höja följande [avsökningshälsorapporter][health-introduction-link] om DeployedServicePackage:

* `Ok`: Om sonden lyckas **framgångTröskar** då vi rapporterar hälsa som Ok.

* `Error`: Om avsökningen misslyckadesCount == **failureThreshold,** innan du startar om behållaren rapporterar vi Fel.

* `Warning`: 
    1. Om avsökningen misslyckas och feletCount < **misslyckandeDröskar** vi Varning. Den här hälsorapporten stannar tills failureCount når **felReshold** eller **successThreshold**.
    2. Vid framgång efter misslyckande rapporterar vi fortfarande Varning men med uppdaterad framgång i följd.

## <a name="specifying-liveness-probe"></a>Ange Liveness Probe

Du kan ange avsökning i ApplicationManifest.xml under ServiceManifestImport:

Probe kan antingen en av:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP-avsökning

För HTTP-avsökning skickar Service Fabric en HTTP-begäran till den angivna porten och sökvägen. Returkod som är större än eller lika med 200 och mindre än 400 anger framgång.

Här är ett exempel på hur du anger HttpGet-avsökning:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HttpGet-avsökningen har ytterligare egenskaper som du kan ange:

* `path`: Sökväg till åtkomst på HTTP-begäran.

* `port`: Port för åtkomst för sonder. Räckvidden är 1 till 65535. Obligatorisk.

* `scheme`: Schema som ska användas för anslutning till kodpaket. Om den är inställd på HTTPS hoppas certifikatverifieringen över. Standardvärden till HTTP

* `httpHeader`: Rubriker som ska anges i begäran. Du kan ange flera av dessa.

* `host`: Värd-IP att ansluta till.

## <a name="tcp-probe"></a>TCP-sond

För TCP-avsökning försöker Service Fabric öppna ett uttag på behållaren med den angivna porten. Om den kan upprätta en anslutning anses avsökningen vara framgångsrik. Här är ett exempel på hur du anger avsökning som använder TCP-socket:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Exec sond

Den här avsökningen utfärdar en exec i behållaren och väntar på att kommandot ska slutföras.

> [!NOTE]
> Exec kommandot tar en komma separat sträng. Följande kommando i exemplet fungerar för Linux-behållaren.
> Om du försöker windows-behållaren använder du <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar för relaterad information.
* [Service tyg och behållare.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

