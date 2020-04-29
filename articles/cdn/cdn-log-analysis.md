---
title: Analysera Azure CDN användnings mönster | Microsoft Docs
description: I den här artikeln beskrivs de olika typerna av analys rapporter som är tillgängliga för Azure CDN produkter.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253619"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster för Azure CDN

När du har aktiverat CDN för ditt program kan du övervaka CDN-användning, kontrol lera hälso tillståndet för leveransen och felsöka eventuella problem. Azure CDN tillhandahåller dessa funktioner på följande sätt: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Core Analytics via Azure Diagnostic-loggar

Core Analytics är tillgängligt för CDN-slutpunkter för alla pris nivåer. Azure Diagnostics-loggar låter kärn analyser exporteras till Azure Storage, Event Hub eller Azure Monitor loggar. Azure Monitor-loggar erbjuder en lösning med grafer som kan konfigureras av användaren och anpassas. Mer information om Azure Diagnostic-loggar finns i [Azure Diagnostic-loggar](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon Core-rapporter

Som Azure CDN användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profilen kan du Visa Verizon Core-rapporter i den kompletterande portalen för Verizon. Verizon Core-rapporter är tillgängligt via alternativet **Hantera** från Azure Portal och erbjuder en mängd olika grafer och vyer. Mer information finns i [Core-rapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Anpassade Verizon-rapporter

Som Azure CDN användare med en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profilen kan du Visa nya Verizon-rapporter i den kompletterande portalen för Verizon. Anpassade Verizon-rapporter är tillgängliga via alternativet **Hantera** från Azure Portal. På sidan för anpassade Verizon-rapporter visas antalet träffar eller data som överförts för varje Edge CName som tillhör en Azure CDN profil. Data kan grupperas efter HTTP-svarskod eller cache-status under en viss tids period. Mer information finns i [anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium från Verizon-rapporter

Med **Azure CDN Premium från Verizon**kan du också komma åt följande rapporter:
   * [Avancerade HTTP-rapporter](cdn-advanced-http-reports.md)
   * [Realtidsstatistik](cdn-real-time-stats.md)
   * [Gränsnodsprestanda](cdn-edge-performance.md)

