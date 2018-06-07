---
title: Åtkomst till Azure Media Services API - CLI 2.0 | Microsoft Docs
description: Följ stegen i den här anvisningar åtkomst till Azure Media Services API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 9295c3f9dfabc8ef7749758e926df443843720a1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639783"
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Åtkomst till Azure Media Services API med CLI 2.0
 
Du bör använda Azure AD service principal autentisering för att ansluta till Azure Media Services API. Programmet måste begära en Azure AD-token som har följande parametrar:

* Azure AD-klient slutpunkt
* Media Services resurs-URI
* Resurs-URI för REST Media Services
* Azure AD application värden: klient-ID och klienthemlighet

Den här artikeln visar hur du använder CLI 2.0 för att skapa en Azure AD-program och tjänstens huvudnamn och värden som behövs för att komma åt resurser i Azure Media Services.

## <a name="prerequisites"></a>Förutsättningar 

Skapa ett nytt Azure Media Services-konto, enligt beskrivningen i [denna Snabbstart](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in i [Azure Portal](http://portal.azure.com) och starta **CloudShell** för att köra CLI-kommandon som visas i nästa steg.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Strömma en fil](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>Se också

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
