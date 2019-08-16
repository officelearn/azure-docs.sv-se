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
ms.openlocfilehash: 1eadda6e36e7e3798bf055b138bec1b897e3014d
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541550"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste auktorisera åtkomsten till ditt lagringskonto. Tillhandahåll autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna och anslutningssträngen för ditt konto visas.
4. Notera namnet på lagringskontot. Du behöver det för auktoriseringen.   
5. Hitta **nyckel** värdet under **KEY1**och välj **Kopiera** för att kopiera konto nyckeln.

    ![Skärmbild som visar hur du kopierar kontonyckeln från Azure-portalen](media/storage-copy-account-key-portal/portal-account-key.png)
