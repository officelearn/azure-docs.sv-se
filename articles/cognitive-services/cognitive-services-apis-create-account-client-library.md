---
title: Skapa Cognitive Services resurs med hjälp av klient biblioteket för Azure Management
titleSuffix: Azure Cognitive Services
description: Skapa och hantera Azure Cognitive Services-resurser med hjälp av klient biblioteket för Azure-hantering.
services: cognitive-services
keywords: kognitiva tjänster, kognitiv intelligens, kognitiva lösningar, AI-tjänster
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262458"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Snabb start: skapa en Cognitive Services resurs med hjälp av klient biblioteket för Azure-hantering

Använd den här snabb starten för att skapa och hantera Azure Cognitive Services-resurser med hjälp av klient biblioteket för Azure-hantering.

Azure Cognitive Services är Cloud-Base-tjänster med REST API: er och SDK: er för klient bibliotek som är tillgängliga för att hjälpa utvecklare att bygga kognitiv information i program utan att ha direkt artificiell intelligens (AI) eller data vetenskaps kunskaper eller kunskap. Azure Cognitive Services gör det möjligt för utvecklare att enkelt lägga till kognitiva funktioner i sina program med kognitiva lösningar som kan se, höra, tala, förstå och till och med börja på skäl.

Enskilda AI-tjänster representeras av Azure- [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) som du skapar under din Azure-prenumeration. När du har skapat en resurs kan du använda nycklarna och slut punkten som genereras för att autentisera dina program.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
