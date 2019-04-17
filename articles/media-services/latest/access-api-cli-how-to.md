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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617843"
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
- [Skapa ett Media Services-konto – CLI](./scripts/cli-create-account.md) 
- [Återställa autentiseringsuppgifter för konto – CLI](./scripts/cli-reset-account-credentials.md)
- [Skapa resurser – CLI](./scripts/cli-create-asset.md)
- [Ladda upp en fil – CLI](./scripts/cli-upload-file-asset.md)
- [Skapa transformeringar – CLI](./scripts/cli-create-transform.md)
- [Skapa jobb – CLI](./scripts/cli-create-jobs.md)
- [Skapa EventGrid – CLI](./scripts/cli-create-event-grid.md)
- [Publicera en resurs – CLI](./scripts/cli-publish-asset.md)
- [Filtrera – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Nästa steg

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
