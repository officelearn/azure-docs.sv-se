---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ffc91a6e01cfb479a4090bcf13f15a7bd20095c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "71203931"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste auktorisera åtkomsten till ditt lagringskonto. Tillhandahåll autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot:

1. In på [Azure-portalen](https://portal.azure.com) går till ditt lagringskonto.
1. I avsnittet **Inställningar** i översikten för lagringskonto väljer du **Åtkomstnycklar** för att visa dina kontoåtkomstnycklar och anslutningssträng.
1. Notera namnet på lagringskontot. Du behöver det för auktoriseringen.   
1. Leta reda på **nyckelvärdet** under **nyckel1**och välj **Kopiera** för att kopiera kontonyckeln.

    ![Skärmbild som visar hur du kopierar kontonyckeln från Azure-portalen](media/storage-copy-account-key-portal/portal-account-key.png)
