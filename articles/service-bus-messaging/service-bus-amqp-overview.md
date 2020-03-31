---
title: Översikt över AMQP 1.0 i Azure Service Bus
description: Lär dig mer om hur Azure Service Bus stöder ADVANCED Message Queuing Protocol (AMQP), ett öppet standardprotokoll.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759320"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1.0-stöd i Service Bus
Både molntjänsten Azure Service Bus och den lokala [servicebussen för Windows Server (servicebuss 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) stöder AMQP (Advanced Message Queueing Protocol) 1.0. MED AMQP kan du skapa hybridprogram som inte är plattformarna och hybridprogram med ett öppet standardprotokoll. Du kan skapa program med hjälp av komponenter som är byggda med olika språk och ramverk och som körs på olika operativsystem. Alla dessa komponenter kan ansluta till Service Bus och sömlöst utbyta strukturerade affärsmeddelanden effektivt och med full återgivning.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Inledning: Vad är AMQP 1.0 och varför är det viktigt?
Traditionellt har meddelandeorienterade mellanprogram produkter använt egna protokoll för kommunikation mellan klientprogram och mäklare. Det innebär att när du har valt en viss leverantörs meddelandemäklare måste du använda leverantörens bibliotek för att ansluta dina klientprogram till den mäklaren. Detta resulterar i ett visst beroende av den leverantören, eftersom portering av ett program till en annan produkt kräver kodändringar i alla anslutna program. 

Dessutom är det svårt att ansluta meddelandemäklare från olika leverantörer. Detta kräver vanligtvis överbryggning på programnivå för att flytta meddelanden från ett system till ett annat och översätta mellan deras egna meddelandeformat. Detta är ett vanligt krav. Till exempel när du måste tillhandahålla ett nytt enhetligt gränssnitt till äldre olika system, eller integrera IT-system efter en sammanslagning.

Mjukvaruindustrin är en snabbrörlig verksamhet; nya programmeringsspråk och tillämpningsramar införs i en ibland förvirrande takt. På samma sätt utvecklas kraven i IT-system över tid och utvecklare vill dra nytta av de senaste plattformsfunktionerna. Men ibland stöder inte den valda meddelandeleverantören dessa plattformar. Eftersom meddelandeprotokoll är proprietära är det inte möjligt för andra att tillhandahålla bibliotek för dessa nya plattformar. Därför måste du använda metoder som att skapa gateways eller bryggor som gör att du kan fortsätta att använda meddelandeprodukten.

Utvecklingen av AMQP (Advanced Message Queuing Protocol) 1.0 motiverades av dessa problem. Det har sitt ursprung på JP Morgan Chase, som, liksom de flesta finansiella tjänster företag, är tunga användare av meddelande-orienterade mellanprogram. Målet var enkelt: att skapa ett meddelandeprotokoll i öppen standard som gjorde det möjligt att skapa meddelandebaserade program med hjälp av komponenter som skapats med olika språk, ramverk och operativsystem, allt med hjälp av förstklassiga komponenter från en rad olika Leverantörer.

## <a name="amqp-10-technical-features"></a>AMQP 1.0 tekniska funktioner
AMQP 1.0 är ett effektivt, tillförlitligt meddelandeprotokoll på trådnivå som du kan använda för att skapa robusta, plattformsoberoende meddelandeprogram. Protokollet har ett enkelt mål: att definiera mekaniken i en säker, tillförlitlig och effektiv överföring av meddelanden mellan två parter. Själva meddelandena kodas med hjälp av en bärbar datarepresentation som gör det möjligt för heterogena avsändare och mottagare att utbyta strukturerade affärsmeddelanden med full återgivning. Följande är en sammanfattning av de viktigaste funktionerna:

* **Effektiv**: AMQP 1.0 är ett anslutningsorienterat protokoll som använder en binär kodning för protokollinstruktionerna och de affärsmeddelanden som överförs över det. Den innehåller sofistikerade flödeskontrollsystem för att maximera utnyttjandet av nätverket och de anslutna komponenterna. Med detta sagt var protokollet utformat för att skapa en balans mellan effektivitet, flexibilitet och driftskompatibilitet.
* **Tillförlitlig:** AMQP 1.0-protokollet gör att meddelanden kan utbytas med en rad tillförlitlighetsgarantier, från brand-och-glöm till tillförlitlig, exakt en gång erkänd leverans.
* **Flexibel:** AMQP 1.0 är ett flexibelt protokoll som kan användas för att stödja olika topologier. Samma protokoll kan användas för kommunikation mellan klient och klient, klient-till-mäklare och mäklare.
* **Broker-modell oberoende:** AMQP 1.0 specifikationen gör inga krav på meddelandemodell som används av en mäklare. Detta innebär att det är möjligt att enkelt lägga till AMQP 1.0-stöd till befintliga meddelandemäklare.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 är en standard (med ett stort "S")
AMQP 1.0 är en internationell standard, godkänd av ISO och IEC som ISO/IEC 19464:2014.

AMQP 1.0 har varit under utveckling sedan 2008 av en kärngrupp med mer än 20 företag, både teknikleverantörer och slutanvändarföretag. Under den tiden har användarföretag bidragit med sina verkliga affärskrav och teknikleverantörerna har utvecklat protokollet för att uppfylla dessa krav. Under hela processen har leverantörer deltagit i workshops där de samarbetat för att validera interoperabiliteten mellan deras implementeringar.

I oktober 2011 övergick utvecklingsarbetet till en teknisk kommitté inom Organisationen för främjande av strukturerade informationsstandarder (OASIS) och OASIS AMQP 1.0-standarden släpptes i oktober 2012. Följande företag deltog i den tekniska kommittén under utvecklingen av standarden:

* **Teknikleverantörer**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Användarföretag**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Några av de allmänt citerade fördelarna med öppna standarder är:

* Mindre chans att leverantören lås-in
* Samverkan
* Bred tillgång till bibliotek och verktyg
* Skydd mot inkurans
* Tillgång till kunnig personal
* Lägre och hanterbar risk

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 och Service Bus
AMQP 1.0-stöd i Azure Service Bus innebär att du nu kan utnyttja servicebusskök- och publicerings-/prenumerationsmäklara meddelandefunktioner från en rad plattformar med hjälp av ett effektivt binärt protokoll. Dessutom kan du skapa program som består av komponenter byggda med en blandning av språk, ramverk och operativsystem.

Följande bild illustrerar en exempeldistribution där Java-klienter som körs på Linux, skrivna med hjälp av vanliga JAVA Message Service (JMS) API och .NET-klienter som körs på Windows, utbyta meddelanden via Service Bus med AMQP 1.0.

![][0]

**Bild 1: Exempel på distributionsscenario som visar plattformsoberoende meddelanden med Service Bus och AMQP 1.0**

För närvarande är följande klientbibliotek kända för att fungera med Service Bus:

| Språk | Bibliotek |
| --- | --- |
| Java |Apache Qpid Java Message Service (JMS) klient<br/>IIT Software SwiftMQ Java-klient |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Bild 2: Tabell över AMQP 1.0-klientbibliotek**

## <a name="summary"></a>Sammanfattning
* AMQP 1.0 är ett öppet, tillförlitligt meddelandeprotokoll som du kan använda för att skapa plattformsoberoende hybridprogram. AMQP 1.0 är en OASIS-standard.
* AMQP 1.0-stöd är nu tillgängligt i Azure Service Bus samt Service Bus för Windows Server (Service Bus 1.1). Prissättningen är densamma som för de befintliga protokollen.

## <a name="next-steps"></a>Nästa steg
Vill du lära dig mer? Besök följande länkar:

* [Använda Service Bus från .NET med AMQP]
* [Använda Service Bus från Java med AMQP]
* [Installera Apache Qpid Proton-C på en virtuell Azure Linux-dator]
* [AMQP i servicebuss för Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Använda Service Bus från .NET med AMQP]: service-bus-amqp-dotnet.md
[Använda Service Bus från Java med AMQP]: service-bus-amqp-java.md
[Installera Apache Qpid Proton-C på en virtuell Azure Linux-dator]: service-bus-amqp-apache.md
[AMQP i servicebuss för Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
