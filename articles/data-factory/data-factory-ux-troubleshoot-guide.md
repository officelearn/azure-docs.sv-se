---
title: Felsöka Azure Data Factory UX
description: Lär dig hur du felsöker problem med Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87568011"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Felsöka problem med Azure Data Factory UX
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>ADF-UX läses inte in

> [!NOTE]
> Azure Data Factory UX har officiellt stöd för Microsoft Edge och Google Chrome. Att använda andra webbläsare kan leda till oväntad eller inte dokumenterad funktion.

### <a name="third-party-cookies-blocked"></a>Cookies från tredje part blockeras

ADF UX använder webbläsarens cookies för att spara användarsessionen och aktivera interaktiva utvecklings-och övervaknings upplevelser. Det är möjligt att webbläsaren blockerar cookies från tredje part eftersom du använder en Incognito-session eller har en ad-blockerare aktive rad. Om du blockerar cookies från tredje part kan det orsaka problem vid inläsning av portalen, till exempel att de ska omdirigeras till en tom sida https://adf.azure.com/accesstoken.html eller få ett varnings meddelande om att cookies från tredje part blockeras. Lös problemet genom att aktivera cookies-alternativ från tredje part i webbläsaren med följande steg:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Tillåt alla cookies

1. Besök **Chrome://settings/cookies** i webbläsaren.
1. Välj alternativet **Tillåt alla cookies** ![ , Chrome-Allow-all-cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Uppdatera ADF UX och försök igen.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Tillåt endast att ADF-UX använder cookies
Om du inte vill tillåta alla cookies kan du välja att bara tillåta ADF-UX:
1. Besök **Chrome://settings/cookies**.
1. Klicka på **Lägg till** under **platser som alltid kan använda cookies** -alternativet ![ Lägg till ADF UX till tillåtna platser](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Lägg till **ADF.Azure.com** -plats, markera alternativet **alla cookies** och spara. ![Tillåt alla cookies från ADF UX-webbplatsen](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Uppdatera ADF UX och försök igen.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Besök **Edge://Settings/Content/cookies** i webbläsaren.
1. Se till att **platser för att spara och läsa cookie-data** är aktiverade och att alternativet **blockera cookies från tredje part** är inaktiverat ![ Tillåt alla cookies i Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Uppdatera ADF UX och försök igen.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Tillåt endast att ADF-UX använder cookies

Om du inte vill tillåta alla cookies kan du välja att bara tillåta ADF-UX:

1. Besök **Edge://Settings/Content/cookies**.
1. Under avsnittet **Tillåt** klickar du på **Lägg till** och Lägg till **ADF.Azure.com** -plats. ![Lägg till ADF-UX till tillåtna platser](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Uppdatera ADF UX och försök igen.

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

* [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videor](https://azure.microsoft.com/resources/videos/index/)
* [Sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
