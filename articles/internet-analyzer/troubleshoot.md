---
title: Felsökning av Azure Internet Analyzer | Microsoft-dokument
description: Felsökningsreferensen för Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069225"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Felsökning av Azure Internet Analyzer

Den här artikeln innehåller felsökningssteg för vanliga Problem med Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Saker att tänka på
- Klientskriptet måste bäddas in på en **HTTPS-webbplats.** Mått samlas inte in om skriptet körs i en klartext **(http://**) eller lokal **(file://)** webbplats.
- Mätdata samlas bara in om Internet Analyzer-profilens klientskript har bäddats in i ett program som tar emot verklig användartrafik. Syntetisk trafik (till exempel Azure WebApp Performance Tests) kör vanligtvis inte inbäddad Javascript-kod, så inga mätningar genereras av den typen av trafik.

## <a name="azure-portal"></a>Azure Portal
**"Ett styrkort har inte genererats för den valda filterkombinationen" i avsnittet Styrkort**
- Styrkort genereras dagligen (i slutet av varje dag, UTC-tid).
- Styrkort genereras endast om mer än 100 mätningar samlades in för den valda filterkombinationen (Test, Tidsperiod, Land osv.).

**"Totalt antal måttvärden" är noll för en eller båda slutpunkterna i ett test**
- Tidsserier och mätantal beräknas en gång i timmen, så du måste vänta minst den tiden för att nya mätdata ska visas.
- Internet Analyzer räknar endast lyckade mätningar (dvs. HTTP 200-svar) för sin analys. Om en eller båda slutpunkterna i ett test inte kan nås eller returnerar en HTTP-kod som inte är 200, visas de med noll totala mått.

## <a name="next-steps"></a>Nästa steg
Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md)
