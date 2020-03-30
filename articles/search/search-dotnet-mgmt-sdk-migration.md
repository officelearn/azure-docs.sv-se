---
title: Uppgradera till Azure Search .NET Management SDK version 2
titleSuffix: Azure Cognitive Search
description: Uppgradera till Azure Search .NET Management SDK version 2 från tidigare versioner. Läs om nyheter och vilka kodändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847540"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uppgradera versioner av Azure Search .NET Management SDK

> [!Important]
> Detta innehåll är fortfarande under uppbyggnad. Version 3.0 av Azure Search Management .NET SDK är tillgänglig på NuGet. Vi arbetar med att uppdatera den här migreringsguiden för att förklara hur du uppgraderar till den nya versionen. 
>

Om du använder version 1.0.2 eller äldre av [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)hjälper den här artikeln dig att uppgradera ditt program för att använda version 2.

Version 2 av Azure Search .NET Management SDK innehåller vissa ändringar från tidigare versioner. Dessa är oftast mindre, så ändra din kod bör kräva endast minimal ansträngning. Se [Steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden för att använda den nya SDK-versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Nyheter i version 2
Version 2 av Azure Search .NET Management SDK är inriktad på samma allmänt tillgängliga version av AZURE Search Management REST API som tidigare SDK-versioner, särskilt 2015-08-19. Ändringarna i SDK är strikt ändringar på klientsidan för att förbättra användbarheten av SDK själv. Dessa ändringar omfattar följande:

* `Services.CreateOrUpdate`och dess asynkrona versioner avsöker `SearchService` nu automatiskt etableringen och returneras inte förrän etableringen av tjänsten är klar. Detta sparar dig från att behöva skriva en sådan avsökningskod själv.
* Om du fortfarande vill avsöka tjänstetablering `Services.BeginCreateOrUpdate` manuellt kan du använda den nya metoden eller en av dess asynkrona versioner.
* Nya `Services.Update` metoder och dess asynkrona versioner har lagts till i SDK. Dessa metoder använder HTTP PATCH för att stödja inkrementell uppdatering av en tjänst. Du kan till exempel skala en `SearchService` tjänst genom att skicka en `partitionCount` instans `replicaCount` till dessa metoder som bara innehåller önskade och egenskaper. Det gamla sättet `Services.Get`att ringa, `SearchService`ändra den returnerade och skicka den till `Services.CreateOrUpdate` stöds fortfarande, men är inte längre nödvändigt. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Uppdatera först din NuGet-referens för `Microsoft.Azure.Management.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. Om så är fallet, är du redo att gå!

Om din version misslyckas kan det bero på att du har implementerat några av SDK-gränssnitten (till exempel för enhetstestning), som har ändrats. För att lösa detta måste du implementera de `BeginCreateOrUpdateWithHttpMessagesAsync`nya metoderna, till exempel .

När du har åtgärdat eventuella byggfel kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK [beskrivs i Nyheter i version 2.](#WhatsNew)

## <a name="next-steps"></a>Nästa steg
Vi välkomnar din feedback på SDK. Om du stöter på problem, skicka dina frågor till [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Om du hittar ett fel kan du lämna in ett problem i [Azure .NET SDK GitHub-databasen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att märka problemets titel med "[sök]".
