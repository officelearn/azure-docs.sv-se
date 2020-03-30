---
title: Distribuera Azure File Sync-agenten
description: Distribuera Azure File Sync-agenten. Ett vanligt textblock som delas mellan migreringsdokument.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209420"
---
I det här avsnittet installerar du Azure File Sync-agenten på din Windows Server.
[Distributionsguiden](../articles/storage/files/storage-sync-files-deployment-guide.md) visar att du måste inaktivera **IE:s förbättrade säkerhet**. Förbättrad säkerhet i IE är en säkerhetsåtgärd som inte är tillämplig med Azure File Sync och stänga av den gör att du kan autentisera till Azure utan problem.

Öppna PowerShell och installera de PowerShell-moduler som krävs med följande kommandon. Se till att installera hela modulen och NuGet-providern när du uppmanas:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Om du har några problem att nå internet från din server, nu är det dags att lösa dem. Azure File Sync använder alla tillgängliga nätverksanslutning till Internet.
Det stöds också att en proxyserver når internet. Du kan antingen konfigurera en datoromfattande proxy nu, eller ange en proxy som bara filsynkronisering ska användas under agentinstallationen.

Om det innebär att du behöver öppna brandväggarna för den här servern kan det vara en godtagbar metod för dig. I slutet av serverinstallationen, efter slutförd serverregistrering, kommer det att finnas en nätverksanslutningsrapport som visar de exakta slutpunkts-URL:erna i Azure, som filsynkronisering måste kommunicera med för den region som du har valt. I betänkandet berättas också varför kommunikation behövs. Du kan använda rapporten för att sedan låsa brandväggarna runt den här servern till specifika webbadresser.

Du kan också följa en mer konservativ metod, där du inte öppnar brandväggarna brett, utan i stället begränsar servern för att kommunicera till DNS-namnutrymmen på högre nivå – det finns mer dokumentation och information tillgänglig i artikeln [Azure File Sync proxy och brandväggsinställningar.](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) Följ dina egna metodtips för nätverk.

I slutet av guiden för *serverinstallation* visas en *serverregistreringsguide.*
Registrera servern till Azure-resursen för Lagringssynkroniseringstjänsten från tidigare.

De här stegen beskrivs mer i detalj i distributionsguiden, inklusive ovanstående PowerShell-moduler som du bör installera först: [Azure File Sync agent install](../articles/storage/files/storage-sync-files-deployment-guide.md).

Den senaste agenten ska användas och kan hämtas från Microsoft Download Center: [Azure File Sync - agent](https://aka.ms/AFS/agent "Hämtning av Azure File Sync-agent").

Efter en lyckad installation och serverregistrering kan du kontrollera att du har slutfört det här steget: Navigera till storage sync-tjänsten resurs i Azure-portalen och följ sedan vänstermenyn till "Registrerade servrar". Du kommer att se din server listad där direkt.
