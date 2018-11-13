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
ms.openlocfilehash: 499ffdf7e91d4ff6926f59ff534142e0a1c5d6c5
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51222198"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste auktorisera åtkomsten till ditt lagringskonto. Tillhandahåll autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna och anslutningssträngen för ditt konto visas.
4. Notera namnet på lagringskontot. Du behöver det för auktoriseringen.   
5. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.

    ![Skärmbild som visar hur du kopierar kontonyckeln från Azure-portalen](media/storage-copy-account-key-portal/portal-account-key.png)
