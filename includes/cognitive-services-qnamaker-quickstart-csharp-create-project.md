---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019111"
---
1. Öppna Visual Studio 2017 Community Edition.
1. Skapa ett nytt **konsolapprojekt (.Net Core)** och ge projektet namnet `QnaMakerQuickstart`. Godkänn standardinställningarna för de återstående inställningarna.
1. I Solution Explorer högerklickar du på projektnamnet, **QnaMakerQuickstart**, och väljer sedan **Hantera NuGet-paket...**.
1. I NuGet-fönstret väljer du **Webbläsare**, söker sedan efter **Newtonsoft.JSON** och installerar paketet. Det här paketet används för att parsa det JSON som returneras från QnA Maker HTTP-svaret. 