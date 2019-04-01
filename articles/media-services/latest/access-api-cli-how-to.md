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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 257fe51cae245708816cd9a7bb0c33b6edf5aa05
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756010"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Åtkomst till Azure Media Services API med Azure CLI
 
Du bör använda den Azure AD autentiseringen av tjänstens huvudnamn för att ansluta till Azure Media Services-API. Programmet måste begära en Azure AD-token som har följande parametrar:

* Azure AD-klientorganisationsslutpunkt
* Media Services resurs-URI
* Resurs-URI för REST Media Services
* Azure AD application värden: klient-ID och klienthemlighet

Den här artikeln visar hur du använder Azure CLI för att skapa en Azure AD-program och tjänstens huvudnamn och hämta de värden som behövs för att få åtkomst till Azure Media Services-resurser.

## <a name="prerequisites"></a>Förutsättningar 

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
