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
ms.openlocfilehash: b28049f1854494b61d63824334b986d814a641cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309326"
---
Fyll i och skicka [fråge formuläret för Cognitive Services containers](https://aka.ms/csgate) för att begära åtkomst till behållaren.
Formuläret efterfrågar information om dig, ditt företag och användar scenariot som du använder behållaren för. När du har skickat formuläret granskar Azure Cognitive Services-teamet det för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållar registret.

> [!IMPORTANT]
> * I formuläret måste du använda en e-postadress som är kopplad till ett Azure-prenumerations-ID.
> * Den Azure-resurs som du använder för att köra behållaren måste ha skapats med det godkända Azure-prenumerations-ID: t. 
> * Kontrol lera din e-postadress (både Inkorgen och skräppostmappen) för uppdateringar av status för ditt program från Microsoft.

Använd kommandot Docker Login med autentiseringsuppgifter som anges i ditt onboarding-e-postmeddelande för att ansluta till vårt privata behållar register för Cognitive Services behållare.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att ladda ned den här behållar avbildningen från vårt privata behållar register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
