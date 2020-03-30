---
title: Resurshanteraren för klusterresurs för servicevävnad – tillhörighet
description: Översikt över tjänsttillhörighet för Azure Service Fabric-tjänster och vägledning om konfiguration av tjänsttillhörighet.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551751"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurera och använda tjänsttillhörighet i Service Fabric
Affinitet är en kontroll som tillhandahålls främst för att underlätta övergången av större monolitiska program till molnet och mikrotjänster världen. Det används också som en optimering för att förbättra prestanda för tjänster, även om detta kan ha biverkningar.

Anta att du tar med en större app, eller en som bara inte har utformats med mikrotjänster i åtanke, till Service Fabric (eller någon distribuerad miljö). Denna typ av övergång är vanlig. Du börjar med att lyfta in hela appen i miljön, paketera den och se till att den fungerar smidigt. Sedan börjar du dela upp det i olika mindre tjänster som alla pratar med varandra.

Så småningom kan det hända att programmet har vissa problem. Frågorna faller vanligtvis i en av dessa kategorier:

1. Någon komponent X i den monolitiska appen hade ett odokumenterat beroende av komponent Y, och du har just förvandlat dessa komponenter till separata tjänster. Eftersom dessa tjänster nu körs på olika noder i klustret är de trasiga.
2. Dessa komponenter kommunicerar via (lokala namngivna rör | delat minne | filer på disk) och de måste verkligen kunna skriva till en delad lokal resurs av prestandaskäl just nu. Det hårda beroendet tas bort senare, kanske.
3. Allt är bra, men det visar sig att dessa två komponenter är faktiskt pratsam / prestandakänslig. När de flyttade dem till separata tjänster övergripande programprestanda tanked eller latens ökade. Som ett resultat av detta uppfyller den övergripande ansökan inte förväntningarna.

I dessa fall vill vi inte förlora vårt refactoring arbete, och vill inte gå tillbaka till monoliten. Det sista villkoret kan även vara önskvärt som en vanlig optimering. Men tills vi kan omforma komponenterna för att fungera naturligt som tjänster (eller tills vi kan lösa prestandaförväntningarna på något annat sätt) kommer vi att behöva lite känsla för ort.

Vad bör jag göra? Du kan prova att slå på affinitet.

## <a name="how-to-configure-affinity"></a>Konfigurera tillhörighet
Om du vill ställa in tillhörighet definierar du en intressegruppsrelation mellan två olika tjänster. Du kan tänka dig tillhörighet som att "peka" en tjänst mot en annan och säga "Den här tjänsten kan bara köras där tjänsten körs.", Ibland refererar vi till tillhörighet som en överordnad/underordnad relation (där du pekar barnet mot den överordnade). Tillhörighet säkerställer att replikerna eller instanserna av en tjänst placeras på samma noder som för en annan tjänst.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> En underordnad tjänst kan bara delta i en enda tillhörighetsrelation. Om du ville att barnet skulle vara affiniterat till två överordnade tjänster på en gång har du ett par alternativ:
> - Återför relationerna (har parentService1 och parentService2-punkt vid den aktuella underordnade tjänsten), eller
> - Utse en av föräldrarna som ett nav efter konvention och ha alla tjänster punkt på den tjänsten. 
>
> Det resulterande placeringsbeteendet i klustret bör vara detsamma.
>

## <a name="different-affinity-options"></a>Olika tillhörighetsalternativ
Affinitet representeras via ett av flera korrelationsscheman och har två olika lägen. Det vanligaste affinitetsläget är vad vi kallar NonAlignedAffinity. I Icke-allignedaffinitet placeras replikerna eller instanserna av de olika tjänsterna på samma noder. Det andra läget är AlignedAffinity. Justerad tillhörighet är endast användbar med tillståndskänsliga tjänster. Konfigurera två tillståndskänsliga tjänster för att ha justerad tillhörighet säkerställer att primärvalen för dessa tjänster placeras på samma noder som varandra. Det medför också att varje par sekundärer för dessa tjänster placeras på samma noder. Det är också möjligt (men mindre vanligt) att konfigurera Icke-allvärdig tillhörighet för tillståndskänsliga tjänster. För Icke-allvärdigtillhörighet skulle de olika replikerna för de två tillståndskänsliga tjänsterna köras på samma noder, men deras primärval kan hamna på olika noder.

<center>

![Affinitetslägen och deras effekter][Image1]
</center>

### <a name="best-effort-desired-state"></a>Bästa insats önskat tillstånd
En affinitetsrelation är bästa insats. Det ger inte samma garantier för samlokalisering eller tillförlitlighet som körs i samma körbara process gör. Tjänsterna i en tillhörighetsrelation är fundamentalt olika entiteter som kan misslyckas och flyttas oberoende av sig. En affinitet relation kan också bryta, även om dessa raster är tillfälliga. Kapacitetsbegränsningar kan till exempel innebära att endast vissa tjänstobjekt i tillhörighetsrelationen får plats på en viss nod. I dessa fall även om det finns en affinitetsrelation på plats, kan den inte verkställas på grund av de andra begränsningarna. Om det är möjligt att göra det korrigeras överträdelsen automatiskt senare.

### <a name="chains-vs-stars"></a>Kedjor vs stjärnor
I dag kan klusterresurshanteraren inte modellera kedjor av tillhörighetsrelationer. Vad detta innebär är att en tjänst som är ett underordnat i en tillhörighetsrelation inte kan vara överordnad i en annan tillhörighetsrelation. Om du vill modellera denna typ av relation, måste du effektivt modellera det som en stjärna, snarare än en kedja. Om du vill flytta från en kedja till en stjärna skulle det nedersta barnet i stället vara förälder till det första barnets förälder. Beroende på arrangemanget av dina tjänster, kan du behöva göra detta flera gånger. Om det inte finns någon naturlig överordnad tjänst kan du behöva skapa en som fungerar som platshållare. Beroende på dina behov kan du också titta på [programgrupper](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Kedjor vs Stjärnor i samband med affinitet relationer][Image2]
</center>

En annan sak att notera om affinitet relationer idag är att de är riktnings som standard. Det innebär att tillhörighetsregeln endast upprätthåller att den underordnade som placeras med den överordnade. Det säkerställer inte att föräldern finns med barnet. Om det föreligger en överträdelse av tillhörighet och korrigerar överträdelsen av någon anledning är det därför inte möjligt att flytta barnet till den överordnade noden, då - även om det skulle ha korrigerat överträdelsen att flytta den överordnade till barnets nod - barnets nod. Om du ställer in config [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) till true skulle riktningen tas bort. Det är också viktigt att notera att affinitetsrelationen inte kan vara perfekt eller omedelbart verkställas eftersom olika tjänster har med olika livscykler och kan misslyckas och röra sig oberoende av dem. Anta till exempel att föräldern plötsligt växlar över till en annan nod eftersom den kraschade. Klusterresurshanteraren och redundanshanteraren hanterar redundansen först, eftersom det är prioritet att hålla tjänsterna uppdaterade, konsekventa och tillgängliga. När redundansen är klar bryts tillhörighetsrelationen, men Klusterresurshanteraren tycker att allt är bra tills den märker att barnet inte finns med den överordnade. Dessa typer av kontroller utförs regelbundet. Mer information om hur Cluster Resource Manager utvärderar begränsningar finns i [den här artikeln](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), och den [här](service-fabric-cluster-resource-manager-balancing.md) talar mer om hur du konfigurerar kadensen som dessa begränsningar utvärderas på.   


### <a name="partitioning-support"></a>Stöd för partitionering
Det sista att märka om tillhörighet är att tillhörighetsrelationer inte stöds där den överordnade partitioneras. Partitionerade överordnade tjänster kan stödjas så småningom, men idag är det inte tillåtet.

## <a name="next-steps"></a>Nästa steg
- Mer information om hur du konfigurerar tjänster [finns i Läs mer om att konfigurera tjänster](service-fabric-cluster-resource-manager-configure-services.md)
- Om du vill begränsa tjänster till en liten uppsättning datorer eller samla in belastningen av tjänster använder du [Programgrupper](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png