---
title: Service Fabric Cluster Resource Manager - tillhörighet | Microsoft Docs
description: Översikt över konfigurera tillhörighet för Service Fabric-tjänster
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bda70a6854dc6d94d3d4b37e6f587e4dcd045126
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543847"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurera och använda tjänsttillhörighet i Service Fabric
Tillhörighet är en kontroll som har angetts främst för att underlätta övergången av större oflexibla tillämpningar till molnet och mikrotjänster världen. Den används också som en optimering för att förbättra prestanda för tjänster, även om detta kan ha sidoeffekter.

Vi antar att du behöver en större app eller en som bara inte har utformats med mikrotjänster i åtanke till Service Fabric (eller distribuerad miljö). Den här typen av övergången är vanligt. Du startar genom att flytta hela appen i miljön, paketerar den och se till att det fungerar smidigt. Du startar sedan dela upp frågan i olika mindre tjänster att alla kommunicera med varandra.

Så småningom hända att programmet upplever några problem. Problem som faller vanligtvis inom en av dessa kategorier:

1. Vissa komponenten X i appen monolitisk hade en odokumenterade beroende på komponenten Y, och du har aktiverat bara dessa komponenter i separata tjänster. Eftersom de här tjänsterna körs nu på olika noder i klustret, är de bruten.
2. Dessa komponenter kommunicerar (lokal namngivna pipes | delat minne | filer på disk) och de behöver för att kunna skriva till en lokal resurs av prestandaskäl just nu. Det hårda beroendet tas bort senare, kanske.
3. Allt är bra, men det har visat sig att dessa två komponenter är faktiskt trafikintensiva/prestanda känslig. När de flyttas dem i separata tjänster övergripande programprestanda tanked eller svarstid ökar. Därför uppfyller övergripande programmet inte förväntningar.

I dessa fall kan vi vill inte förlora vårt refaktoriseringsaktiviteter arbete och vill inte gå tillbaka till gammal. Senaste villkoret kan även vara önskvärt som en vanlig optimering. Dock ska förrän vi kan göra om komponenter ska fungera naturligt som tjänster (eller tills vi kan lösa prestandaförväntningarna något annat sätt) vi behöver några uppfattning om ort.

Hur ska du göra? Dessutom kan du försöka aktivera tillhörighet.

## <a name="how-to-configure-affinity"></a>Så här konfigurerar du mappning
Om du vill konfigurera tillhörighet kan du definiera en tillhörigheten mellan två olika tjänster. Du kan tänka på tillhörighet som ”pekar” en-tjänst på en annan och säger ”kan endast köras var att tjänsten körs”. Ibland kallas för mappning mellan en överordnad/underordnad relation (där du peka underordnade på överordnat). Det innebär att repliker eller instanser av en tjänst är placerade på samma noderna som innehållet i en annan tjänst.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> En underordnad tjänst kan bara delta i en enda mappning. Om du vill att barnet ska tillhöra två överordnade tjänster på samma gång har ett par alternativ:
> - Omvänd relationerna (har parentService1 och parentService2 som pekar på den aktuella underordnade tjänsten), eller
> - Ange en av överordnade som ett nav enligt konventionen och har alla tjänster som pekar på tjänsten. 
>
> Resultatet placering i klustret ska vara samma.
>

## <a name="different-affinity-options"></a>Alternativ för olika tillhörighet
Tillhörighet representeras via någon av flera scheman för korrelation och har två olika lägen. Det vanligaste tillhörighet-läget är det vi kallar NonAlignedAffinity. I NonAlignedAffinity, är repliker eller instanser av de olika tjänsterna placerade på samma noderna. Det andra läget är AlignedAffinity. Justerade tillhörighet är användbar bara med tillståndskänsliga tjänster. Konfigurera två tillståndskänsliga tjänster om du vill ha justerad tillhörighet säkerställer att USA: s presidentval av dessa tjänster är placerade på samma noder efter varandra. Det gör även varje par med sekundärservrar för dessa tjänster ska kunna placeras på samma noderna. Det är också möjligt (även om mindre fråga om delad) Konfigurera NonAlignedAffinity för tillståndskänsliga tjänster. Olika repliker av två tillståndskänsliga tjänster skulle köras på samma noder för NonAlignedAffinity, men deras USA: s presidentval kan hamna på olika noder.

<center>
![Tillhörighet och deras effekter][Image1]
</center>

### <a name="best-effort-desired-state"></a>Bästa arbete önskat tillstånd
En mappningsrelation mellan är bästa prestanda. Det ger inte samma garantier för samplacering eller tillförlitlighet som körs i samma körbar process gör. Tjänster i en mappning mellan är helt olika entiteter som kan misslyckas och flyttas oberoende av varandra. En mappning kan också innebära, även om dessa radbrytningar är tillfällig. Kapacitetsbegränsningar kan till exempel innebära att bara en del av tjänsten-objekt i mappningsrelation mellan får plats på en viss nod. I dessa fall även om det finns en mappning på plats, kan den inte tillämpas på grund av andra villkor. Om det är möjligt att göra det korrigeras automatiskt överträdelsen senare.

### <a name="chains-vs-stars"></a>Kedjor jämfört med stjärnor
Klusterresurshanteraren har idag inte till modellen kedjor av mappning mellan och relationer. Det innebär som en tjänst som är underordnat i mappning mellan en får inte vara en förälder i en annan mappning. Om du vill utforma den här typen av relation kan måste du effektivt modell som en stjärna i stället för en kedja. Om du vill flytta från en kedja till en stjärna, längst ned, underordnade skulle ha en överordnad till den första underordnade överordnad i stället. Beroende på hur dina tjänster kan du behöva göra detta flera gånger. Om det finns ingen naturlig överordnade tjänst, kan du behöva skapa en som fungerar som platshållare. Beroende på dina krav kan du även vill söka i [programgrupper](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Jämfört med kedjor Stjärnor i samband med tillhörighet relationer][Image2]
</center>

En annan sak att tänka tillhörighet relationer idag är att de är riktad som standard. Det innebär att regeln tillhörighet kommer bara att verkställa att underordnat placerade med överordnat. Det garanterar inte att överordnat finns tillsammans med underordnat. Därför, om det finns en mappning mellan överträdelse och åtgärda överträdelsen av någon anledning inte är möjligt att flytta underordnat till den överordnade noden sedan – även om skulle har korrigerats flytta överordnad till den underordnade noden som överträdelsen--överordnad inte flyttas till th e-underordnad nod. Ange konfig [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) till true skulle ta bort riktningen. Det är också viktigt att notera att mappningsrelation mellan inte perfekt eller direkt tillämpas eftersom olika tjänster har med olika livscykler kan misslyckas och flytta oberoende av varandra. Anta exempelvis att överordnat plötsligt växlar över till en annan nod eftersom den har kraschat. Cluster Resource Manager och Redundanshanteraren hanterar redundansen först, sedan hänger tjänsterna, konsekvent, det tillgängliga är prioritet. När redundansväxlingen är klar mappningsrelation mellan bryts, men Klusterresurshanteraren tror att allt är bra tills den meddelanden där underordnat inte befinner sig med överordnat. Dessa typer av kontroller som utförs med jämna mellanrum. Mer information om hur Cluster Resource Manager utvärderar begränsningar finns i [i den här artikeln](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), och [den här](service-fabric-cluster-resource-manager-balancing.md) berättar mer om hur du konfigurerar det intervall som de här villkoren är utvärderas.   


### <a name="partitioning-support"></a>Partitionering
Den slutliga praktiskt om mappning mellan och är den tillhörighet relationer inte stöds där överordnat är partitionerad. Partitionerade överordnade tjänster kan stödjas så småningom, men idag är det inte tillåtet.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Begränsa tjänster till en liten uppsättning datorer eller aggregering av belastningen på tjänster, använda [programgrupper](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png