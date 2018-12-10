---
title: Skapa en internetuppkopplad belastningsutjämnare för Azure cloud services
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en Internetuppkopplad lastbalanserare i den klassiska distributionsmodellen för molntjänster
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 66c978a7eb151ce9df939a11e2e3c0016c8e7c9e
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138100"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Komma igång med att skapa en Internetuppkopplad lastbalanserare för molntjänster

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du vet att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad lastbalanserare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

Molntjänster konfigureras automatiskt med en lastbalanserare och kan anpassas via tjänstmodellen.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Skapa en lastbalanserare med hjälp av tjänstdefinitionsfilen

Du kan uppdatera dina molntjänster med hjälp av Azure SDK för .NET 2.5. Slutpunktsinställningarna för molntjänster görs i .csdef-[tjänstdefinitionsfilen](https://msdn.microsoft.com/library/azure/gg557553.aspx).

Följande exempel visar hur en .csdef-tjänstdefinitionsfil för en molndistribution konfigureras:

Genom att granska kodfragmentet för .csdef-filen som genereras av en molndistribution kan du se att den externa slutpunkten har konfigurerats att använda HTTP på port 10000, 10001 och 10002.

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Kontrollera hälsostatusen för lastbalanseraren för molntjänster

Följande är ett exempel på en hälsoavsökning:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

Lastbalanseraren kombinerar informationen om slutpunkten och informationen om avsökningen för att skapa en URL i formatet `http://{DIP of VM}:80/Probe.aspx` som kan användas för att köra frågor relaterade till tjänstens hälsotillstånd.

Tjänsten identifierar regelbundna avsökningar från samma IP-adress. Den här är hälsoavsökningsbegäran kommer från värden för noden där den virtuella datorn körs. Tjänsten måste svara med en HTTP 200-statuskod för att lastbalanseraren ska tolka tjänstens hälsostatus som felfri. Vid andra HTTP-statuskoder (till exempel 503) tas den virtuella datorn direkt bort från roteringen.

Avsökningsdefinitionen kontrollerar även avsökningsfrekvensen. I exemplet ovan avsöker lastbalanseraren slutpunkten var femte sekund. Om inget positivt svar tas emot inom tio sekunder (två avsökningsintervall) tolkas det som att avsökningen misslyckades och den virtuella datorn tas bort från roteringen. På motsvarande sätt, om tjänsten inte ingår i roteringen och ett positivt svar tas emot, så läggs tjänsten genast till i roteringen igen. Om tjänsten växlar mellan felfritt och icke felfritt tillstånd kan lastbalanseraren besluta att vänta med att föra in tjänsten i roteringen igen tills den har varit felfri under ett visst antal avsökningar.

Kontrollera tjänstdefinitionsschemat för [hälsotillståndsavsökning](https://msdn.microsoft.com/library/azure/jj151530.aspx) om du vill ha mer information.

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern lastbalanserare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)

