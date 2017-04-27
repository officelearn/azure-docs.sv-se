---
title: Vad finns i Azure RemoteApp-mallavbildningarna? | Microsoft Docs
description: "Läs mer om mallavbildningarna som ingår i Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7f8442b2-81da-421e-a453-aa53ba2066b7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 348306795fd3c8275b21e4ec6dceae408916bf72
ms.lasthandoff: 03/31/2017


---
# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Vad finns i Azure RemoteApp-mallavbildningarna?
> [!IMPORTANT]
> Azure RemoteApp upphör att gälla den 31 augusti 2017. Läs [meddelandet](https://go.microsoft.com/fwlink/?linkid=821148) för mer information.
> 
> 

Din Azure RemoteApp-prenumeration innehåller tre mallavbildningar:

* Windows Server 2012
* Microsoft Office 365 ProPlus (Office 365-prenumeration krävs)
* Microsoft Office 2013 Professional Plus (endast utvärderingsversion)

> [!IMPORTANT]
> Din Azure RemoteApp-prenumeration ger dig åtkomst till programvaran i avbildningarna, med undantag för Office 365 ProPlus, som kräver en separat prenumeration, och Office 2013, som inte kan användas i produktionen. Det innebär att du kan dela programmen eller apparna i mallavbildningarna med dina användare. Om du till exempel skapar en samling som använder Windows Server 2012 R2-avbildningen, kan du publicera System Center Endpoint Protection som användarna kan komma åt via RemoteApp.
> 
> Mer info finns i [RemoteApp-licensieringsuppgifterna](remoteapp-licensing.md). Mer info om Office-licensiering finns i artikeln om att [använda Office med Azure RemoteApp](remoteapp-o365.md).
> 
> 

I nedanstående avsnitt finns information om vad varje avbildning innehåller.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 (den vanliga avbildningen)
Den här avbildningen baseras på operativsystemet Microsoft Windows Server 2012 R2 Datacenter och har följande installerade roller och funktioner för att uppfylla kraven för Azure RemoteApp-mallavbildningar:

* .NET Framework 4.5, 3.5.1, 3.5
* Skrivbordsmiljö
* Handskriftstjänster
* Media Foundation
* Värd för fjärrskrivbordssession
* Windows PowerShell 4.0
* Windows PowerShell ISE
* WoW64-stöd

Den här avbildningen innehåller även följande program:

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Microsoft Windows Media Player

## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (prenumeration krävs)
Office 365 är det mest efterfrågade programmet så vi har skapat en ”anpassad” avbildning som du kan använda.

Den här avbildningen är en utökning av den vanliga avbildningen, och innehåller följande komponenter från Microsoft Office 365 ProPlus utöver de komponenter som beskrivs i Windows Server 2012 R2-avbildningen:

* Åtkomst
* Excel
* Lync
* OneNote
* OneDrive för företag (observera att synkroniseringsagenten inte stöds för användning med Azure RemoteApp)
* Outlook
* PowerPoint
* Word
* Språkverktyg för Microsoft Office

I avbildningen ingår även Visio Pro och Project Pro.

Samt följande program:

* SQL Native Client
* ODBC-drivrutin
* SQL Server Data Mining-klient
* MasterDataServices-klient
* Microsoft Publisher
* PowerQuery
* PowerMap

Alla funktioner i Office 365 ProPlus-apparna är bara tillgängliga för användare som har en Office 365 ProPlus-plan. Mer info om Office 365-prenumerationsavtalen finns i avsnittet om [Office 365-tjänstplaner](http://technet.microsoft.com/library/office-365-plan-options.aspx). Har du fler frågor? Läs informationen i [Office 365 + RemoteApp](remoteapp-o365.md). Det finns även information i den nya artikeln om att [använda Office 365-prenumerationen med Azure RemoteApp](remoteapp-officesubscription.md).

Observera att du måste licensiera Office 365 ProPlus, Visio Pro och Project Pro separat – alla har en egen licens.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (endast utvärderingsversion)
Du kan prova på tjänsten med Office 2013-avbildningen under den kostnadsfria utvärderingsperioden.

Den här avbildningen är en utökning av den vanliga avbildningen, och innehåller följande komponenter från Microsoft Office 2013 Professional Plus utöver de komponenter som beskrivs i Windows Server 2012 R2-avbildningen:

* Access
* Excel
* Lync
* OneNote
* OneDrive för företag (observera att synkroniseringsagenten inte stöds för användning med Azure RemoteApp)
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Språkverktyg för Microsoft Office

> [!IMPORTANT]
> **Juridisk information:** Den här avbildningen innehåller inte en licens för Microsoft Office och *kan inte användas för produktion*. Office 2013 Professional Plus-avbildningen är endast avsedd för utvärderingsanvändning. Om du vill använda Office-apparna i Azure RemoteApp för produktion, måste du använda Office 365 ProPlus-avbildningen. Mer info om Office-licensiering finns i artikeln om att [använda Office 365 med Azure RemoteApp](remoteapp-o365.md).
> 
> 


