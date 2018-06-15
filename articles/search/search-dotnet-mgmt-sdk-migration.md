---
title: Uppgradera till Azure Search .NET Management SDK version 2 | Microsoft Docs
description: Uppgradera till Azure Search .NET Management SDK version 2
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.openlocfilehash: a6b6c01f0cc811abca90139e4c26c27b7bd7119f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790373"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Uppgradera till Azure Search .NET Management SDK version 2
Om du använder version 1.0.2 eller senare av den [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk), den här artikeln hjälper dig att uppgradera ditt program att använda version 2.

Version 2 av Azure Search .NET Management SDK innehåller vissa ändringar från tidigare versioner. Detta är oftast mindre, så ändrar koden kräver endast minsta möjliga ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden att använda den nya SDK-versionen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Vad är nytt i version 2
Version 2 av Azure Search .NET Management SDK riktar sig till samma allmänt tillgänglig version av Azure Search Management REST API som tidigare versioner av SDK specifikt 2015-08-19. Ändringar till SDK är strikt klientsidan ändringar för att förbättra användningsmöjligheter SDK sig själv. De här förändringarna innefattar följande:

* `Services.CreateOrUpdate` och de asynkrona versionerna nu automatiskt efter etableringen `SearchService` och returnerar inte förrän tjänsten etableringen har slutförts. Du slipper skriva sådan kod avsökning själv.
* Om du vill söka service etablering manuellt kan du använda den nya `Services.BeginCreateOrUpdate` metod eller en av dess asynkrona versionerna.
* Nya metoder `Services.Update` och dess asynkrona versionerna har lagts till SDK. Dessa metoder använder HTTP-korrigering för att stödja stegvis uppdatering av en tjänst. Du kan till exempel nu skala en tjänst genom att skicka en `SearchService` instans till dessa metoder som innehåller endast önskade `partitionCount` och `replicaCount` egenskaper. Det gamla sättet att anropa `Services.Get`, ändra den returnerade `SearchService`, och skicka den till `Services.CreateOrUpdate` stöds fortfarande, men inte längre behövs. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Uppdatera först NuGet-referens för `Microsoft.Azure.Management.Search` NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

När NuGet har laddat ned nya paket och deras beroenden, återskapa projektet. Beroende på hur koden är strukturerad, kan den återskapa har. I så fall, är du redo att sätta igång!

Om din build misslyckas bero det på att du har implementerat vissa SDK-gränssnitt (till exempel för tillämpning av enhet testning), som har ändrats. För att lösa problemet måste du implementera de nya metoderna som `BeginCreateOrUpdateWithHttpMessagesAsync`.

När du har åtgärdat eventuella build-fel, kan du göra ändringar i programmet för att dra nytta av nya funktioner, om du vill. Nya funktioner i SDK beskrivs i [vad är nytt i version 2](#WhatsNew).

## <a name="conclusion"></a>Sammanfattning
Vi uppskattar din feedback om SDK. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Om du hittar ett programfel kan du filen ett problem i den [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixet rubriken problemet med ”[Azure Search]”.

Tack för att använda Azure Search!
