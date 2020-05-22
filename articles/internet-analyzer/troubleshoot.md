---
title: Fel sökning av Azure Internet Analyzer | Microsoft Docs
description: Fel söknings referensen för Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745471"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Fel sökning i Azure Internet Analyzer

Den här artikeln innehåller fel söknings steg för vanliga problem med Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Saker att tänka på
- Klient skriptet måste vara inbäddat i en **https** -webbplats. Mätningar samlas inte in om skriptet körs i en oformaterad text-(**http://**) eller lokal webbplats (**File://**).
- Mät data samlas endast in om Internet Analyzer-profilens klient skript har bäddats in i ett program som tar emot faktisk användar trafik. Syntetisk trafik (till exempel Azure WebApp-prestandatester) kör vanligt vis inte inbäddad JavaScript-kod, så inga mätningar genereras av den typen av trafik.

## <a name="azure-portal"></a>Azure Portal
**"Ett styrkort har inte skapats för den valda filter kombinationen" i avsnittet styrkort**
- Styrkort skapas per dag (i slutet av varje dag, UTC-tid).
- Styrkort skapas endast om fler än 100 mått samlades in för den valda filter kombinationen (test, tids period, land/region osv.).

**"Totalt antal mätningar" är noll för en eller båda slut punkterna i ett test**
- Tids serier och mätnings antal beräknas en gång i timmen, så du måste vänta minst den tiden för att nya Mät data ska visas.
- Internet Analyzer räknar endast lyckade mätningar (t. ex. HTTP 200-svar) för analys. Om en eller båda slut punkterna i ett test inte kan kommas åt eller returnerar en HTTP-kod som inte är 200, visas de med noll total mått.

## <a name="next-steps"></a>Nästa steg
Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)
