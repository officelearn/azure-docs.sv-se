---
title: Översikt över AMQP 1.0 i Azure Service Bus | Microsoft Docs
description: Lär dig mer om hur du använder den avancerade Message Queuing-protokollet (AMQP) 1.0 i Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6d2dffd22ecfc0aaf6e338567d5cf107a2c07383
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926605"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1.0-stöd i Service Bus
Både Azure Service Bus tjänst i molnet och lokala [Service Bus för Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) stöder den avancerade Message Queueing Protocol (AMQP) 1.0. AMQP låter dig skapa plattformsoberoende, hybridprogram med ett öppet standardprotokoll. Du kan skapa program med hjälp av komponenter som är byggda med olika språk och ramverk och som körs på olika operativsystem. Alla dessa komponenter kan ansluta till Service Bus och sömlöst utbyter strukturerade meddelanden effektivt och fullständig återgivning.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introduktion: Vad är AMQP 1.0 och varför är det viktigt?
Meddelande-inriktade mellanprodukter har tidigare använt egna protokoll för kommunikation mellan program och mäklare. Det innebär att när du har valt en viss leverantör meddelanden broker, måste du använda den leverantör bibliotek för att ansluta ditt klientprogram att Service broker. Detta resulterar i en grad av beroende av tillverkaren, eftersom portera ett program till en annan produkt kräver kodändringar i alla anslutna program. 

Dessutom är meddelanden mäklare från olika leverantörer ansluta svårt. Detta kräver normalt på programnivå bryggning flytta meddelanden från en dator till en annan och översätta mellan sina egna meddelandeformat. Det här är ett vanligt krav. exempelvis när du måste ange ett nytt gränssnitt för enhetlig till äldre olika system eller integrera IT-system efter en fusion.

Industrin är ett fast flytta företag; nya programmeringsspråk och programramverk introduceras i en ibland bewildering takt. På liknande sätt kraven i IT-system utvecklas över tid och utvecklare som vill dra nytta av de senaste funktionerna för plattformen. Men stöder ibland valda meddelanden leverantören inte dessa plattformar. Eftersom meddelanden protokollen är skyddad, går det inte att andra bibliotek att dessa nya plattformar. Därför måste du använda metoderna, till exempel skapa gateways och bryggor som gör att du kan fortsätta använda produkten meddelanden.

Utvecklingen av de avancerade Message Queuing Protocol (AMQP) 1.0 har motiveras av dessa problem. Den kommer från JP Morgan Chase som är tungt användare av meddelande-inriktade mellanprogram som mest ekonomiska tjänster företag. Målet är enkel: Så här skapar du en öppen standard meddelanden protokoll som möjligt att skapa message-baserade program med hjälp av komponenter som skapats med olika språk och ramverk operativsystem, alla med bäst för RAS-komponenter från en mängd leverantörer.

## <a name="amqp-10-technical-features"></a>Tekniska AMQP 1.0-funktioner
AMQP 1.0 är ett effektivt, tillförlitlig och överföring nivå meddelanden protokoll som du kan använda för att bygga robusta, plattformsoberoende, e-postprogram. Protokollet har ett enkelt mål: definiera säkerhetsnivån säker, tillförlitlig och effektiv överföring av meddelanden mellan två parter. Själva meddelandena kodas med hjälp av en bärbar data representation som gör det möjligt för heterogena avsändare och mottagare att utbyta strukturerade meddelanden vid fullständig återgivning. Följande är en sammanfattning av de viktigaste funktionerna:

* **Effektiv**: AMQP 1.0 är en anslutningsorienterad protokoll som använder en binär kodning protokollet anvisningar och business-meddelanden skickas via den. Den innehåller avancerade flödeskontroll scheman för att maximera användningen av nätverket och de anslutna komponenterna. Namn, protokollet har utformats för att göra en avvägning mellan effektivitet, flexibilitet och samverkan.
* **Tillförlitliga**: den AMQP 1.0-protokollet gör att meddelanden kan skickas med ett intervall av tillförlitlighet garantier från fire-och-glömmer att tillförlitligt exakt-bekräftade leverans en gång.
* **Flexibla**: AMQP 1.0 är ett flexibelt protokoll som kan användas för att stödja olika topologier. Samma protokoll kan användas för kommunikation med klient-till-klient och klient-till-broker broker Service broker.
* **Service Broker-modell oberoende**: den AMQP 1.0-specifikationen inte gör det några krav på meddelanden modellen som en koordinator. Det innebär att det är möjligt att enkelt lägga till stöd för AMQP 1.0 befintliga meddelanden mäklare.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 är en Standard (med en versal ')
AMQP 1.0 är en internationell standard godkänd av ISO och IEC som ISO/IEC 19464:2014.

AMQP 1.0 har varit under utveckling sedan 2008 genom en core företagsgrupp fler än 20, både teknik leverantörer och slutanvändarens företag. Under den tiden kan företag som användaren har bidragit sina verkliga affärsbehov och teknikleverantörer har utvecklats protokollet för att uppfylla dessa krav. Under hela processen, har leverantörer deltagit i arbetsgrupper som de samarbetat för att validera samverkan mellan sina implementeringar.

I oktober 2011 gavs utvecklingsarbete övergått till tekniska kommittén inom organisationen för den framflyttning för Structured Information Standards (OASIS) och OASIS AMQP 1.0 Standard ut i oktober 2012. Följande företag har deltagit i den tekniska kommittén under utvecklingen av standarden:

* **Teknikleverantörer**: Axway programvara, Huawei tekniker, IIT programvara, INETCO system, Kaazing, Microsoft, Mitre Corporation, Primeton tekniker, förlopp programvara, Red Hat, SITA, programvara AG, Solace system, VMware, WSO2, Zenika.
* **Användaren företag**: Bank Sydamerika, kredit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Ofta citerad fördelarna med att öppna standarder bland annat:

* Mindre risk för lås i leverantör
* Samverkan
* Bred tillgängligheten för bibliotek och verktygsuppsättning
* Skydd mot föråldring
* Tillgängligheten för kunniga personer
* Lägre och hanterbar risk

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 och Service Bus
AMQP 1.0-stöd i Azure Service Bus innebär att du nu använda Service Bus queuing och publicera/prenumerera asynkrona meddelandetjänsten funktioner mellan olika plattformar med en effektiv binär-protokollet. Dessutom kan du skapa program består av komponenter som skapats med en blandning av språk och ramverk operativsystem.

Följande bild illustrerar ett exempel på distribution där Java-klienter som körs på Linux, skrivs med standard Java meddelandet Service (JMS) API och .NET-klienter som körs på Windows, utbyter meddelanden via Service Bus använder AMQP 1.0.

![][0]

**Bild 1: Exempel distributionsscenariot visar meddelanden med hjälp av Service Bus och AMQP 1.0 på flera plattformar**

Följande klientbibliotek har visat sig fungera med Service Bus just nu:

| Språk | Bibliotek |
| --- | --- |
| Java |Apache Qpid Java meddelandet Service (JMS)-klient<br/>IIT programvara SwiftMQ Java-klient |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .net Lite |

**Bild 2: Tabell med AMQP 1.0-klientbibliotek**

## <a name="summary"></a>Sammanfattning
* AMQP 1.0 är en öppen, tillförlitlig meddelanden protokoll som du kan använda för att skapa plattformsoberoende, hybridprogram. AMQP 1.0 är en OASIS standard.
* AMQP 1.0-stöd är nu tillgängligt i Azure Service Bus samt Service Bus för Windows Server (Service Bus 1.1). Priser är desamma som de befintliga protokoll.

## <a name="next-steps"></a>Nästa steg
Är du redo att lära dig mer? Besök följande länkar:

* [Använda Service Bus från .NET med AMQP]
* [Med AMQP Service Bus från Java]
* [Installera Apache Qpid Proton-C på en Azure Linux-dator]
* [AMQP i Service Bus för Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Använda Service Bus från .NET med AMQP]: service-bus-amqp-dotnet.md
[Med AMQP Service Bus från Java]: service-bus-amqp-java.md
[Installera Apache Qpid Proton-C på en Azure Linux-dator]: service-bus-amqp-apache.md
[AMQP i Service Bus för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
