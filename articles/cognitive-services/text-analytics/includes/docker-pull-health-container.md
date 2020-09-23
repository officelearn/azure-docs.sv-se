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
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906104"
---
Fyll i och skicka [fråge formuläret för Cognitive Services containers](https://aka.ms/cognitivegate) för att begära åtkomst till behållaren.
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
