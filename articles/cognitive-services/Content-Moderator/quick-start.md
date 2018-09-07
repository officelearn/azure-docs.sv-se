---
title: Azure Content Moderator Kom igång | Microsoft Docs
description: Hur du kommer igång med Azure Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: 39727b4d97ade67b854fe525afad565451cc3d77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024519"
---
# <a name="get-started-with-content-moderator"></a>Kom igång med Content Moderator

Du Kom igång med Content Moderator på följande sätt:

- [Börja med granskningsverktyget](#start-with-the-review-tool) att hämta API-nyckeln och skapa ett granskningsteam. Fördelen är att du kan använda API-nyckel för att anropa API: er för moderering för att söka efter innehåll och granska API: er för att generera granskningar utan ytterligare steg.
- [Prenumerera på Content Moderator](#start-with-the-apis) i Azure för att hämta API-nyckel. Kolla in den [API-referens](api-reference.md) och [SDK: er](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Du måste registrera dig online för att skapa ett granskningsteam.
- [Använda Flow-anslutningar och mallar för](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/) att checka ut en rad olika integreringar med en enkel att använda designer.

Oavsett vilket alternativ som väljs, se den [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md) artikeln för att hitta dina API-autentiseringsuppgifter.

## <a name="start-with-the-review-tool"></a>Börja med granskningsverktyget
[Registrera dig](http://contentmoderator.cognitive.microsoft.com/) på webbplatsen för Content Moderator granska verktyget.

![Startsida för innehåll Moderator](images/homepage.PNG)

### <a name="create-a-review-team"></a>Skapa ett granskningsteam
Namnge ditt team. Om du vill bjuda in dina kollegor kan du göra det genom att ange deras e-postadresser.

![Bjud in gruppmedlem](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>Ladda upp bilder eller ange text
Klicka på **försök > bild** eller **försök > Text**. Överföra upp till fem Exempelbilder eller ange exempeltext för moderering.

![Testa avbildningen eller Textmoderering](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>Skicka in för automatiserad moderering
Skicka in ditt innehåll för automatiserad moderering. Internt, anropar granskningsverktyget moderering API: er så att ditt innehåll. När den skanningen är klar, visas ett meddelande som informerar dig om väntar på att granska resultaten.

![Måttlig filer](images/submitted.png)

### <a name="review-and-confirm-results"></a>Granska och bekräfta resultat
Granska automatisk modereras-taggar, ändra om det behövs och skicka med hjälp av den **nästa** knappen. Som ett affärsprogram anrop Moderator API: er, den taggade innehåll startar köa, är redo för att granskas av mänsklig granskning-team. Du kan snabbt granska stora mängder innehåll med hjälp av den här metoden.

![Granska resultatet](images/reviewresults.png)

Lär dig att använda alla de [granskar verktygets funktionerna](Review-Tool-User-Guide/human-in-the-loop.md) eller fortsätta med nästa avsnitt för att lära dig om de API: er. Hoppa över registrering steget eftersom du har etablerat för dig i granskningsverktyget som visas i API-nyckeln i [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md) artikeln.

### <a name="use-the-apis"></a>API: er i

Lär dig hur du integrerar Content Moderator med dina affärsprogram. Kolla in den [API-referens](api-reference.md) och [SDK: er](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net).

## <a name="subscribe-in-the-azure-portal"></a>Prenumerera på Azure-portalen

[Prenumerera på Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) i Azure-portalen. Börja med någon av följande API: er:

### <a name="image-moderation"></a>Bildmoderering

Börja med den [API-konsol](try-image-api.md) eller Använd den [Snabbstart för .NET](image-moderation-quickstart-dotnet.md) Genomsök bilder och identifiera potentiellt vuxet eller olämpligt innehåll med hjälp av taggar, förtroende poäng och andra extraherade information.

### <a name="text-moderation"></a>Textmoderering

Börja med den [API-konsol](try-text-api.md) eller Använd den [Snabbstart för .NET](text-moderation-quickstart-dotnet.md) söker igenom textinnehåll för potentiella svordomar, datorstödd oönskad text klassificering (förhandsversion) och personligt identifierbar information (PII). 


### <a name="video-moderation"></a>Videomoderering

Börja med den [Snabbstart för .NET](video-moderation-api.md) att söka igenom videor och identifiera potentiellt vuxet eller olämpligt innehåll. 


### <a name="review-apis"></a>Omdömes-API:er

Börja här genom att välja från jobbet, granskning och arbetsflöde för API: er.

- Den [jobbet API](try-review-api-job.md) söker igenom ditt innehåll med hjälp av API: er för moderering och genererar granskningar i granskningsverktyget. 
- Den [granska API](try-review-api-review.md) direkt skapar bild, text eller video granskningar för mänskliga moderatorer utan att söka igenom innehållet. 
- Den [arbetsflöde API](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbetsflöden som ditt team skapar.

## <a name="next-steps"></a>Nästa steg

Kolla in den [API-referens](api-reference.md) och [SDK: er](sdk-and-samples.md#sdks-for-python-java-nodejs-and-net). Kom igång snabbt med din integrering med den [.NET SDK-prov](sdk-and-samples.md#net-sdk-samples), [REST API-exempel i C#](https://github.com/sanjeev3/azure-docs-pr/blob/master/articles/cognitive-services/Content-Moderator/sdk-and-samples.md#rest-api-samples-in-c) och [självstudier](sdk-and-samples.md#tutorials).
