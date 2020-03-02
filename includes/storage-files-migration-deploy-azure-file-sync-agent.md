---
title: Distribuera Azure File Sync agent
description: Distribuera Azure File Sync agenten. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209420"
---
I det här avsnittet installerar du Azure File Sync-agenten på Windows-servern.
[Distributions guiden](../articles/storage/files/storage-sync-files-deployment-guide.md) visar att du behöver inaktivera **förbättrad säkerhet i Internet Explorer**. Förbättrad säkerhet i Internet Explorer är ett säkerhets mått som inte är tillämpligt med Azure File Sync och när du inaktiverar det kan du autentisera till Azure utan problem.

Öppna PowerShell och installera de nödvändiga PowerShell-modulerna med följande kommandon. Se till att installera hela modulen och NuGet-providern när du uppmanas till det:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Om du har problem med att nå Internet från servern är det dags att lösa dem. Azure File Sync använder en tillgänglig nätverks anslutning till Internet.
Det finns också stöd för att en proxyserver ska kunna ansluta till Internet. Du kan antingen konfigurera en datorövergripande proxy nu eller ange en proxy som bara används av filsynkronisering under agent installation.

Om det innebär att du måste öppna brand väggarna för den här servern kan det vara en acceptabel metod för dig. Efter en slutförd Server registrering i slutet av Server installationen visas en nätverks anslutnings rapport som visar de exakta slut punkts-URL: erna i Azure. filsynkroniseringen måste kommunicera med för den region som du har valt. Rapporten visar även varför kommunikationen krävs. Du kan använda rapporten för att låsa brand väggarna runt den här servern, till vissa URL: er.

Du kan också följa en mer försiktig metod, i vilken du inte öppnar brand väggarna, utan i stället begränsa servern till att kommunicera med DNS-namn utrymmen på högre nivå – det finns mer dokumentation och information som finns i artikeln [Azure File Sync proxy och brand Väggs inställningar](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . Följ dina egna bästa metoder för nätverk.

I slutet av guiden Server *installation* visas en guide för Server *registrering* .
Registrera servern på Azure-resursen för lagrings synkronisering från tidigare.

De här stegen beskrivs i detalj i distributions guiden, inklusive ovanstående PowerShell-moduler som du bör installera först: [Azure File Sync agent installation](../articles/storage/files/storage-sync-files-deployment-guide.md).

Den senaste agenten ska användas och kan laddas ned från Microsoft Download Center: [Azure File Sync-agent](https://aka.ms/AFS/agent "Ladda ned Azure File Sync agent").

Efter en lyckad installation och Server registrering kan du kontrol lera att du har slutfört det här steget: navigera till resursens synkroniseringstjänst för synkroniseringstjänst i Azure Portal och följ sedan den vänstra menyn till "registrerade servrar". Du kommer att se din server som finns där.
