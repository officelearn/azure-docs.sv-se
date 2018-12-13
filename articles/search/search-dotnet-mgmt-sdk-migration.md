---
title: Uppgradera till Azure Search .NET Management SDK version 2 – Azure Search
description: Uppgradera till Azure Search .NET Management SDK version 2 från tidigare versioner. Läs mer om nyheterna och vilka kodändringar krävs.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316884"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Uppgradera till Azure Search .NET Management SDK version 2
Om du använder version 1.0.2 eller senare av den [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk), den här artikeln beskriver hur du uppgraderar program du använder version 2.

Version 2 av Azure Search .NET Management SDK innehåller vissa ändringar från tidigare versioner. Det här är främst mindre, så ändra din kod kräver bara minimal ansträngning. Se [stegen för att uppgradera](#UpgradeSteps) anvisningar om hur du ändrar din kod till den nya versionen av SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Vad är nytt i version 2
Version 2 av Azure Search .NET Management SDK riktar sig mot samma allmänt tillgänglig version av Azure Search Management REST API som tidigare versioner av SDK specifikt 2015-08-19. Ändringarna i SDK: N är strikt klientsidan ändringar för att förbättra användbarheten av SDK: N själva. De här förändringarna innefattar följande:

* `Services.CreateOrUpdate` och de asynkrona versionerna nu automatiskt efter etableringen `SearchService` och returnerar inte förrän tjänsten etableringen har slutförts. Detta gör att du inte behöver skriva sådan avsökningen kod själv.
* Om du fortfarande vill avsöker tjänst etablering manuellt kan du använda den nya `Services.BeginCreateOrUpdate` metoden eller någon av dess asynkrona versioner.
* Nya metoder `Services.Update` och dess asynkrona versioner har lagts till i SDK. Dessa metoder använder HTTP-PATCH för att stödja inkrementell uppdatering av en tjänst. Exempelvis kan du nu kan du skala en tjänst genom att skicka en `SearchService` instans till dessa metoder som innehåller bara önskade `partitionCount` och `replicaCount` egenskaper. Det gamla sättet att anropa `Services.Get`, ändra den returnerade `SearchService`, och skicka den till `Services.CreateOrUpdate` stöds fortfarande, men inte längre behövs. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdatera dina NuGet-referens för `Microsoft.Azure.Management.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

Återskapa ditt projekt när NuGet har laddats ned nya paket och deras beroenden. Beroende på hur din kod är strukturerade, kan den återskapa har. I så, fall är du redo att sätta igång!

Om din version misslyckas kan bero det på eftersom du har implementerat vissa SDK-gränssnitt (till exempel för betecknas Enhetstestning) som har ändrats. För att lösa problemet måste du implementera de nya metoderna som `BeginCreateOrUpdateWithHttpMessagesAsync`.

När du har åtgärdat eventuella build-fel, kan du gör ändringar i ditt program för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK finns beskrivna i [vad är nytt i version 2](#WhatsNew).

## <a name="conclusion"></a>Sammanfattning
Vi uppskattar din feedback om SDK. Om du får problem kan passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Om du har hittat ett programfel kan du rapportera problemet i den [Azure .NET SDK på GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att din Ärenderubrik med ”[Azure Search]”-prefix.

Tack för att använda Azure Search!
