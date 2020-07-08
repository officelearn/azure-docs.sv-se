---
title: Distribuera Azure File Sync agent
description: Distribuera Azure File Sync agenten. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143554"
---
I det här avsnittet installerar du Azure File Sync-agenten på Windows Server-instansen.

[Distributions guiden](../articles/storage/files/storage-sync-files-deployment-guide.md) illustrerar att du behöver inaktivera **förbättrad säkerhets konfiguration i Internet Explorer**. Den här säkerhets åtgärden kan inte användas med Azure File Sync. Genom att inaktivera det kan du autentisera till Azure utan problem.

Öppna PowerShell och installera de nödvändiga PowerShell-modulerna med hjälp av följande kommandon. Se till att installera hela modulen och NuGet-providern när du uppmanas till det.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Om du har problem med att nå Internet från servern är det dags att lösa dem. Azure File Sync använder en tillgänglig nätverks anslutning till Internet. Det finns också stöd för att en proxyserver ska kunna ansluta till Internet. Du kan antingen konfigurera en datorövergripande proxy nu eller ange en proxy som bara Azure File Sync kommer att använda under Agent installationen.

Om du konfigurerar en proxy måste du öppna dina brand väggar för den här servern, vilket kan vara en acceptabel metod för dig. När Server installationen är klar visas en nätverks anslutnings rapport med de exakta slut punkts-URL: erna i Azure som Azure File Sync måste kommunicera med för den region som du har valt. Rapporten visar även varför kommunikationen krävs. Du kan använda rapporten för att låsa brand väggarna runt den här servern till vissa URL: er.

Du kan också följa en mer försiktig metod där du inte öppnar brand väggarna, utan begränsar i stället servern till att kommunicera med DNS-namnrymder på högre nivå. Mer information finns i [Azure File Sync proxy-och brand Väggs inställningar](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Följ dina egna bästa metoder för nätverk.

I slutet av guiden Server *installation* öppnas en guide för Server *registrering* . Registrera servern på Azure-resursen för lagrings synkronisering från tidigare.

De här stegen beskrivs i mer detalj i distributions guiden, inklusive PowerShell-moduler som du bör installera först: [Azure File Sync agent installation](../articles/storage/files/storage-sync-files-deployment-guide.md).

Använd den senaste agenten. Du kan ladda ned det från Microsoft Download Center: [Azure File Sync-agenten](https://aka.ms/AFS/agent "Ladda ned Azure File Sync agent").

Efter en lyckad installation och Server registrering kan du kontrol lera att du har slutfört det här steget. Gå till resursen Storage Sync service i Azure Portal och följ sedan den vänstra menyn för **registrerade servrar**. Servern visas där.
