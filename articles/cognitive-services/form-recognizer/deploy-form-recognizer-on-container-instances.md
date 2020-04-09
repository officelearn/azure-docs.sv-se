---
title: Köra form recognizer-behållare i Azure Container-instanser
titleSuffix: Azure Cognitive Services
description: Distribuera behållaren för formulärkoneform till en Azure Container-instans och testa den i en webbläsare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: acba4d9fab784181fda5728c30831c8c1838b91f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879538"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Distribuera behållaren för formuläravkänningsbehållare till Azure Container-instanser

Lär dig hur du distribuerar behållaren för Cognitive [Services-formulärreform](form-recognizer-container-howto.md) till Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Den här proceduren visar skapandet av en Azure Form Recognizer-resurs. Sedan diskuterar vi att dra den tillhörande behållarbilden. Slutligen belyser vi möjligheten att utöva orkestrering av de två från en webbläsare. Med hjälp av behållare kan flytta utvecklarnas uppmärksamhet från att hantera infrastruktur till att istället fokusera på applikationsutveckling.

> [!IMPORTANT]
> Formulärreformerarbehållarna använder för närvarande version 1.0 av API:et för formulärre recognizeer. Du kan komma åt den senaste versionen av API:et med hjälp av den hanterade tjänsten i stället.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först fylla i och skicka [formuläret Cognitive Services Form Recognizer Containers åtkomstbegäran](https://aka.ms/FormRecognizerContainerRequestAccess) för att begära åtkomst till behållaren. Om du gör det registrerar du dig också för Datorseende. Du behöver inte registrera dig för formuläret för begäran om datorseende separat. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
