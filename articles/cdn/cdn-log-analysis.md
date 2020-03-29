---
title: Analysera Användningsmönster för Azure CDN | Microsoft-dokument
description: I den här artikeln beskrivs de olika typerna av analysrapporter som är tillgängliga för Azure CDN-produkter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 238dea3c136daf13d3db7be41bed103a0cbf7636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593674"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster för Azure CDN

När du har aktiverat CDN för ditt program kan du övervaka CDN-användning, kontrollera leveransens hälsa och felsöka potentiella problem. Azure CDN tillhandahåller dessa funktioner på följande sätt: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kärnanalys via Azure-diagnostikloggar

Kärnanalys är tillgängligt för CDN-slutpunkter för alla prisnivåer. Azure-diagnostikloggar gör att kärnanalys kan exporteras till Azure-lagring, händelsehubbar eller Azure Monitor-loggar. Azure Monitor-loggar erbjuder en lösning med diagram som är användarkonfigurerbara och anpassningsbara. Mer information om Azure-diagnostikloggar finns i [Azure-diagnostikloggar](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon kärnrapporter

Som Azure CDN-användare med en **Azure CDN Standard från Verizon** eller Azure **CDN Premium från Verizon-profilen** kan du visa Verizons kärnrapporter i Verizons tilläggsportal. Verizons kärnrapporter är tillgängliga via alternativet **Hantera** från Azure-portalen och erbjuder en mängd olika diagram och vyer. Mer information finns i [Kärnrapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon anpassade rapporter

Som Azure CDN-användare med en **Azure CDN Standard från Verizon** eller Azure **CDN Premium från Verizon-profilen** kan du visa anpassade rapporter från Verizon i Verizons tilläggsportal. Verizons anpassade rapporter är tillgängliga via alternativet **Hantera** från Azure-portalen. Sidan Anpassade rapporter i Verizon visar antalet träffar eller data som överförs för varje CName-kant som tillhör en Azure CDN-profil. Data kan grupperas efter HTTP-svarskod eller cachestatus under en viss tidsperiod. Mer information finns i [Anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium från Verizon rapporter

Med **Azure CDN Premium från Verizon**kan du även komma åt följande rapporter:
   * [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
   * [Realtidsstatistik](cdn-real-time-stats.md)
   * [Gränsnodsprestanda](cdn-edge-performance.md)

