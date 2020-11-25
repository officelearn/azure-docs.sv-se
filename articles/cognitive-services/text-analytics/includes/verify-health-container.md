---
title: Verifiera hälso behållar instansen
titleSuffix: Azure Cognitive Services
description: Lär dig hur du verifierar hälso behållar instansen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009930"
---
### <a name="verify-that-a-container-is-running"></a>Verifiera att en behållare körs

1. Välj fliken **Översikt** och kopiera IP-adressen.
1. Öppna en ny flik i webbläsaren och ange IP-adressen. Ange till exempel `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Behållarens start sida visas, där du kan se att behållaren körs.

    ![Visa start sidan för behållaren för att kontrol lera att den körs](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Välj länken **Service API-Beskrivning** för att gå till behållarens Swagger-sida.

1. Välj någon av **post** -API: erna och välj **prova**. Parametrarna visas, vilket innehåller exempel på indatamängdar.

Du kan också använda flera webb adresser för att kontrol lera att behållaren körs.

|Förfrågan|Syfte|
|--|--|
|`http://localhost:5000/`|Containern tillhandahåller en startsida.|
|`http://localhost:5000/ready`|Begärd med GET, detta ger en verifiering av att behållaren är redo att acceptera en fråga mot modellen. Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/status`|Begärd med GET, t. ex./Ready-slutpunkten, validerar detta att behållaren körs utan att det uppstår någon fråga mot modellen. Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Via den här URL: en innehåller behållaren en fullständig uppsättning dokumentation för slut punkterna och en `Try it now` funktion. Med den här funktionen kan du ange dina inställningar i ett webbaserat HTML-formulär och göra frågan utan att behöva skriva någon kod. När frågan returnerar visas ett exempel på ett spiral kommando som visar de HTTP-rubriker och det text format som krävs. |
|`http://localhost:5000/demo`| Begärd via en webbläsare, ger den här funktionen en interaktiv visualisering av resultaten från frågor om inmatade textexempel eller en som du anger.  |

Använd den här begäran-URL: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` en för att skicka en fråga till behållaren.
