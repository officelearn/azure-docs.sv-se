---
title: Få åtkomst till Azure Media Services-API – Azure CLI | Microsoft Docs
description: Följ stegen i den här anvisningen att få åtkomst till Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 54a92e56df21b59430ed12f191a9cf7a918e14c9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612907"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Åtkomst till Azure Media Services API med Azure CLI
 
Du bör använda den Azure AD autentiseringen av tjänstens huvudnamn för att ansluta till Azure Media Services-API. Programmet måste begära en Azure AD-token som har följande parametrar:

* Azure AD-klientorganisationsslutpunkt
* Media Services resurs-URI
* Resurs-URI för REST Media Services
* Azure AD application värden: klient-ID och klienthemlighet

Den här artikeln visar hur du använder Azure CLI för att skapa en Azure AD-program och tjänstens huvudnamn och hämta de värden som behövs för att få åtkomst till Azure Media Services-resurser.

## <a name="prerequisites"></a>Förutsättningar 

- Installera och använda CLI lokalt kan du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) kommandona fungerar i Azure Cloud Shell. Det rekommenderas att använda CLI lokalt.

- [Skapa ett Media Services-konto](create-account-cli-how-to.md).

    Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-konto.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
