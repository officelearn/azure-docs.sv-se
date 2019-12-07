---
title: Fel sökning av Azure Internet Analyzer | Microsoft Docs
description: Fel söknings referensen för Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897395"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Fel sökning i Azure Internet Analyzer

Den här artikeln innehåller fel söknings steg för vanliga problem med Internet Analyzer.

## <a name="azure-portal"></a>Azure-portalen
**"Inte tillräckligt många mått har samlats in för det här styrkortet" i avsnittet styrkort**

Tänk på följande:
- Mät data samlas endast in om Internet Analyzer-profilens klient skript har bäddats in i ett program som tar emot faktisk användar trafik. Syntetisk trafik (till exempel Azure WebApp-prestandatester) kör vanligt vis inte inbäddad JavaScript-kod, så inga mätningar genereras av den typen av trafik.
- Tids serier genereras en gång i timmen, så du måste vänta minst den tiden för att nya Mät data ska visas.
- Styrkort skapas per dag (i slutet av varje dag, UTC-tid).
- Styrkort skapas endast om fler än 100 mått samlades in för den valda filter kombinationen (test, tids period, land osv.).

## <a name="next-steps"></a>Nästa steg
Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)
