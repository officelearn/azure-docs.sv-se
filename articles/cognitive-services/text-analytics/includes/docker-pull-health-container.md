---
title: Docker-hämtning för hälso containern
titleSuffix: Azure Cognitive Services
description: Docker pull-kommando för Textanalys för hälso container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779635"
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
