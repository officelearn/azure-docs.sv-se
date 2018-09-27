---
title: Översikt över AMQP 1.0 i Azure Service Bus | Microsoft Docs
description: Lär dig mer om hur du använder AMQP Advanced Message Queuing Protocol () 1.0 i Azure.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/26/2018
ms.author: spelluru
ms.openlocfilehash: b43cdfa0b5f9e5bf6a94f4f59034e07f59ddb163
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393340"
---
# <a name="amqp-10-support-in-service-bus"></a>Stöd för AMQP 1.0 i Service Bus
Både Azure Service Bus-Molntjänsten och lokala [Service Bus för Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) stöd för AMQP Advanced Message Queueing Protocol () 1.0. AMQP gör det möjligt för dig att skapa plattformsöverskridande hybridprogram med ett öppet standardprotokoll. Du kan skapa program med hjälp av komponenter som är byggda med olika språk och ramverk och som körs på olika operativsystem. Alla dessa komponenter kan ansluta till Service Bus och sömlöst utbyter strukturerade meddelanden effektivt och fullständig återgivning.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduktion: Vad är AMQP 1.0 och varför är det viktigt?
Meddelande-orienterade mellanprodukter har tidigare använt egna protokoll för kommunikation mellan program och asynkrona meddelandeköer. Det innebär att när du har valt en viss leverantör meddelanden broker, måste du använda den leverantören bibliotek för att ansluta dina klientprogram till den asynkrona meddelandekön. Detta resulterar i en grad av beroende av den leverantören, eftersom porta ett program till en annan produkt kräver ändringar i koden i alla anslutna program. 

Ansluta asynkrona meddelandeköer från olika leverantörer har dessutom svårt. Detta kräver normalt programnivå bryggning flytta meddelanden från ett system till en annan och för att omvandla sina egna meddelandeformat. Det här är ett vanligt krav; till exempel när du måste ange ett nytt enhetligt gränssnitt till äldre olika system, eller integrera IT-system som följer en fusion.

Industrin är en snabbrörliga verksamhet. ny programmeringsspråk och programramverk introduceras i ibland bewildering takt. På samma sätt kan kraven i IT-system utvecklas över tid och utvecklare som vill dra nytta av de senaste funktionerna för plattformen. Men stöder ibland den valda meddelanden leverantören inte dessa plattformar. Eftersom meddelandeprotokoll är tillverkarspecifika, går det inte för andra att tillhandahålla bibliotek för dessa nya plattformar. Därför måste du använda metoderna, till exempel att skapa gatewayer eller bryggor så att du kan fortsätta att använda meddelanden produkten.

Utveckling av Message Queuing Protocol AMQP (Advanced) 1.0 har motiveras av de här problemen. Den kommer från JP Morgan Chase som är som mest ekonomiska finanstjänstföretag tung användare av meddelande-orienterade mellanprogram. Målet har enkla: att skapa en öppen standard trådnivå som gjorde det möjligt att skapa meddelande-baserade program med hjälp av komponenter som skapats med olika språk, ramverk och operativsystem, alla använda bästa möjliga komponenter från en mängd leverantörer.

## <a name="amqp-10-technical-features"></a>Tekniska funktioner för AMQP 1.0
AMQP 1.0 är ett effektivt, pålitligt meddelandeprotokoll på trådnivå som du kan använda för att skapa robusta och plattformsöverskridande meddelandeprogram. Protokollet som har ett enkelt mål: definiera säkerhetsnivån säker, tillförlitlig och effektiv överföring av meddelanden mellan två parter. Själva meddelandena kodas med hjälp av en bärbar datarepresentation som gör det möjligt för heterogena sändare och mottagare att utbyta strukturerade meddelanden med fullständig exakthet. Här följer en sammanfattning av de viktigaste funktionerna:

* **Effektiv**: AMQP 1.0 är en anslutning inriktad protokoll som används som en binär kodning protocol-instruktioner och business-meddelanden överförs den. Avtalet omfattar också avancerade flödeskontroll scheman för att maximera användningen av nätverket och anslutna komponenter. Det finns dock protokollet som har utformats för att göra en avvägning mellan effektivitet, flexibilitet och samverkan.
* **Tillförlitlig**: The AMQP 1.0-protokollet gör att meddelanden kan skickas med en mängd tillförlitlighet garantier från fire-and-forget till pålitlig exakt – en gång bekräftas leverans.
* **Flexibla**: AMQP 1.0 är ett flexibelt protokoll som kan användas för att stödja olika topologier. Samma protokoll kan användas för klient-till-klient och klient-till-broker broker-broker-kommunikation.
* **Broker-modell oberoende**: The AMQP 1.0-specifikation inte göra några krav på meddelanden modellen som en koordinator. Det innebär att det är möjligt att enkelt lägga till stöd för AMQP 1.0 i befintliga asynkrona meddelandeköer.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 är en Standard (med en versal ”)
AMQP 1.0 är en internationell standard godkänd av ISO och IEC som ISO/IEC 19464:2014.

AMQP 1.0 har varit i utveckling sedan 2008 av en core-grupp med fler än 20 företag, både teknik leverantörer och slutanvändarens företag. Under den tiden kan användaren företag har bidragit deras verkliga affärskrav och teknikleverantörer som har utvecklats av protokollet för att uppfylla dessa krav. Under hela processen, har leverantörer deltagit i workshops där de samarbetat för att verifiera samverkan mellan sina implementeringar.

I oktober 2011 släpptes utvecklingsarbete som gått över till en tekniska kommittén inom organisationen för den karriärmöjligheter av Structured Information Standards (OASIS) och Standard OASIS AMQP 1.0 i oktober 2012. Följande företag har deltagit i den tekniska kommittén under utvecklingen av standarden:

* **Teknikleverantörer**: Axway programvara, Huawei Technologies, IIT programvara, INETCO system, Kaazing, Microsoft, Mitre Corporation, Primeton tekniker, förloppet programvara, Red Hat, SITA, Software AG, Solace system, VMware, WSO2, Zenika.
* **Användaren företag**: Bank America, kredit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Några av de vanligaste åberopas fördelarna med öppna standarder är:

* Mindre risk för lås in leverantör
* Samverkan
* Bred tillgängligheten för bibliotek och verktyg
* Skydd mot föråldring
* Tillgängligheten för kunnig personal
* Lägre och hanterbar risk

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 och Service Bus
Stöd för AMQP 1.0 i Azure Service Bus innebär att du kan nu använda Service Bus queuing och publicera/prenumerera på asynkrona meddelandefunktioner från flera olika plattformar med en effektiv binär-protokollet. Dessutom kan du skapa program som består av komponenter som skapats med en blandning av språk, ramverk och operativsystem.

Följande bild visar ett exempel på distribution där Java-klienter som körs på Linux, skrivna med hjälp av standard Java Message Service JMS () API och .NET-klienter som körs på Windows, utbyta meddelanden via Service Bus via AMQP 1.0.

![][0]

**Bild 1: Distribution exempelscenario som visar meddelanden med hjälp av Service Bus och AMQP 1.0 på flera plattformar**

Just nu är följande klientbibliotek kända för att arbeta med Service Bus:

| Språk | Bibliotek |
| --- | --- |
| Java |Apache Qpid Java Message Service JMS ()-klient<br/>IIT programvara SwiftMQ Java-klient |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton – PHP |
| Python |Apache Qpid Proton – Python |
| C# |AMQP .net Lite |

**Bild 2: Tabell med AMQP 1.0-klientbibliotek**

## <a name="summary"></a>Sammanfattning
* AMQP 1.0 är ett öppet, tillförlitlig trådnivå som du kan använda för att skapa plattformsöverskridande hybridprogram. AMQP 1.0 är en OASIS-standard.
* Stöd för AMQP 1.0 är nu tillgänglig i Azure Service Bus, samt Service Bus för Windows Server (Service Bus 1.1). Priserna är desamma som för de befintliga protokoll.

## <a name="next-steps"></a>Nästa steg
Är du redo att lära dig mer? Besök följande länkar:

* [Använda Service Bus från .NET med AMQP]
* [Använda Service Bus från Java med AMQP]
* [Installera Apache Qpid Proton-C på en virtuell Azure Linux-dator]
* [AMQP i Service Bus för Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Använda Service Bus från .NET med AMQP]: service-bus-amqp-dotnet.md
[Använda Service Bus från Java med AMQP]: service-bus-amqp-java.md
[Installera Apache Qpid Proton-C på en virtuell Azure Linux-dator]: service-bus-amqp-apache.md
[AMQP i Service Bus för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
