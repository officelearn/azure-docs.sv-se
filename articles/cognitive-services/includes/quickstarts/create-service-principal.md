---
title: Skapa Azure-tjänstens huvudnamn
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321650"
---
## <a name="create-an-azure-service-principal"></a>Skapa ett huvud namn för Azure-tjänsten

För att ditt program ska interagera med ditt Azure-konto behöver du ett Azure-tjänstens huvud namn för att hantera behörigheter. Följ instruktionerna i [skapa ett huvud namn för Azure-tjänsten](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

När du skapar ett huvud namn för tjänsten visas det ett hemligt värde, ett ID och ett program-ID. Spara program-ID och hemlighet till en tillfällig plats för senare steg.