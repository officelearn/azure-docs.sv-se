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
ms.openlocfilehash: 364da4bb5f3f76f0a8a189db5aa59cca305470ea
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197191"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste autentisera åtkomsten till lagringskontot. Lägg till autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Här kan du visa åtkomstnycklarna för kontot och den fullständiga anslutningssträngen för varje nyckel.   
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att välja **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](media/storage-copy-connection-string-portal/portal-connection-string.png)
