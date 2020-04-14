---
title: 'Snabbstart: Använd cURL & REST för att hantera kunskapsbas - QnA Maker'
description: Den här snabbstarten visar hur du skapar, publicerar och frågar kunskapsbasen med hjälp av REST-API:erna.
ms.date: 04/13/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: facc45ab8f916181f7eeceb65c5102a60ae7d7e9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261711"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Snabbstart: Använd cURL och REST för att hantera kunskapsbas

Den här snabbstarten hjälper dig att skapa, publicera och fråga din kunskapsbas. QnA Maker extraherar automatiskt frågor och svar för delvis strukturerat innehåll, som vanliga frågor från [datakällor](../Concepts/knowledge-base.md). Modellen för kunskapsbasen har definierats i JSON som skickas i brödtexten i API-begäran.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Krav

* Den aktuella versionen av [cURL](https://curl.haxx.se/). Flera kommandoradsväxlar används i snabbstarterna, som noteras i [cURL-dokumentationen](https://curl.haxx.se/docs/manpage.html).
* Du måste ha en [QnA Maker-resurs](../How-To/set-up-qnamaker-service-azure.md)för att kunna använda nyckeln och resursnamnet. Du angav **resursnamnet** när du skapade resursen och sedan skapades nyckeln åt dig. Resursnamnet används som underdomän för slutpunkten. Om du vill hämta nyckel- och resursnamnet väljer du **Snabbstart** för din resurs i Azure-portalen. Resursnamnet är den första underdomänen i slutpunkts-URL:en:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Följande BASH-exempel `\` använder linjefortsättningstecknet. Om du konsolen eller terminalen använder ett annat linjefortsättningstecken använder du det här tecknet.

## <a name="create-a-knowledge-base"></a>Skapa en kunskapsbas

Om du vill skapa en kunskapsbas med REST-API:erna och cURL måste du ha följande information:

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|QnA Maker-resursnyckel|`-h`param `Ocp-Apim-Subscription-Key` för rubrik|Autentisera till QnA Maker-tjänsten|
|JSON som beskriver kunskapsbas|`-d`Param|[Exempel på](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) JSON|
|Storleken på JSON i byte|`-h`param `Content-Size` för rubrik||

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med egna resursnamn, resursnyckel och JSON-värden och storleken på JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

CURL-svaret från QnA `operationId` Maker innehåller , som krävs för att [få status för åtgärden](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>Hämta status för åtgärden

När du skapar en kunskapsbas, eftersom åtgärden är asynkron, innehåller svaret information för att fastställa statusen.

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|Åtgärd ID|URL-väg|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker-resursnyckel|`-h`param `Ocp-Apim-Subscription-Key` för rubrik|Autentisera till QnA Maker-tjänsten|

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med ditt eget resursnamn, resursnyckel och åtgärds-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

CURL-svaret innehåller statusen. Om åtgärdstillståndet lyckas `resourceLocation` innehåller kunskapsbas-ID:t.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Publicera kunskapsbas

Innan du frågar kunskapsbasen måste du:
* Publicera kunskapsbas
* Hämta slutpunktsnyckeln för körning

Den här uppgiften publicerar kunskapsbasen. Att hämta slutpunktsnyckeln för körning är en [separat uppgift](#get-published-knowledge-bases-runtime-endpoint-key).

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|QnA Maker-resursnyckel|`-h`param `Ocp-Apim-Subscription-Key` för rubrik|Autentisera till QnA Maker-tjänsten|
|Kunskapsbas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med ditt eget resursnamn, resursnyckel och kunskapsbas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

Svarsstatusen är 204 utan resultat. Använd `-v` kommandoradsparametern för att se utförliga utdata för kommandot cURL. Detta inkluderar HTTP-status.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Få publicerad kunskapsbasens slutpunktsnyckel för körning

Innan du frågar kunskapsbasen måste du:
* Publicera kunskapsbas
* Hämta slutpunktsnyckeln för körning

Den här aktiviteten hämtar slutpunktsnyckeln för körning. Att publicera kunskapsbasen är en [separat uppgift](#publish-knowledge-base).

Slutpunktsnyckeln för körning är samma nyckel för alla kunskapsbaser som använder QnA Maker-resursen.

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|QnA Maker-resursnyckel|`-h`param `Ocp-Apim-Subscription-Key` för rubrik|Autentisera till QnA Maker-tjänsten|

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med ditt eget resursnamn, resursnyckel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


CURL-svaret innehåller slutpunktsnycklarna för körning. Använd bara en av nycklarna när du frågar för att få ett svar från kunskapsbasen.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Fråga efter svar från publicerad kunskapsbas

Att få ett svar från kunskapen görs från en separat körning än att hantera kunskapsbasen. Eftersom det är en separat körning måste du autentisera med en körningsnyckel.

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|QnA Maker runtime-nyckel|`-h`param `Authorization` för rubrik|Nyckeln är en del av en `Endpointkey `sträng som innehåller ordet . Autentisera till QnA Maker-tjänsten|
|Kunskapsbas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON som beskriver fråga|`-d`Param|[Begär kroppsparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) och [exempel på](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) JSON|
|Storleken på JSON i byte|`-h`param `Content-Size` för rubrik||

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med ditt eget resursnamn, resursnyckel och kunskapsbas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Ett lyckat svar innehåller det översta svaret tillsammans med annan information som ett klientprogram, till exempel en chattrobot, måste visa ett svar för användaren.

## <a name="delete-knowledge-base"></a>Ta bort kunskapsbas

När du är klar med kunskapsbasen tar du bort den.

|Information|cURL-konfiguration|Syfte|
|--|--|--|
|Resursnamn för QnA Maker|URL|används för att konstruera URL|
|QnA Maker-resursnyckel|`-h`param `Ocp-Apim-Subscription-Key` för rubrik|Autentisera till QnA Maker-tjänsten|
|Kunskapsbas-ID|URL-väg|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

Kommandot cURL körs från ett BASH-skal. Redigera det här kommandot med ditt eget resursnamn, resursnyckel och kunskapsbas-ID.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

Svarsstatusen är 204 utan resultat. Använd `-v` kommandoradsparametern för att se utförliga utdata för kommandot cURL. Detta inkluderar HTTP-status.

## <a name="additional-resources"></a>Ytterligare resurser

* [Författande](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referensdokumentation
* [Körtid](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referensdokumentation
* [Exempel på BASH-skript med cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
