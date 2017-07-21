---
title: "Jag använder Mobile Services. Vad bidrar Apptjänst med?"
description: "Läs om vilken nytta du har av Apptjänst i dina befintliga Mobile Services-projekt."
services: app-service\mobile
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: e23a4f182e6bb0ba53927aa6195967c2464e4233
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017

---
# <a name="getting-started"> </a>Jag använder Mobile Services. Vad bidrar Apptjänst med?
## <a name="overview"></a>Översikt
Din nuvarande mobiltjänst Mobile Services är säker och kommer att kunna användas även i fortsättningen. Det finns emellertid ett antal fördelar som *Azure Apptjänst*-plattformen ger din mobilapp som i dagsläget inte är tillgängliga i Mobile Services:

* enklare och mer kostnadseffektivt utbud av appar för både webb- och mobilklienter
* nya värdfunktioner såsom WebJobs, anpassade CName-poster, bättre övervakning
* nyckelfärdig integrering med Traffic Manager
* anslutning till lokala resurser och VPN-nätverk via VNet som komplement till hybridanslutningar
* övervakning, aviseringar och felsökning av appar med NewRelic eller AppInsights
* större utbud av underliggande beräkningsresurser och prissättning
* inbyggd automatisk skalning, belastningsutjämning och prestandaövervakning
* inbyggd mellanlagring, säkerhetskopiering, återställning och möjlighet till testning i produktionsmiljö

## <a name="new-hosting-features"></a>Nya värdfunktioner
I *Azure Apptjänst* körs serverdelskoden för *mobilappar* i samma behållare som för webbappen och API-appen. Det innebär att du kan använda alla funktionerna i den här behållaren, inklusive några som för närvarande inte finns i Mobile Services:

* Du kan lägga till serverdelslogik som körs hela tiden via WebJobs.
* Du kan säkerställa att serverdelskoden alltid körs.
* Du kan använda anpassade CNames-poster och sätta egna stabila namn på dina mobila serverdelsslutpunkter.
* Du kan geoskala appen med Traffic Manager.
* Du kan ha med vilka bibliotek och paket du vill.
* (För .NET) Du kan använda alla funktioner i ASP.NET, även MVC.
* (För Node.js) Du kan använda alla bibliotek med endast JavaScript i Node-ekosystemet, även vanliga MVC-bibliotek.

## <a name="access-on-premises-data-using-vnet"></a>Möjlighet att nå lokala data via VNet
Med Mobile Services kan du redan idag använda hybridanslutningar för att komma åt lokala resurser. Det finns dock tillfällen då det är bäst med VPN-lösning. Med *Azure Apptjänst* kan du använda Azure VNet för serverdelskoden till mobilappar.

## <a name="use-your-favorite-backend-language"></a>Använd det serverdelsspråk som du gillar bäst
*Azure Apptjänst* har bredare och bättre stöd för ASP.NET- och Node.js-plattformarna, inklusive tillgång till de senaste körningarna.

## <a name="set-up-automatic-scale"></a>Automatisk skalning
Med Mobile Services kördes alla instanser av serverdelskoden på små virtuella datorer. Med *Azure Apptjänst* kan du välja storlek på de virtuella datorerna bland ett mycket större antal alternativ. Du kan även snabbt skala upp eller ut för att hantera inkommande kundbelastning utifrån olika prestandamått.

## <a name="be-in-the-know"></a>Håll koll
Du kan åtgärda problem i realtid genom övervakning och aviseringar så att du och dina kolleger alltid har koll på vad som händer. Du kan integrera avancerad appanalys och övervakningsfunktioner från New Relic och AppInsights och få ännu bättre inblick i vad som händer med din mobilapp. Med *Azure Apptjänst* kan du nu ställa in aviseringar på basis av flera olika prestandamått, antingen genom att programmera eller via Azure Portal.

## <a name="keep-your-assets-safe"></a>Dina tillgångar är trygga
Serverdelen och databasen kan säkerhetskopieras automatiskt. Kod och data är skyddade vid katastrofer och kan lätt återställas så att du kan bedriva din verksamhet utan att behöva oroa dig.

## <a name="ready-stage-go"></a>Klara, mellanlagra, kör!
Med *Azure Apptjänst* kan du nu skapa flera olika privata testnings- och mellanlagringsmiljöer för dina mobilappar. I de här miljöerna kan du testa apparna innan du distribuerar ut dem. Du kan växla till produktionsmiljö utan avbrott. Webbappar är förinstallerade, vilket ger bästa möjliga kundupplevelse.

Du kan komma igång och utnyttja alla fördelarna med *Apptjänst* för din befintliga mobiltjänst genom att gå igenom den här [kursen](app-service-mobile-migrating-from-mobile-services.md).

