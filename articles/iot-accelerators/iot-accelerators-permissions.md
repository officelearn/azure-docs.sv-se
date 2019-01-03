---
title: Använda webbplatsen för Azure IoT-lösningar – Azure | Microsoft Docs
description: Beskriver hur du använder webbplatsen AzureIoTSolutions.com för att distribuera utvecklingsacceleratorn.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601091"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Använda azureiotsolutions.com plats för att distribuera din lösningsaccelerator

Du kan distribuera Azure IoT-Lösningsacceleratorer till din Azure-prenumeration från [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com är värd för både Microsoft öppen källkod och partner Lösningsacceleratorer. Dessa Lösningsacceleratorer överensstämmer med den [Referensarkitektur för Azure IoT](https://aka.ms/iotrefarchitecture). Du kan använda webbplatsen för att snabbt distribuera en lösningsaccelerator som en demo eller produktion-miljö.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Om du behöver mer kontroll över distributionsprocessen kan du använda den [CLI för att distribuera en lösningsaccelerator](iot-accelerators-remote-monitoring-deploy-cli.md).

Du kan distribuera Lösningsacceleratorer i följande konfigurationer:

* **Standard**: En för utökad infrastrukturdistribution för att utveckla en produktionsmiljö. Azure Container Service distribuerar mikrotjänsterna till flera virtuella Azure-datorer. Kubernetes orkestrerar Docker-containrar som värdar för enskilda mikrotjänster.
* **Grundläggande**: En lägre kostnadsversion för en demonstration eller för att testa en distribution. Alla mikrotjänster distribueras till en enda virtuell Azure-dator.
* **Lokala**: En lokal datordistribution för testning och utveckling. Den här metoden distribuerar mikrotjänsterna till en lokal Docker-behållare och ansluter till IoT Hub, Azure Cosmos DB och Azure storage-tjänster i molnet.

Var och en av Lösningsacceleratorer använder en annan kombination av Azure-tjänster som IoT Hub, Azure Stream Analytics och Cosmos DB. För mer information, besök [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) och välj en lösningsaccelerator.

## <a name="sign-in-at-azureiotsolutionscom"></a>Logga in på azureiotsolutions.com

Innan du kan distribuera en lösningsaccelerator, måste du logga in på AzureIoTSolutions.com med hjälp av autentiseringsuppgifter som är associerade med en Azure-prenumeration. Om ditt konto är associerad med fler än en klient i Microsoft Azure Active Directory (AD), kan du använda den **listruta för val av konto** att välja katalog.

Dina behörigheter för att distribuera Lösningsacceleratorer, hantera användare och hantera Azure-tjänster är beroende av din roll i den valda katalogen. Vanliga Azure AD-roller som är associerade med Lösningsacceleratorer är:

* **Global administratör**: Det kan finnas många [globala administratörer](../active-directory/users-groups-roles/directory-assign-admin-roles.md) per Azure AD-klient:

  * När du skapar en Azure AD-klient, är du som standard global administratör för klienten.
  * Global administratör kan distribuera basic och standard Lösningsacceleratorer.

* **Domänanvändare**: Det kan finnas många domänanvändare per Azure AD-klient. En domänanvändare kan distribuera en grundläggande lösningsaccelerator.

* **Gästanvändaren**: Det kan finnas många gästanvändare per Azure AD-klient. Gästanvändare kan inte distribuera en lösningsaccelerator i Azure AD-klient.

Mer information om användare och roller i Azure AD finns i följande resurser:

* [Skapa användare i Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Tilldela användare till appar](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Välj din enhet

Webbplatsen AzureIoTSolutions.com länkar till den [Azure Certified för IoT-enhetskatalog](https://catalog.azureiotsolutions.com/).

Katalogen innehåller hundratals certifierade IoT-maskinvaruenheter som du kan ansluta till dina Lösningsacceleratorer för att börja skapa din IoT-lösning.

![Enhetskatalog](media/iot-accelerators-permissions/devicecatalog.png)

Om du är en maskinvarutillverkare, klickar du på **bli en Partner** vill veta mer om partnerskap med Microsoft på Certified for IoT-programmet.

## <a name="next-steps"></a>Nästa steg

Ta en titt på snabbstarterna om du vill prova någon av IoT-lösningsacceleratorerna:

* [Testa en fjärrövervakningslösning](quickstart-remote-monitoring-deploy.md)
* [Testa en lösning för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [Testa en lösning för förutsägande underhåll](quickstart-predictive-maintenance-deploy.md)
* [Testa en enhetssimuleringslösning](quickstart-device-simulation-deploy.md)
