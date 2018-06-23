---
title: Azure innehåll kontrollant komma igång | Microsoft Docs
description: Hur du kommer igång med Azure innehåll kontrollanten.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352566"
---
# <a name="get-started-with-content-moderator"></a>Kom igång med Content Moderator

Du komma igång med API: er med innehåll kontrollant och granska verktyget på följande sätt:

- [Börja med verktyget granska](#start-with-the-review-tool) både skapa API-nycklar och granska-teamet. Utforska verktyget granskning och hur du integrerar med hjälp av API: er för innehåll kontrollanten.
- [Prenumerera på innehåll kontrollant](#start-with-the-apis) i Azure-portalen. Du måste registrera dig online för att skapa ett team för granskning.
- [Använd flödet koppling och mallar](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) att checka ut en mängd olika integration med ett enkelt att använda designer.

Oavsett vilket alternativ du väljer, finns det [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md) artikeln för att hitta dina API-autentiseringsuppgifter.

## <a name="start-with-the-review-tool"></a>Börja med verktyget granskning
[Registrera dig](http://contentmoderator.cognitive.microsoft.com/) på webbplatsen innehåll kontrollant granska verktyget.

![Innehåll kontrollant startsidan](images/homepage.PNG)

### <a name="create-a-review-team"></a>Skapa ett team för granskning
Namnge ditt team. Om du vill bjuda in dina kollegor kan du göra det genom att ange sina e-postadresser.

![Bjud in teammedlem](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Ladda upp bilder eller ange text
Klicka på **försök > bild** eller **försök > Text**. Överför upp till fem exempel bilder eller ange exempeltext för gruppering.

![Försök bild- eller Text Avbrottsmoderering](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Skicka för automatisk avbrottsmoderering
Skicka innehållet för automatisk måtta. Internt, anropar verktyget granska avbrottsmoderering API: er för att söka igenom ditt innehåll. När skanning har slutförts visas ett meddelande som talar om väntar på att granska resultaten.

![Måttlig filer](images/submitted.png)

### <a name="review-and-confirm-results"></a>Granska och bekräfta resultat
Granska automatiskt kontrollerad taggar, ändra vid behov och skicka med hjälp av den **nästa** knappen. Som ett affärsprogram anropar kontrollant API: er, de kodade innehåll startar köa, är redo för att granskas av mänsklig granska team. Du kan snabbt granska stora mängder innehåll med hjälp av den här metoden.

![Granska resultatet](images/reviewresults.png)

Lär dig att använda alla de [granska verktygets funktioner](Review-Tool-User-Guide/human-in-the-loop.md) eller Fortsätt med nästa avsnitt för att lära dig om de API: er. Hoppa över registrering steget eftersom du har API-nyckeln som etablerats för dig i verktyget granska enligt den [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md) artikel.

### <a name="use-the-apis"></a>API: er i

Nu när du har arbetat innehåll avbrottsmoderering och granska verktyget lär du dig hur du integrerar innehåll kontrollant med dina affärsprogram. Använd följande avsnitt om du vill veta mer och snabb åtkomst till din förståelse med SDK: er och exempel.

## <a name="start-with-the-apis"></a>Börja med de API: er

[Prenumerera på innehåll kontrollant](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) i Azure-portalen. Börja med en av följande API: er:

### <a name="image-moderation"></a>Bildmoderering

Börja med den [API konsolen](try-image-api.md) eller använda den [.NET quickstart](image-moderation-quickstart-dotnet.md) skanna bilder och identifiera potentiella innehåll för vuxna och dyr med hjälp av taggar, förtroende poäng och andra extraherade information.

### <a name="text-moderation"></a>Textmoderering

Börja med den [API-konsolen](try-text-api.md) eller använda den [.NET quickstart](text-moderation-quickstart-dotnet.md) skanna textinnehåll potentiella bort olämpligt material, dator-stödd oönskad text klassificering (förhandsversion) och personligt identifierbar information (PII). 


### <a name="video-moderation"></a>Videomoderering

Börja med den [.NET quickstart](video-moderation-api.md) att skanna videor och identifiera potentiella innehåll för vuxna och dyr. 


### <a name="review-apis"></a>Omdömes-API:er

Börja här genom att välja från jobbet, granska och API: er för arbetsflödet.

- Den [jobbet API](try-review-api-job.md) genomsöker ditt innehåll med hjälp av avbrottsmoderering API: er och genererar granskningar i verktyget granska. 
- Den [granska API](try-review-api-review.md) direkt skapar image, text eller video omdömen för mänsklig moderatorer utan att söka igenom innehållet. 
- Den [arbetsflöde API](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbetsflöden som ni skapar.

## <a name="next-steps"></a>Nästa steg

Mer information om innehåll avbrottsmoderering från och med den [bild avbrottsmoderering API](image-moderation-api.md).
