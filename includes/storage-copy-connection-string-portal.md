---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8f511eafda70fca997a08b5907e606e572b8e219
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50973987"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste autentisera åtkomsten till lagringskontot. Lägg till autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot genom att följa dessa steg:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Här kan du visa åtkomstnycklarna för kontot och den fullständiga anslutningssträngen för varje nyckel.   
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att välja **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](media/storage-copy-connection-string-portal/portal-connection-string.png)
