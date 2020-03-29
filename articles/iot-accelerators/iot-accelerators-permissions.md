---
title: Använd webbplatsen Azure IoT Solutions - Azure | Microsoft-dokument
description: Beskriver hur du använder den AzureIoTSolutions.com webbplatsen för att distribuera din lösningsaccelerator.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447462"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Använd azureiotsolutions.com-platsen för att distribuera lösningsacceleratorn

Du kan distribuera Azure IoT-lösningsacceleratorer till din Azure-prenumeration från [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com är värd för både Microsofts open source- och partnerlösningsacceleratorer. Dessa lösningsacceleratorer anpassar sig till [Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture). Du kan använda platsen för att snabbt distribuera en lösningsaccelerator som en demo- eller produktionsmiljö.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Om du behöver mer kontroll över distributionsprocessen kan du använda [CLI för att distribuera en lösningsaccelerator](iot-accelerators-remote-monitoring-deploy-cli.md).

Du kan distribuera lösningsacceleratorerna i följande konfigurationer:

* **Standard**: En utökad infrastrukturdistribution för att utveckla en produktionsmiljö. Azure Container Service distribuerar mikrotjänsterna till flera virtuella Azure-datorer. Kubernetes orkestrerar Docker-containrar som värdar för enskilda mikrotjänster.
* **Grundläggande**: En reducerad kostnadsversion för en demonstration eller för att testa en distribution. Alla mikrotjänster distribueras till en enda virtuell Azure-dator.
* **Lokal**: En lokal datordistribution för testning och utveckling. Den här metoden distribuerar mikrotjänsterna till en lokal Docker-behållare och ansluter till IoT Hub, Azure Cosmos DB och Azure-lagringstjänster i molnet.

Var och en av lösningsacceleratorerna använder en annan kombination av Azure-tjänster som IoT Hub, Azure Stream Analytics och Cosmos DB. Mer information finns i [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) och välj en lösningsaccelerator.

## <a name="sign-in-at-azureiotsolutionscom"></a>Logga in på azureiotsolutions.com

Innan du kan distribuera en lösningsaccelerator måste du logga in på AzureIoTSolutions.com med autentiseringsuppgifter som är associerade med en Azure-prenumeration. Om ditt konto är associerat med mer än en Microsoft Azure Active Directory (AD) klient kan du använda **listrutan Kontoval** för att välja vilken katalog som ska användas.

Dina behörigheter för att distribuera lösningsacceleratorer, hantera användare och hantera Azure-tjänster beror på din roll i den valda katalogen. Vanliga Azure AD-roller som är associerade med lösningsacceleratorerna är:

* **Global administratör**: Det kan finnas många [globala administratörer](../active-directory/users-groups-roles/directory-assign-admin-roles.md) per Azure AD-klient:

  * När du skapar en Azure AD-klient är du som standard den globala administratören för den klienten.
  * Den globala administratören kan distribuera grundläggande och standardiserade lösningsacceleratorer.

* **Domänanvändare:** Det kan finnas många domänanvändare per Azure AD-klientorganisation. En domänanvändare kan distribuera en grundläggande lösningsaccelerator.

* **Gästanvändare:** Det kan finnas många gästanvändare per Azure AD-klientorganisation. Gästanvändare kan inte distribuera en lösningsaccelerator i Azure AD-klienten.

Mer information om användare och roller i Azure AD finns i följande resurser:

* [Skapa användare i Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Tilldela användare till appar](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Välj enhet

Webbplatsen AzureIoTSolutions.com länkar till [Azure Certified for IoT-enhetskatalogen](https://catalog.azureiotsolutions.com/).

Katalogen listar hundratals certifierade IoT-maskinvaruenheter som du kan ansluta till dina lösningsacceleratorer för att börja bygga din IoT-lösning.

![Enhetskatalog](media/iot-accelerators-permissions/devicecatalog.png)

Om du är maskinvarutillverkare klickar du på **Bli partner** om du vill veta mer om hur du samarbetar med Microsoft i programmet Certified for IoT.

## <a name="next-steps"></a>Nästa steg

Ta en titt på snabbstarterna om du vill prova någon av IoT-lösningsacceleratorerna:

* [Testa en fjärrövervakningslösning](quickstart-remote-monitoring-deploy.md)
* [Testa en lösning för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [Testa en lösning för förutsägande underhåll](quickstart-predictive-maintenance-deploy.md)
* [Testa en enhetssimuleringslösning](quickstart-device-simulation-deploy.md)
