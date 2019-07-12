---
title: ta med fil
description: ta med fil
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608377"
---
### <a name="preview"></a>Hur jag för att delta i den offentliga förhandsversionen?

Du måste publicera för att kunna delta i den offentliga förhandsversionen. Följ stegen i [i den här artikeln](../articles/bastion/bastion-create-host-portal.md) att skapa en ny Azure Skyddsmiljö-resurs. För närvarande när åtkomst till och med den här tjänsten, måste du använda den [Azure-portalen – förhandsversion](https://aka.ms/BastionHost) i stället för vanliga Azure-portalen.

### <a name="regions"></a>Vilka regioner är tillgängliga under förhandsperioden?

Du kan distribuera och använda skyddade resursen i någon av dessa förhandsversionsregioner via den [Azure-portalen – förhandsversion länk](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Jag hittar inte den skyddade resursen i Azure-portalen. Vad ska jag göra?

Se till att du använder den [Azure-portalen – förhandsversion länken](https://aka.ms/BastionHost), inte vanliga Azure-portalen.

### <a name="publicip"></a>Behöver jag en offentlig IP på min virtuella dator?

Du behöver inte en offentlig IP på Azure-dator som du ansluter till med tjänsten Azure Skyddsmiljö. Tjänsten Skyddsmiljö öppnas RDP/SSH-session/anslutning till den virtuella datorn över den privata IP-Adressen för den virtuella datorn i det virtuella nätverket.

### <a name="rdpssh"></a>Behöver jag en RDP eller SSH-klient?

Du behöver ingen RDP- eller SSH-klient för att få åtkomst till RDP/SSH till din virtuella Azure-dator i din Azure-portalen. Använd den [Azure-portalen – förhandsversion länken](https://aka.ms/BastionHost) att få åtkomst till förhandsversionen flygning med portalen. Då kan du få RDP/SSH-åtkomst till din virtuella dator direkt i webbläsaren.

### <a name="agent"></a>Behöver jag en agent som körs i Azure-dator?

Du behöver inte installera någon agent eller programvara i webbläsaren eller den virtuella Azure-datorn. Tjänsten Bastion är agentlös och ingen ytterligare programvara krävs för RDP/SSH.

### <a name="browsers"></a>Vilka webbläsare stöds?

Den offentliga förhandsversionen, använder du Microsoft Edge-webbläsaren eller Google Chrome på Windows. För Apple Mac använder du webbläsaren Google Chrome. Microsoft Edge Chromium kan också användas på både Windows och Mac.

### <a name="roles"></a>Är alla roller som krävs för att få åtkomst till en virtuell dator?

För att upprätta en anslutning, krävs följande roller:

* Läsarroll för på den virtuella datorn
* Läsarroll på nätverkskortet med privata IP-Adressen för den virtuella datorn
* Läsarroll för Azure-skyddade resursen

### <a name="previewbill"></a>Priser – faktureras jag för att du deltar i förhandsgranskningen?

Du kommer bara att debiteras delvis allmänt tillgängliga förhandsversionen. Det finns dock inget serviceavtal som är kopplat till din distribution. Mer information finns på sidan med [priser](https://aka.ms/BastionHostPricing).

### <a name="previewbill"></a>Varför får jag ”din session har upphört att gälla” fel meddelandet innan du startar Skyddsmiljö-sessionen?

En session ska initieras från Azure-portalen. Logga in på Azure Portal och börja sessionen igen. Om du går till URL: en direkt från en annan webbläsarsession eller flik kan är det här felet förväntat. Det säkerställer att din session är säkrare och att sessionen är tillgänglig endast via Azure portal.
