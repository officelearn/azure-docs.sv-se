---
title: Omvandlar och jobb i Azure Media Services | Microsoft Docs
description: När du använder Media Services, måste du skapa en transformering för att beskriva regler och specifikationer för bearbetning av dina videor. Den här artikeln ger en översikt över transformeringen är och hur du använder den.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb

## <a name="overview"></a>Översikt 

Den senaste versionen av Azure Media Services REST API (v3) introducerar en ny resurs mallbaserat arbetsflöde för kodning och/eller analysera videor som kallas en **transformera**. **Transformerar** kan användas för att konfigurera vanliga uppgifter för kodning eller analying videor. Varje **transformera** beskriver ett enkelt arbetsflöde för aktiviteter för bearbetning av video eller ljud filerna. 

Den **transformera** objektet är ritar och en **jobbet** är den faktiska begäranden till Azure Media Services ska användas som **transformera** till en viss inkommande video eller ljud innehåll. Den **jobbet** anger information som platsen för indata video och platsen för utdata. Du kan ange platsen för din video med: http (s)-URL: er, SAS-URL: er eller en sökväg till filer lokalt eller i Azure Blob storage. Du kan ha upp till 100 transformeringar i Azure Media Services-kontot och skicka jobb under dessa transformeringar. Du kan sedan prenumerera på händelser som jobbet status ändras, använder meddelanden som integreras direkt med Azure Event notification nätet. 

Eftersom detta API är drivs av Azure Resource Manager, kan du använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan också anges på nivån resurs i detta API så att du kan låsa åtkomst till specifika resurser som transformeringar.

## <a name="typical-workflow-and-example"></a>Vanligt arbetsflöde och exempel

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering om du inte planerar att använda ”recept” i framtiden. 

Anta att du vill extrahera den första rutan av dina videor som en miniatyrbild – är du kan vidta åtgärder: 

1. Definiera regeln för bearbetning – t.ex, använda den första bildrutan i videon som miniatyren 
2. Sedan för varje video som indata för tjänsten, anger du tjänsten: 
    1. Var du hittar den videon och 
    2. Var du vill skriva utdata – till exempel miniatyrbilden 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Stream-filer](stream-files-dotnet-quickstart.md)
