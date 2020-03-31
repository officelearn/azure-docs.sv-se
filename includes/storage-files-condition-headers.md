---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737499"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Felvillkor Inte Stöds från ett webbprogram med Azure-filer från webbläsaren

Felet ConditionHeadersNotSupported uppstår när du öppnar innehåll som finns i Azure-filer via ett program som använder villkorliga huvuden, till exempel en webbläsare, misslyckas åtkomsten. Felet anger att villkorshuvuden inte stöds.

![Villkorshuvuden för Azure Files](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Orsak

Villkorliga rubriker stöds ännu inte. Program som implementerar dem måste begära hela filen varje gång filen används.

### <a name="workaround"></a>Lösning

När en ny fil överförs är egenskapen för cachekontroll som standard "no-cache". För att tvinga programmet att begära filen varje gång måste filens cachekontrollegenskap uppdateras från "no-cache" till "no-cache, no-store, must-revalidate". Detta kan uppnås med Hjälp av [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Innehållscachemodifiering av lagringsutforskare för villkorliga Azure-filer](media/storage-files-condition-headers/storage-explorer-cache.png)