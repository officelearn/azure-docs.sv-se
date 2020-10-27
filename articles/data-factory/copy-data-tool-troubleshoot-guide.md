---
title: Felsöka Kopiera data-verktyget i Azure Data Factory
description: Lär dig hur du felsöker problem med Kopiera data verktyg i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550677"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Felsöka Kopiera data-verktyget i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs vanliga fel söknings metoder för Kopiera data-verktyget i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Felkod: det går inte att validera i Kopiera data-verktyget

- **Symptom** : du får varnings meddelandet "Det gick inte att verifiera" i det första steget i kopiera data-verktyget.
- **Orsaker** : Detta kan inträffa när alla tredjeparts cookies är inaktiverade.
- **Lösning** : 
    - Använd Internet Explorer eller Microsoft Edge-webbläsare.
    - Om du använder Chrome Browser följer du anvisningarna nedan om du vill lägga till cookies-undantag för *microsoftonline.com* och *Windows.net* .
        1.  Öppna Chrome-webbläsaren.
        2.  Klicka på SKIFT nyckel eller tre rader till höger (anpassa och kontrol lera Google Chrome).
        3.  Klicka på **Inställningar** .
        4.  Sök efter **cookies** eller gå till **Sekretess** under avancerade inställningar.
        5.  Välj **innehålls inställningar** .    
        6.  Cookies ska ställas in så att **lokala data kan anges (rekommenderas)** .
        7.  Klicka på **Hantera undantag** . Under **hostname-mönster** anger du följande och kontrollerar att **Tillåt** är inställd.
            - login.microsoftonline.com
            - login.windows.net
        8.  Stäng webbläsaren och starta om.
    - Om du använder Firefox webbläsare följer du anvisningarna nedan för att lägga till cookies-undantag.
        1. Från menyn Firefox går du till **verktyg**  >  **alternativ** .
        2. Under **Sekretess**  >  **historiken** kan du se att den aktuella inställningen **använder anpassade inställningar för historik** .
        3. I **acceptera cookies från tredje part** kan den aktuella inställningen bli **aldrig** . du bör sedan klicka på **undantag** till höger för att lägga till följande platser.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Stäng webbläsaren och starta om. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Felkod: det går inte att öppna inloggnings sidan och ange lösen ord

- **Symptom** : kopiera data verktyget omdirigerar dig till inloggnings sidan, men inloggnings sidan visas inte korrekt.
- **Orsaker** : det här problemet kan inträffa om du har ändrat nätverks miljön från kontors nät verket till hem nätverket. Det finns vissa cacheminnen i webbläsare. 
- **Lösning** : 
    1.  Stäng webbläsaren och försök igen. Gå till nästa steg om problemet fortfarande finns.   
    2.  Om du använder Internet Explorer-webbläsare försöker du öppna den i privat läge (tryck på "CTRL" + "Shift" + "P"). Om du använder Chrome-webbläsare försöker du öppna den i Incognito-läge (tryck på "CTRL" + "Shift" + "N"). Gå till nästa steg om problemet fortfarande finns. 
    3.  Försök att använda en annan webbläsare. 


## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:
*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestanda guide för ADF-mappning av data flöden](concepts-data-flow-performance.md)
