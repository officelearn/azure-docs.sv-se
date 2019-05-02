---
title: Förberedelse av Dynamics 365-lösning
description: Ramverk för förpackning, installation och avinstallation av komponenter
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 919feb39c9cd84f8da0ef89827ad3e83e0eb9bc5
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935257"
---
# <a name="dynamics-365-solution-preparation"></a>Förberedelse av Dynamics 365-lösning

Dynamics 365 solutioning system är ett ramverk för paketering, installation och avinstallation komponenter som innehåller specifika funktioner. Lösningar som används av ISV: er och andra Microsoft Dynamics 365-partner för att distribuera tillägg som de skapar.

Om du är en befintlig Dynamics 365 (xRM) ISV kan du förmodligen redan har skapat en hanterad lösning och har en solution.zip-fil. Kontrollera att ”visningsnamn” och ”beskrivning” fält omfattar vad du vill att kunder ska se i din lösning. Dessa visas i Administrationscenter för CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Obs:** I följande paket-exempel förutsätter vi att lösningens namn är ”SampleSolution.zip”_

Om du är en ny ISV kan få du mer information om hur du skapar en lösning här: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Om din lösning kräver stöd för data:

* Skapa exempeldata i testmiljön
* Använd konfigurationsverktyget för migrering för att skapa ett Schema med jämförelse regler för dina data.
* Spara din konfigurationsschema med dina projektfiler. Du behöver det senare om du uppdaterar konfigurationsdata.
* Exportera konfigurationsdata. Glöm inte att namnge exportfilen som är meningsfullt för exporten.
