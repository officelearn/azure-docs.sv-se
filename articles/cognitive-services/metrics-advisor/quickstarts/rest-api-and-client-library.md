---
title: Metrics Advisor-klient bibliotek REST API
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att ansluta dina program till Metrics Advisor API från Azure Cognitive Services.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186872"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Snabb start: Använd klient bibliotek eller REST-API: er för att anpassa din lösning

Kom igång med mått Advisor REST API eller klient bibliotek. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter.

Använd mått rådgivare för att utföra:

* Lägga till en datafeed från en data Källa
* Kontrol lera inmatnings status
* Konfigurera identifiering och aviseringar 
* Fråga efter avvikelse identifierings resultat
* Diagnostisera avvikelser


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

- [Använda webbportalen](web-portal.md)
- [Publicera dina data strömmar](../how-tos/onboard-your-data.md)
    - [Hantera datafeeds](../how-tos/manage-data-feeds.md)
    - [Konfigurationer för olika data källor](../data-feeds-from-different-sources.md)
- [Konfigurera mått och finjustera konfiguration för identifiering](../how-tos/configure-metrics.md)
- [Justera avvikelseidentifiering med hjälp av feedback](../how-tos/anomaly-feedback.md)