---
title: Använd webbplatsen för Azure IoT-lösningar – Azure | Microsoft Docs
description: Beskriver hur du använder AzureIoTSolutions.com-webbplatsen för att distribuera Solution Accelerator.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 6585718a549c0e0a6eac1ab50788798e1021fef9
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446823"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Använd azureiotsolutions.com-webbplatsen för att distribuera din lösnings Accelerator

Du kan distribuera Azure IoT Solution Accelerators till din Azure-prenumeration från [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com är värd för både Microsoft-acceleratorer med öppen källkod och partner lösningar. Dessa lösnings acceleratorer överensstämmer med [Azure IoT-referens arkitekturen](/azure/architecture/reference-architectures/iot). Du kan använda platsen för att snabbt distribuera en lösnings Accelerator som en demonstrations-eller produktions miljö.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Om du behöver mer kontroll över distributions processen kan du använda [CLI för att distribuera en lösnings Accelerator](iot-accelerators-remote-monitoring-deploy-cli.md).

Du kan distribuera lösnings acceleratorerna i följande konfigurationer:

* **Standard**: en utökad infrastruktur distribution för att utveckla en produktions miljö. Azure Container Service distribuerar mikrotjänsterna till flera virtuella Azure-datorer. Kubernetes orkestrerar Docker-containrar som värdar för enskilda mikrotjänster.
* **Basic**: en reducerad kostnads version för en demonstration eller för att testa en distribution. Alla mikrotjänster distribueras till en enda virtuell Azure-dator.
* **Lokal**: en lokal dator distribution för testning och utveckling. Den här metoden distribuerar mikrotjänster till en lokal Docker-behållare och ansluter till IoT Hub, Azure Cosmos DB och Azure Storage-tjänster i molnet.

Varje lösning acceleratorer använder en annan kombination av Azure-tjänster, till exempel IoT Hub, Azure Stream Analytics och Cosmos DB. Mer information finns på [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) och välj en lösnings Accelerator.

## <a name="sign-in-at-azureiotsolutionscom"></a>Logga in på azureiotsolutions.com

Innan du kan distribuera en lösnings Accelerator måste du logga in på AzureIoTSolutions.com med autentiseringsuppgifter som är associerade med en Azure-prenumeration. Om ditt konto är associerat med fler än en Microsoft Azure Active Directory-klient (AD) kan du använda **list rutan konto val** och välja vilken katalog som ska användas.

Dina behörigheter för att distribuera lösnings acceleratorer, hantera användare och hantera Azure-tjänster beror på din roll i den valda katalogen. Vanliga Azure AD-roller som är associerade med lösnings acceleratorerna är:

* **Global administratör**: det kan finnas många [globala administratörer](../active-directory/roles/permissions-reference.md) per Azure AD-klient:

  * När du skapar en Azure AD-klient är du som standard den globala administratören för den klienten.
  * Den globala administratören kan distribuera standard acceleratorer för Basic-och standard-lösningar.

* **Domän användare**: det kan finnas många domän användare per Azure AD-klient. En domän användare kan distribuera en Basic Solution Accelerator.

* **Gäst användare**: det kan finnas många gäst användare per Azure AD-klient. Gäst användare kan inte distribuera en lösnings Accelerator i Azure AD-klienten.

Mer information om användare och roller i Azure AD finns i följande resurser:

* [Skapa användare i Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Tilldela användare till appar](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Välj din enhet

AzureIoTSolutions.com-platsen länkar till [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com/).

Katalogen innehåller hundratals certifierade enheter för IoT-maskinvara som du kan ansluta till dina lösnings acceleratorer för att börja bygga din IoT-lösning.

![Enhets katalog](media/iot-accelerators-permissions/devicecatalog.png)

Om du är maskin varu tillverkare klickar du på **en partner** för att lära dig mer om att samar beta med Microsoft i programmet Certified for IoT.

## <a name="next-steps"></a>Nästa steg

Ta en titt på snabbstarterna om du vill prova någon av IoT-lösningsacceleratorerna:

* [Testa en fjärrövervakningslösning](quickstart-remote-monitoring-deploy.md)
* [Testa en lösning för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [Testa en lösning för förutsägande underhåll](quickstart-predictive-maintenance-deploy.md)
* [Testa en enhetssimuleringslösning](quickstart-device-simulation-deploy.md)