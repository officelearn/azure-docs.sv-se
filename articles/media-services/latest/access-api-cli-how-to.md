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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 5dbcf446a609adcd0f1902fcca2ac19ad87f17b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65779672"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Åtkomst till Azure Media Services API med Azure CLI
 
Om du vill använda den Azure AD autentiseringen av tjänstens huvudnamn för att ansluta till Azure Media Services-API, behöver programmet för att begära en Azure AD-token som har följande parametrar:

* Azure AD-klientorganisationsslutpunkt
* Media Services resurs-URI
* Resurs-URI för REST Media Services
* Azure AD application värden: klient-ID och klienthemlighet

Detaljerad förklaring finns i [API: er för åtkomst till Media Services v3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Den här artikeln visar hur du använder Azure CLI för att skapa en Azure AD-program och tjänstens huvudnamn och hämta de värden som behövs för att få åtkomst till Azure Media Services-resurser.

## <a name="prerequisites"></a>Nödvändiga komponenter 

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
- [Koda med en anpassad transformering – CLI](custom-preset-cli-howto.md)
- [Skapa jobb – CLI](cli-create-jobs.md)
- [Skapa EventGrid – CLI](job-state-events-cli-how-to.md)
- [Publicera en resurs – CLI](cli-publish-asset.md)
- [Filtrera – CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Nästa steg

Slutpunkt för direktuppspelning som du vill spela upp innehåll måste vara i tillståndet körs. Följande CLI-kommando startar din standard-slutpunkt för direktuppspelning:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

