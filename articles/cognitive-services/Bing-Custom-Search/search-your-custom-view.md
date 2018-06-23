---
title: 'Bing anpassad sökning: Söka efter en anpassad vy | Microsoft Docs'
description: Beskriver hur du söker en anpassad vy av
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352851"
---
# <a name="call-your-custom-search"></a>Anropa sökningen anpassad
Du måste hämta en nyckel för prenumerationen kognitiva Services innan du gör din första anropa API: et för anpassad sökning för att få sökresultat för din instans. Om du vill få en nyckel för anpassad sökning API finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).

> [!NOTE]
> Befintliga Bing anpassad sökning-kunder som har en preview-nyckel som etablerats på eller före den 15 oktober 2017 kommer att kunna använda sina nycklar tills November 30 2017 eller tills de har förbrukat det maximala antalet frågor tillåts. Därefter kan behöver de för att migrera till den allmänt tillgängliga versionen på Azure.

## <a name="try-it-out"></a>Prova
När du har konfigurerat din anpassade sökinställningar kan testa du konfigurationen från anpassad sökning-portalen. Logga in på [anpassad sökning](https://customsearch.ai), klicka på en anpassad sökning-instans och på den **produktion** fliken. Den **slutpunkter** visas. Prenumerationen avgör vilka slutpunkter som är tillgängliga för försök, se [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/). Välj i listrutan och ange tillhörande konfigurationsalternativ för att testa en slutpunkt. 

Följande är de tillgängliga alternativen.

- **Frågan**: söktermen att söka efter. Endast tillgängligt för webb-, Image- och automatiska förslag slutpunkter.
- **ID för konfiguration av anpassade**: konfigurations-ID i den valda instansen anpassad sökning. Det här fältet är skrivskyddat.
- **Marknaden**: marknaden var resultatet ska hämtas. Endast tillgängligt för webb-, Image- och värdbaserade UI-slutpunkter.
- **Prenumerationen nyckeln**: nyckeln prenumeration för att testa med. Du kan välja en nyckel i listrutan eller ange en manuellt.
- **Säker sökning**: ett filter som används för att filtrera webbsidor för vuxet innehåll. Endast tillgängligt för webb-, Image- och värdbaserade UI-slutpunkter.
- **Antal**: antalet sökresultat att returnera i svaret. Endast tillgängligt för webb- och slutpunkter.
- **Förskjutning**: antalet sökresultat att returnera i svaret. Endast tillgängligt för webb- och slutpunkter.

När du har angett alla obligatoriska alternativ för webb-, Image- eller automatiska förslag klickar du på **anropa** Se JSON-svar i den högra rutan. 

Om du väljer finns UI-slutpunkten måste testa du sökinställningar från den högra rutan.

## <a name="next-steps"></a>Nästa steg
- [Anropa den anpassade vyn med C#](./call-endpoint-csharp.md)
- [Anropa den anpassade vyn med Java](./call-endpoint-java.md)
- [Anropa den anpassade vyn med NodeJs](./call-endpoint-nodejs.md)
- [Anropa den anpassade vyn med Python](./call-endpoint-python.md)