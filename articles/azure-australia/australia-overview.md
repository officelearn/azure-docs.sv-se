---
title: Vad är Azure Australien? | Microsoft Docs
description: Vägledning om hur du konfigurerar Azure i de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: Galey801
ms.service: azure-australia
ms.topic: overview
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a6bd4a8747ba4aa592914fc970acd756803dd15e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570882"
---
# <a name="what-is-azure-australia"></a>Vad är Azure Australien?

I 2014 startades Azure i Australien, med två regioner. Östra Australien (Sydney) och Australien, sydöstra (Melbourne). I april 2018 lanserades två nya Azure-regioner som finns i Canberra – Australien, centrala och Australien, centrala 2. Regionerna Australien, centrala och Australien, centrala 2 är utformade för att uppfylla behoven hos myndighets och viktig nationell infrastruktur och erbjuder specialiserad anslutning och flexibilitet så att du kan hitta dina system bredvid molnet, med nivåer av säkerhet och återhämtning som endast förväntas av hemliga, klassificerade nätverk. Azure Australien är en plattform för digital omvandling av myndighets och viktig nationell infrastruktur – och det enda verksamhets kritiska molnet som är tillgängligt i Australien och som utformats specifikt för dessa behov.

Det finns särskilda australiska myndighets krav för anslutning till, användning och drift inom [Microsoft Azure Australien](https://azure.microsoft.com/global-infrastructure/australia/) för myndighets data och system. Resurserna på den här sidan ger också allmän vägledning för alla kunder med en särskilt fokus på säker konfiguration och åtgärd.

På sidan Australien i [Microsoft Service Trust-portalen](https://aka.ms/au-irap) hittar du aktuell information om IRAP-utvärderingar, certifiering och Cloud Services inkludering av Azure, Australien information Security (CCSL). På Australien-sidan hittar du också andra Microsoft-råd som är speciella för myndigheter och operatörer för kritiska infrastrukturer.

## <a name="principles-for-securing-customer-data-in-azure-australia"></a>Principer för att skydda kund information i Azure Australien

Azure Australien tillhandahåller en mängd funktioner och tjänster som du kan använda för att bygga moln lösningar för att möta dina reglerade/kontrollerade data behov. En kompatibel kund lösning är inget mer än den effektivaste implementeringen av Azures funktioner i den färdiga Azure-funktionen, tillsammans med en heltäckande data säkerhets praxis.

När du är värd för en lösning i Azure Australien hanterar Microsoft många av dessa krav på moln infrastruktur nivå.

I följande diagram visas Azure försvar-djupgående-modellen. Microsoft tillhandahåller till exempel grundläggande DDoS för moln infrastruktur, tillsammans med kund funktioner som säkerhets apparater eller Premium DDoS-tjänster för kundspecifika program behov.

![alternativ text](media/defenceindepth.png)

De här artiklarna beskriver grundläggande principer för att skydda dina tjänster och program, med vägledning och bästa praxis för hur du tillämpar dessa principer. Med andra ord bör du använda Azure Australien för att uppfylla de skyldigheter och ansvars områden som krävs för en lösning som hanterar statlig känslig och klassificerad information.

Det finns två kategorier av dokumentation för de australiska myndigheter som migrerar till Azure.

## <a name="security-in-azure-australia"></a>Säkerhet i Azure Australien

Identitets-, rollbaserad åtkomst kontroll, data skydd via kryptering och rättighets hantering samt effektiv övervakning och konfigurations kontroll är viktiga element som du behöver implementera. I det här avsnittet finns en serie artiklar som förklarar de inbyggda funktionerna i Azure och hur de relaterar till ISM och ASD Essentials 8.

De här artiklarna kan nås via menyn under *begrepp – > säkerhet i Azure Australien*.

## <a name="gateways-in-azure-australia"></a>Gateways i Azure Australien

Ett annat viktigt steg för myndighets myndigheter är att upprätta funktioner för perimeter-säkerhet. Dessa funktioner kallas säkra Internet-gatewayer (SIG) och när du använder Azure är det ditt ansvar att se till att dessa skydd är på plats. Microsoft använder inte någon SIG, genom att kombinera våra nätverk för Edge-nätverk som skyddar alla kunder och vissa tjänster som distribueras i din Azure-miljö kan du dock använda motsvarande funktion.

De här artiklarna kan nås via menyn under *begrepp – > gateways i Azure Australien*.

## <a name="next-steps"></a>Nästa steg

* Om ditt huvudsakliga fokus skyddar dina data i Azure börjar du med [data säkerhet](secure-your-data.md)
* Om ditt huvudsakliga fokus bygger en gateway i Azure börjar du med [Gateway-granskning, loggning och synlighet](gateway-log-audit-visibility.md).
