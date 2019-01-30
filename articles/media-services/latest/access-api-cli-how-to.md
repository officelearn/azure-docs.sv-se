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
ms.openlocfilehash: 43f9443e4b5cd700500bd9803f2737ed9e0aa633
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223177"
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

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
