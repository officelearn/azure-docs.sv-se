---
title: Dataentiteter
description: En översikt över dataentiteter.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a382f9b3ce08bba266311c2cc1d5f868f1bc3143
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934935"
---
# <a name="data-entities"></a>Dataentiteter

Den här artikeln definierar och ger en översikt över dataentiteter. Den innehåller information om funktionerna i dataentiteter, de scenarier som de stöder och de kategorier som används för dem och metoder för att skapa dem.

## <a name="overview"></a>Översikt

En dataenhet är en abstraktion av fysiska tillämpningen av databastabeller. Till exempel data för varje kund kan lagras i en kundtabell i normaliserade tabeller, och sedan resten kan spridas till en liten uppsättning relaterade tabeller. I det här fallet visas data entitet för kund-konceptet som en avnormaliserat vy, där varje rad innehåller alla data från kundtabellen och dess relaterade tabeller. En dataenhet kapslar in ett koncept för företag till ett format som underlättar utveckling och integration. Den abstraherad natur en dataenhet kan förenkla programutvecklingen och anpassning. Senare, gör abstraktionen också programkod från ofrånkomligt omsättning av fysiska tabellerna mellan versioner.

Sammanfattningsvis: Datatabell Ger konceptuell abstraktion och inkapsling (avnormaliserat view) av underliggande tabellscheman som representerar viktiga begrepp och funktioner.

## <a name="capabilities"></a>Funktioner

En dataenhet har följande funktioner:

- Det ersätter avvikande och fragmenterade begreppet AXD Data Import/Export Framework (DIXF) entiteter och aggregering frågar med enkel begrepp.
- Det ger en enda stack för att avbilda affärslogik och för att aktivera scenarier, till exempel import/export, integration och programmering.
- Det blir den primära mekanismen för att exportera och importera data paket för Application Lifecycle Management (ALM) och demo datascenarier.
- Den kan visas som OData-tjänsten, och sedan används i tabular-style synkron integrationsscenarier och Microsoft Office-integreringar.

Se [Dataentiteter](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) för mer information.
