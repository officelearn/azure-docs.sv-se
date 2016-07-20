<properties
    pageTitle="Vad är händelsehubbar i Azure? | Microsoft Azure"
    description="Översikt och beskrivning av händelsehubbar i Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/12/2016"
    ms.author="sethm;nberdy"/>

# Vad är händelsehubbar i Azure?

Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Händelsehubbar fungerar som ”ytterdörren” för en händelsepipeline, och när data har samlats in i en händelsehubb kan du omvandla och lagra dessa data med hjälp av valfri leverantör av realtidsanalys eller med adaptrar för batchbearbetning/lagring. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema.

## Funktioner i händelsehubbar

Händelsehubbar är en tjänst för händelsebearbetning som ger händelse- och telemetriingång till molnet i massiv skala med kort svarstid och hög tillförlitlighet. Tjänsten är särskilt lämpad för:

- Programinstrumentering
- Bearbetning av användarupplevelser och arbetsflöden
- Scenarier i sakernas internet (IoT)

Andra viktiga funktioner i händelsehubbar är till exempel beteendespårning i mobilappar, trafikinformation från webbservergrupper, inspelning av spelhändelser i konsolspel eller telemetri som samlats in från industriella datorer eller anslutna fordon.

Till skillnad från [Service Bus-köer och -ämnen](../service-bus/service-bus-messaging-overview.md) fokuserar händelsehubbar på att tillhandahålla hantering av meddelandeströmmar i större skala. Funktionerna i händelsehubbar skiljer sig från Service Bus-ämnen. De fokuserar till exempel på ett högt genomflöde och händelsebearbetningsscenarier. Därför implementerar händelsehubbar inte en del av de meddelandefunktioner som är tillgängliga för [ämnen](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Om du behöver de funktionerna är ämnen fortfarande att föredra.

## Nästa steg

Mer utförlig information om händelsehubbar finns i följande avsnitt.

- [Översikt av händelsehubbar](event-hubs-overview.md)
- [Programmeringsguide för händelsehubbar](event-hubs-programming-guide.md)
- [Vanliga frågor om tillgänglighet och support för händelsehubbar](event-hubs-availability-and-support-faq.md)
- Kom igång med en [kurs om händelsehubbar][]
- Ett komplett [exempelprogram som använder händelsehubbar][].

[kurs om händelsehubbar]: event-hubs-csharp-ephcs-getstarted.md
[exempelprogram som använder händelsehubbar]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097


<!--HONumber=Jun16_HO2-->


