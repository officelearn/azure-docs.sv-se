---
title: Åtkomst till Azure Media Services API – Azure CLI | Microsoft Docs
description: Följ stegen i den här instruktions artikeln för att få åtkomst till Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896155"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Åtkomst Azure Media Services API med Azure CLI
 
Om du vill använda autentisering av Azure AD-tjänstens huvud namn för att ansluta till Azure Media Services API måste programmet begära en Azure AD-token som har följande parametrar:

* Azure AD-klientens slut punkt
* Media Services resurs-URI
* Resurs-URI för REST-Media Services
* Azure AD-program värden: klient-ID och klient hemlighet

Detaljerad förklaring finns i [komma åt Media Services v3-API: er](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Den här artikeln visar hur du använder Azure CLI för att skapa ett Azure AD-program och tjänstens huvud namn och hämta de värden som behövs för att få åtkomst till Azure Media Services resurser.

## <a name="prerequisites"></a>Krav 

[Skapa ett Media Services-konto](create-account-cli-how-to.md).

Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Se också

- [Skala mediereserverade enheter – CLI](media-reserved-units-cli-how-to.md)
- [Skapa ett Media Services-konto – CLI](create-account-cli-how-to.md) 
- [Återställa autentiseringsuppgifter för konto – CLI](cli-reset-account-credentials.md)
- [Skapa resurser – CLI](cli-create-asset.md)
- [Ladda upp en fil – CLI](cli-upload-file-asset.md)
- [Skapa transformeringar – CLI](cli-create-transform.md)
- [Koda med en anpassad Transform-CLI](custom-preset-cli-howto.md)
- [Skapa jobb – CLI](cli-create-jobs.md)
- [Skapa EventGrid – CLI](job-state-events-cli-how-to.md)
- [Publicera en resurs – CLI](cli-publish-asset.md)
- [Filtrera – CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Nästa steg

Den slut punkt för direkt uppspelning som du vill strömma innehåll från måste vara i kör tillstånd. Följande CLI-kommando startar din standard slut punkt för direkt uppspelning:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

