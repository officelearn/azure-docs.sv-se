---
title: Rotering av åtkomstnyckel för Azure SignalR Service
description: En översikt över varför du regelbundet måste rotera åtkomstnycklar och hur du gör det med portalen GUI och CLI.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636086"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Rotering av åtkomstnyckel för Azure SignalR Service

Varje Azure SignalR Service-instans har ett par åtkomstnycklar: Primära och sekundära nycklar. De används för att autentisera SignalR-klienter vid begäranden till tjänsten. Nycklarna associeras med instansens slutpunkts-URL. Skydda dina nycklar och rotera dem regelbundet. Du får två åtkomstnycklar så att du kan upprätthålla anslutningar med en nyckel medan den andra återskapas.

## <a name="why-rotate-access-keys"></a>Varför ska åtkomstnycklar roteras?

På grund av säkerhets- och efterlevnadskrav rekommenderas utvecklare att rotera åtkomstnycklarna regelbundet.

## <a name="how-to-regenerate-access-keys"></a>Hur återskapas åtkomstnycklar?

1. Gå till [Azure-portalen](https://portal.azure.com/) och logga in med dina autentiseringsuppgifter.

1. Leta upp avsnittet **Nycklar** från den Azure SignalR Service-instans som du vill återskapa nycklarna från.

1. Klicka på **Nycklar** på navigeringsmenyn.

1. Klicka på **Återskapa primärnyckel** eller **Återskapa sekundärnyckel**.

En ny nyckel och motsvarande anslutningssträng skapas och visas.

 ![Återskapa nycklar](media/signalr-key-rotation/regenerate-keys.png)

Du kan också återskapa nycklar med hjälp av [Azure CLI](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Uppdatera konfigurationer med nya anslutningssträngar

1. Kopiera den nyskapade anslutningssträngen.

1. Uppdatera alla konfigurationer för att använda den nya anslutningssträngen.

1. Starta om programmet vid behov.

## <a name="forced-access-key-regeneration"></a>Framtvingad återskapning av åtkomstnyckel

Azure SignalR Service kan framtvinga obligatoriskt återskapande av åtkomstnyckeln vid vissa situationer. Tjänsten meddelar kunder via e-post och portalmeddelande. Om du får detta meddelande eller om tjänsten inte fungerar på grund av åtkomstnyckeln, kan du rotera nycklarna genom att följa den här guiden.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du roterar åtkomstnycklarna regelbundet som en säkerhetsrutin.

I den här guiden har du lärt dig om hur du återskapar åtkomstnycklar. Gå vidare till nästkommande självstudiekurser om autentisering med OAuth eller med Azure Functions.

> [!div class="nextstepaction"]
> [Integrera med ASP.NET Core-identitet](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Skapa en app utan server i realtid med autentisering](./signalr-authenticate-azure-functions.md)