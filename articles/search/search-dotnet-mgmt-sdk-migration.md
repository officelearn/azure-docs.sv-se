---
title: Uppgradera till Azure Search .NET Management SDK version 2
titleSuffix: Azure Cognitive Search
description: Uppgradera till Azure Search .NET Management SDK version 2 från tidigare versioner. Lär dig vad som är nytt och vilka kod ändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847540"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uppgradera versioner av Azure Search .NET Management SDK

> [!Important]
> Det här innehållet är fortfarande under utveckling. Version 3,0 av Azure Search Management .NET SDK finns på NuGet. Vi arbetar med att uppdatera den här migreringsguiden för att förklara hur du uppgraderar till den nya versionen. 
>

Om du använder version 1.0.2 eller äldre av [Azure Search .net Management SDK](https://aka.ms/search-mgmt-sdk)hjälper den här artikeln dig att uppgradera ditt program till att använda version 2.

Version 2 av Azure Search .NET Management SDK innehåller vissa ändringar från tidigare versioner. Dessa är huvudsakligen mindre, så att ändringar i din kod bara kräver minimal ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar din kod till att använda den nya SDK-versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Vad är nytt i version 2
Version 2 av Azure Search .NET Management SDK riktar sig till samma allmänt tillgängliga version av Azure Search Management REST API som tidigare SDK-versioner, särskilt 2015-08-19. Ändringarna i SDK är strikta ändringar på klient sidan för att förbättra användbarheten hos själva SDK: n. Dessa ändringar omfattar följande:

* `Services.CreateOrUpdate`och dess asynkrona versioner avsöker nu automatiskt etableringen `SearchService` och returnerar inte förrän tjänst etableringen har slutförts. Detta gör att du slipper skriva en sådan avsöknings kod själv.
* Om du fortfarande vill avsöka tjänst etableringen manuellt kan du använda den nya `Services.BeginCreateOrUpdate` metoden eller en av dess asynkrona versioner.
* Nya metoder `Services.Update` och dess asynkrona versioner har lagts till i SDK: n. Dessa metoder använder HTTP-korrigering för att stödja stegvis uppdatering av en tjänst. Du kan till exempel skala en tjänst genom att skicka en `SearchService` instans till dessa metoder som bara innehåller de önskade `partitionCount` egenskaperna och `replicaCount` . Det gamla sättet att anropa `Services.Get`, ändra det returnerade `SearchService`och skicka det till `Services.CreateOrUpdate` stöds fortfarande, men det behövs inte längre. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera
Först uppdaterar du din NuGet-referens `Microsoft.Azure.Management.Search` för med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. Beroende på hur koden är strukturerad kan den återskapas. I så fall är det dags att sätta igång!

Om din version Miss lyckas kan det bero på att du har implementerat några av SDK-gränssnitten (till exempel för enhets testning) som har ändrats. För att lösa detta måste du implementera de nya metoderna, till exempel `BeginCreateOrUpdateWithHttpMessagesAsync`.

När du har åtgärdat eventuella build-fel kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK beskrivs i [Vad är nytt i version 2](#WhatsNew).

## <a name="next-steps"></a>Nästa steg
Vi välkomnar din feedback om SDK. Om du stöter på problem kan du skicka dina frågor till [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att märka din ärende rubrik med "[search]".
