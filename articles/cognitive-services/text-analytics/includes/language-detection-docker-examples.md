---
title: Docker-exempel för Språkidentifiering container
titleSuffix: Azure Cognitive Services
description: Docker-exempel för Språkidentifiering container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 6e09a19ceb7b892a8ec405b348700b65c9d55ec4
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148499"
---
### <a name="language-detection-container-docker-examples"></a>Docker-exempel för Språkidentifiering container

Följande Docker-exempel är för Språkidentifiering container.

#### <a name="basic-example"></a>Grundläggande exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Loggnings exempel 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
