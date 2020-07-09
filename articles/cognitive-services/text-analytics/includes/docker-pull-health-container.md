---
title: Docker-hämtning för hälso containern
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Textanalys för hälso container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108983"
---
Fyll i och skicka [fråge formuläret för Cognitive Services containers](https://aka.ms/cognitivegate) för att begära åtkomst till behållaren.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Använd kommandot Docker Login med autentiseringsuppgifter som anges i ditt onboarding-e-postmeddelande för att ansluta till vårt privata behållar register för Cognitive Services behållare.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned den här behållar avbildningen från vårt privata behållar register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
