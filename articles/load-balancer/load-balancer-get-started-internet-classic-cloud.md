---
title: "Skapa en Internetaktiverad belastningsutjämnare för Azure-molntjänster | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i den klassiska distributionsmodellen för molntjänster"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: b389d9a01db394b79d07ff9c3d6d1cd94e811472
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Komma igång med att skapa en Internetuppkopplad belastningsutjämnare för molntjänster

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du vet att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

Molntjänster konfigureras automatiskt med en belastningsutjämnare och kan anpassas via tjänstmodellen.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Skapa en belastningsutjämnare med hjälp av tjänstdefinitionsfilen

Du kan uppdatera dina molntjänster med hjälp av Azure SDK för .NET 2.5. Slutpunktsinställningarna för molntjänster görs i .csdef-[tjänstdefinitionsfilen](https://msdn.microsoft.com/library/azure/gg557553.aspx).

Följande exempel visar hur en .csdef-tjänstdefinitionsfil för en molndistribution konfigureras:

Genom att granska kodfragmentet för .csdef-filen som genereras av en molndistribution kan du se att den externa slutpunkten har konfigurerats att använda HTTP på port 10000, 10001 och 10002.

```xml
<ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
<Endpoints>
    <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
    <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
    <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

    <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

    <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
        <AllocatePublicPortFrom>
            <FixedPortRange min=“10110” max=“10120“  />
        </AllocatePublicPortFrom>
    </InstanceInputEndpoint>
    <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
</Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Kontrollera hälsostatusen för belastningsutjämnaren för molntjänster

Följande är ett exempel på en hälsoavsökning:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
</LoadBalancerProbes>
```

Belastningsutjämnaren kombinerar informationen om slutpunkten och informationen om avsökningen för att skapa en URL i formatet `http://{DIP of VM}:80/Probe.aspx` som kan användas för att köra frågor relaterade till tjänstens hälsotillstånd.

Tjänsten identifierar regelbundna avsökningar från samma IP-adress. Den här är hälsoavsökningsbegäran kommer från värden för noden där den virtuella datorn körs. Tjänsten måste svara med en HTTP 200-statuskod för att belastningsutjämnaren ska tolka tjänstens hälsostatus som felfri. Vid andra HTTP-statuskoder (till exempel 503) tas den virtuella datorn direkt bort från roteringen.

Avsökningsdefinitionen kontrollerar även avsökningsfrekvensen. I exemplet ovan avsöker belastningsutjämnaren slutpunkten var femte sekund. Om inget positivt svar tas emot inom tio sekunder (två avsökningsintervall) tolkas det som att avsökningen misslyckades och den virtuella datorn tas bort från roteringen. På motsvarande sätt, om tjänsten inte ingår i roteringen och ett positivt svar tas emot, så läggs tjänsten genast till i roteringen igen. Om tjänsten växlar mellan felfritt och icke felfritt tillstånd kan belastningsutjämnaren besluta att vänta med att föra in tjänsten i roteringen igen tills den har varit felfri under ett visst antal avsökningar.

Kontrollera tjänstdefinitionsschemat för [hälsotillståndsavsökning](https://msdn.microsoft.com/library/azure/jj151530.aspx) om du vill ha mer information.

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)

