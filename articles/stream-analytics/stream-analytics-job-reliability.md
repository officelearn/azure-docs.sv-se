---
title: "Undvika avbrott i tjänsten med Azure Stream Analytics-jobb | Microsoft Docs"
description: "Riktlinjer för att göra Stream Analytics jobb uppgraderingen flexibel."
services: stream-analytics
documentationCenter: 
authors: samacha
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 76e347ee62ffc07db1d8e74cf0ac5327a154fe4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantera Stream Analytics-jobbet tillförlitlighet under uppdateringar av tjänsten

En del av en helt hanterad tjänst som är möjligheten att införa den nya tjänsten funktioner och förbättringar i en snabb takt. Stream Analytics kan därför ha en tjänstuppdatering distribuera varje vecka (eller oftare). Oavsett hur mycket testning finns fortfarande en risk för att en befintlig, körs jobbet avbryts på grund av ett programfel. För kunder som kör kritiska strömmande bearbetning jobb behöver dessa risker undvikas. En funktion som kunder kan använda för att minska denna risk är Azures  **[parad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  modell. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur parad Azure-regioner adressera detta?

Stream Analytics garanterar jobb i parad regioner har uppdaterats i separata grupper. Det finns därför en tillräcklig lucka mellan uppdateringar att identifiera potentiella bryta buggar och åtgärda dem.

_Med undantag för centrala Indien_ (vars parad region, södra Indien och inte har Stream Analytics förekomst), distribution av en uppdatering till Stream Analytics inte skulle uppstå samtidigt i en parad regioner. Distribution i flera områden **i samma grupp** kan uppstå **samtidigt**.

Artikel om  **[tillgänglighet och parad regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)**  har den senaste informationen som regioner har parats ihop.

Kunder bör distribuera identiska jobb till båda parad regioner. Förutom Stream Analytics interna övervakningsfunktionerna kunder bör också övervaka jobb som om **både** produktion jobb. Om ett avbrott identifieras som ett resultat av tjänstuppdatering Stream Analytics eskalera på rätt sätt och över några underordnade konsumenter till Felfri jobbutdata. Eskalering till stöd för kommer att förhindra att parad region påverkas av den nya distributionen och underhålla integriteten hos parad jobb.
