---
title: Analysera användningsmönster Azure CDN | Microsoft Docs
description: Den här artikeln beskriver de olika typerna av analysrapporter som är tillgängligt för Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 3f475c5cc9b766ea9aa5bd39d4a378e8deed5e35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster för Azure CDN

När du aktiverar CDN för programmet, du övervaka CDN användningen, kontrollera hälsotillståndet för din leverans och felsöka problem. Azure CDN innehåller dessa funktioner på följande sätt: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Core analytics via Azure diagnostikloggar

Core analytics är tillgängligt för alla CDN-slutpunkter som hör till Verizon (Standard och Premium) och Akamai (Standard) CDN profiler. Azure diagnostics loggar Tillåt core analytics exporteras till Azure storage, händelsehubbar eller logganalys. Logganalys erbjuder en lösning med diagram som kan konfigureras och anpassas. Mer information finns i [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon core rapporter

Som en Azure CDN-användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil, du kan visa Verizon core rapporter i den kompletterande portalen Verizon. Verizon core rapporter är tillgängliga via den **hantera** alternativet från Azure-portalen och erbjuder en mängd olika vyer och diagram. Mer information finns i [Core rapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon anpassade rapporter

Som en Azure CDN-användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** profil, kan du visa Verizon anpassade rapporter i den kompletterande portalen Verizon. Verizon anpassade rapporter är tillgängliga via den **hantera** alternativet från Azure-portalen. Verizon anpassade rapporter sidan visar antalet träffar eller data som överförs för varje gräns CName som hör till en Azure CDN-profilen. HTTP-kod eller cache svarsstatusen kan grupperas data över tid. Mer information finns i [anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="verizon-premium-reports"></a>Verizon premium rapporter

Med **Azure CDN Premium från Verizon**, du kan även använda följande rapporter:
   * [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
   * [Realtidsstatistik](cdn-real-time-stats.md)
   * [Gränsnodsprestanda](cdn-edge-performance.md)

