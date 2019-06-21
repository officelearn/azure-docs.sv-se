---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187411"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Fel ConditionHeadersNotSupported från ett webbprogram med Azure Files från webbläsaren

När åtkomst till innehåll som finns i Azure Files via ett program som gör användning av villkorlig rubriker, till exempel en webbläsare, åtkomst misslyckas, visas ett ConditionHeadersNotSupported-fel.

![ConditionHeaderNotSupported Error](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Orsak

Villkorlig rubriker stöds inte ännu. Program som implementerar dem måste du begära fullständig fil varje gång filen öppnas.

### <a name="workaround"></a>Lösning:

När en ny fil laddas upp, är cache-control-egenskapen som standard ”no-cache”. Om du vill tvinga programmet för att begära fil måste varje gång filegenskap cache-control uppdateras från ”no-cache” till ”no-cache, no-store måste-revalidate”. Detta kan uppnås med hjälp av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Storage explorer innehållscachen ändring](media/storage-files-condition-headers/storage-explorer-cache.png)