---
title: Service Fabric klustret Resource Manager - tillhörighet | Microsoft Docs
description: Översikt över hur du konfigurerar tillhörighet för Service Fabric-tjänster
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
ms.openlocfilehash: 57abea79a620aa83e16ad4cc2fd78a4294f2b278
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204863"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurera och använda tjänsten tillhörighet i Service Fabric
Tillhörighet är en kontroll som tillhandahålls huvudsakligen för att underlätta övergången av större gigantiska program till molnet och mikrotjänster världen. Det används också som en optimering för att förbättra prestanda för tjänster, även om du gör det kan ha sidoeffekter.

Anta att du tillämpar en större app eller en som bara inte utformad med mikrotjänster i åtanke till Service Fabric (eller distribuerad miljö). Den här typen av övergången är vanligt. Starta genom att lyfta hela appen i miljön, paketera den och se till att det körs. Du startar sedan dela upp frågan i olika mindre tjänster att alla kommunicera med varandra.

Slutligen kan du att programmet har några problem. Problemen indelas vanligtvis i dessa kategorier:

1. Vissa komponenten X i appen monolitisk hade ett odokumenterade beroende på komponenten Y och du har aktiverat precis dessa komponenter till separata tjänster. Eftersom dessa tjänster körs nu på olika noder i klustret, är de bruten.
2. Dessa komponenter kommunicerar (lokal namngivna pipes | delat minne | filer på disk) och de verkligen måste kunna skriva till en lokal resurs av prestandaskäl just nu. Hårda sambandet hämtar togs bort senare, kanske.
3. Allt är bra, men det visar sig att dessa två komponenter är faktiskt chatty och prestanda känslig. När de flyttas dem till olika tjänster övergripande programprestanda tanked eller latens ökade. Övergripande program är därför inte uppfyller förväntningar.

I dessa fall kan vi vill inte förlora våra refaktoriseringsaktiviteter arbete och vill inte gå tillbaka till monolitvolym. Senaste villkoret vara även önskvärt som en vanlig optimering. Tills vi kan göra om komponenter ska fungera naturligt som tjänster (eller kan vi lösa prestandaförväntningarna något annat sätt) är det dags måste vissa uppfattning om ort.

Hur ska du göra? Dessutom kan du aktivera tillhörighet.

## <a name="how-to-configure-affinity"></a>Att konfigurera mappning mellan
Om du vill konfigurera tillhörighet kan du definiera en tillhörigheten mellan två olika tjänster. Du kan tänka dig tillhörighet som ”pekar” en-tjänst på en annan och säger ”den här tjänsten kan endast köras var att tjänsten körs”. Ibland kallas för mappning mellan en överordnad-underordnad relation (där du peka underordnade på överordnat). Det innebär att repliker eller instanser av en tjänst är placerade på samma noder som en annan tjänst.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> En underordnad tjänst kan bara ingå i en enda tillhörigheten. Om du vill lägga till underordnat tillhöra två överordnade tjänster samtidigt har flera olika alternativ:
> - Omvänd relationerna (har parentService1 och parentService2 som pekar på den aktuella underordnade tjänsten), eller
> - Utse en av överordnade som ett nav enligt konventionen och ha alla tjänster som pekar på den tjänsten. 
>
> Resultatet placering i klustret måste vara samma.
>

## <a name="different-affinity-options"></a>Alternativ för olika tillhörighet
Tillhörighet representeras via en av flera scheman för korrelation och har två olika lägen. De vanligaste läget för mappning mellan engelskans NonAlignedAffinity. I NonAlignedAffinity placeras replikeringar eller instanser av olika tjänster på samma noder. Det andra läget är AlignedAffinity. Justerade tillhörighet är användbar bara med tillståndskänsliga tjänster. Konfigurera två tillståndskänsliga tjänster om du vill ha justerad tillhörighet säkerställer att primärfärgerna av tjänsterna är placerade på samma noder efter varandra. Det gör även varje par av sekundärservrar för dessa tjänster kan placeras på samma noder. Det är också möjligt (även om mindre fråga om vanliga) Konfigurera NonAlignedAffinity för tillståndskänsliga tjänster. Olika repliker två tillståndskänsliga tjänster körs på samma noder för NonAlignedAffinity, men deras primärfärgerna kan hamna på olika noder.

<center>
![Tillhörighet och deras effekter][Image1]
</center>

### <a name="best-effort-desired-state"></a>Bästa prestanda önskad tillstånd
En mappningsrelation är bästa prestanda. Den ger inte samma garantier för samplacering eller tillförlitlighet som körs i samma körbara processen. Tjänster i ett tillhörigheten är helt olika enheter som kan misslyckas och flyttas oberoende av varandra. En tillhörigheten kan också dela, även om dessa radbrytningar är tillfällig. Till exempel innebär kapacitetsbegränsningar att endast en del av tjänsten objekten i tillhörigheten får plats på en viss nod. I dessa fall även om det finns en tillhörigheten på plats, kan inte den tillämpas på grund av andra villkor. Om det är möjligt att göra det, korrigeras överträdelse senare.

### <a name="chains-vs-stars"></a>Kedjor kontra stjärnor
Klustret Resource Manager är idag inte kunna modellen kedjor tillhörighet relationer. Det innebär som en tjänst som är underordnat i en tillhörigheten får inte vara en förälder i en annan tillhörigheten. Om du vill att den här typen av relation har du effektivt modellen som en stjärna i stället för en kedja. Om du vill flytta från en kedja till en stjärna, den lägsta underordnat skulle ha en överordnad till den första underordnade överordnad i stället. Beroende på hur dina tjänster kan du behöva göra detta flera gånger. Om det finns ingen fysisk överordnade tjänst, kan du behöva skapa en som fungerar som platshållare. Beroende på dina krav kan du även vill söka i [programgrupper](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Kedjor vs. Stjärnor i samband med tillhörighet relationer][Image2]
</center>

Märke om mappning mellan relationer idag till är att de är riktat. Det innebär att regeln tillhörighet endast tillämpar att underordnat placeras med överordnat. Det garanterar inte att överordnat finns med underordnat. Det är också viktigt att Observera att tillhörigheten inte perfekt eller direkt eftersom olika tjänster har med olika livscykler kan misslyckas och flytta oberoende av varandra. Anta exempelvis att överordnat plötsligt flyttas över till en annan nod eftersom den har kraschat. Hanteraren för filserverresurser och Failover Manager hanterar redundans först sedan hålla tjänsterna, konsekvent, och tillgänglig är prioriteten. När redundansväxlingen är klar på tillhörigheten har brutits, men klustret Resource Manager tror att allt är bra tills den meddelanden underordnad inte är finns med överordnat. Dessa typer av kontroller som utförs med jämna mellanrum. Mer information om hur klustret Resource Manager utvärderar villkor finns i [i den här artikeln](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), och [den här](service-fabric-cluster-resource-manager-balancing.md) berättar mer om hur du konfigurerar det intervall då dessa villkor utvärderas.   


### <a name="partitioning-support"></a>Partitionering
Observera om mappning mellan den sista åtgärd är den tillhörighet relationer inte stöds om överordnat är partitionerad. Partitionerade överordnade tjänster kan stödjas förr eller senare, men idag är det inte tillåtet.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster, [Lär dig mer om hur du konfigurerar tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Att begränsa tjänster till en liten uppsättning datorer eller aggregering belastningen på tjänster, använda [programgrupper](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png