---
title: Skapa Azure-tjänstens huvudnamn
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096086"
---
## <a name="create-an-azure-service-principal"></a>Skapa ett huvud namn för Azure-tjänsten

För att ditt program ska interagera med ditt Azure-konto behöver du ett Azure-tjänstens huvud namn för att hantera behörigheter. Följ instruktionerna i [skapa ett huvud namn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

När du skapar ett huvud namn för tjänsten visas det ett hemligt värde, ett ID och ett program-ID. Spara program-ID och hemlighet till en tillfällig plats för senare steg.