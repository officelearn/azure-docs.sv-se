---
title: Förberedelse av Dynamics 365-lösning | Microsoft Docs
description: Framewrok för packaing, installation och unstalling komponenter
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c1e9c831681867e6a6238159599af39cbab10b7e
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811781"
---
# <a name="dynamics-365-solution-preparation"></a>Förberedelse av Dynamics 365-lösning

Dynamics 365 solutioning system är ett ramverk för paketering, installation och avinstallation komponenter som innehåller specifika funktioner. Lösningar som används av ISV: er och andra Microsoft Dynamics 365-partner för att distribuera tillägg som de skapar.

Om du är en befintlig Dynamics 365 (xRM) ISV kan du förmodligen redan har skapat en hanterad lösning och har en solution.zip-fil. Kontrollera att ”visningsnamn” och ”beskrivning” fält omfattar vad du vill att kunder ska se i din lösning. Dessa visas i Administrationscenter för CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Obs:** i paketet exemplet som följer förutsätter vi att lösningens namn är ”SampleSolution.zip”_

Om du är en ny ISV kan få du mer information om hur du skapar en lösning här: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Om din lösning kräver stöd för data:

* Skapa exempeldata i testmiljön
* Använd konfigurationsverktyget för migrering för att skapa ett Schema med jämförelse regler för dina data.
* Spara din konfigurationsschema med dina projektfiler. Du behöver det senare om du uppdaterar konfigurationsdata.
* Exportera konfigurationsdata. Glöm inte att namnge exportfilen som är meningsfullt för exporten.
