---
title: 'Snabb start: identifiera tal, avsikter och entiteter, python-tal-tjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660576"
---
För att slutföra snabb starten för avsikts igenkänning måste du skapa ett LUIS-konto och ett projekt med hjälp av LUIS Preview Portal. Den här snabb starten kräver bara en LUIS-prenumeration. En röst tjänst prenumeration krävs inte.

Det första du behöver göra är att skapa ett LUIS-konto och en app med hjälp av LUIS Preview Portal. LUIS-appen som du skapar använder en fördefinierad domän för start automatisering, som tillhandahåller avsikter, entiteter och exempel yttranden. När du är klar har du en LUIS-slutpunkt som körs i molnet som du kan ringa med hjälp av tal-SDK. 

Följ de här anvisningarna för att skapa en LUIS-app: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Snabb start: skapa en fördefinierad domän-app</a>

När du är klar behöver du tre saker: 

* Din LUIS-nyckel
* Din LUIS-region
* Ditt LUIS app-ID

Här kan du hitta den här informationen i [Luis Preview Portal](https://preview.luis.ai/):

1. Välj **Hantera**på Luis Preview-portalen och välj sedan **Azure-resurser**. På den här sidan hittar du din LUIS-nyckel och plats (kallas ibland _region_).  

   > [!div class="mx-imgBorder"]
   > ![LUIS nyckel och plats](../../../media/luis/luis-key-region.png)

2. När du har fått nyckeln och platsen behöver du app-ID: t. Välj **program inställningar** – ditt app-ID är tillgängligt på den här sidan.

   > [!div class="mx-imgBorder"]
   > ![LUIS app-ID](../../../media/luis/luis-app-id.png)