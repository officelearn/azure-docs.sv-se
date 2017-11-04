---
title: "Analysera användningsmönster Azure CDN | Microsoft Docs"
description: "Kunden kan aktivera logganalys för Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.openlocfilehash: 04e5499011e72dfcc20dff370d5d837227ed29b6
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analysera användningsmönster Azure CDN

När du aktiverar CDN för programmet, du övervaka CDN användningen, kontrollera hälsotillståndet för din leverans och felsöka problem. Azure CDN finns dessa funktioner i följande två sätt: 

## <a name="verizon-core-reports"></a>Verizon Core rapporter

Du kan visa Verizon Core rapporter i den kompletterande portalen Verizon som en Azure CDN-användare med en Verizon standard eller premium profil Verizon. Verizon Core rapporter är tillgängliga via den **hantera** alternativet från Azure-portalen och erbjuder en mängd olika vyer och diagram. Mer information finns i [Core rapporter från Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Verizon anpassade rapporter

Du kan visa Verizon anpassade rapporter i den kompletterande portalen Verizon som en Azure CDN-användare med en Verizon standard eller premium profil Verizon. Verizon egna rapporter är tillgängliga via den **hantera** alternativet från Azure-portalen. De Verizon egna rapporter visar antalet träffar eller data som överförs för varje gräns CName som hör till en Azure CDN-profilen. HTTP-kod eller cache svarsstatusen kan grupperas data över tid. Mer information finns i [anpassade rapporter från Verizon](cdn-verizon-custom-reports.md).

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Core analytics via Azure diagnostikloggar

Core analytics är tillgängligt för alla CDN-slutpunkter som hör till Verizon (Standard och Premium) och Akamai (Standard) CDN profiler. Azure diagnostics loggar Tillåt core analytics exporteras till Azure storage, händelsehubbar eller logganalys Operations Management Suite (OMS). OMS logganalys erbjuder en lösning med diagram som kan konfigureras och anpassas. Mer information finns i [Azure diagnostikloggar](cdn-azure-diagnostic-logs.md).

