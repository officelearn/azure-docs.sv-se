---
title: Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure-portalen | Microsoft Docs
description: Lär dig hur du använder Azure-portalen för att konfigurera en Programgateway i Azure för att skriva om HTTP-huvuden i begäranden och svar som passerar genom gatewayen
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 9d52114f5d01beca53ed48ee2114dc98cc20f3a2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682432"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure-portalen

Den här artikeln visar hur du använder Azure-portalen för att konfigurera en [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) skriva om HTTP-huvuden i begäranden och svar.

> [!IMPORTANT]
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en Application Gateway-v2 SKU eftersom möjlighet till omskrivning av rubriken inte stöds för v1-SKU. Om du inte har v2-SKU, skapar du en [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) innan du börjar.

## <a name="what-is-required-to-rewrite-a-header"></a>Vad som krävs för att skriva om en rubrik

För att konfigurera HTTP-huvud omskrivning, måste du:

1. Skapa nya objekt som krävs för att skriva om http-huvuden:

   - **Skriv om åtgärden**: används för att ange begäran och begäran huvudfält som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till. Du kan välja att associera en eller flera omskrivning villkor med en Skriv om åtgärden.

   - **Skriv om villkoret**: Det är en valfri konfiguration. Om ett omskrivning villkor läggs utvärderar det innehållet i HTTP (S)-begäranden och svar. Beslutet att köra åtgärden Skriv om som är associerade med villkoret omskrivning baseras om HTTP (S)-begäran eller svar som matchade med Skriv om villkoret. 

     Om flera villkor som är associerade med en åtgärd och sedan åtgärden körs endast när alla villkor är uppfyllda, d.v.s., ett logiskt AND-åtgärd utförs.

   - **Skriva om regeln**: omskrivningsregel innehåller flera omskrivning åtgärd – Skriv om villkoret kombinationer.

   - **Regeln sekvens**: hjälper till att avgöra den ordning som de olika omarbetning regler körs. Det här är användbart när det finns flera omskrivningsregler i en omarbetning. Omskrivningsregel med mindre regeln Sekvensvärde hämtar köras först. Om du anger samma regelsekvens till två omskrivningsregler kommer ordningen för körningen att icke-deterministisk.

   - **Skriv om Set**: innehåller flera omskrivningsregler som ska kopplas till en regel för vidarebefordran av begäran.

2. Du kommer att behöva koppla omskrivning med en regel för vidarebefordran. Det beror på att Skriv om konfigurationen är kopplad till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När en sökvägsbaserad regel används har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. Därför gäller detta bara till området angiven sökväg för en plats.

Du kan skapa flera http-huvud omskrivning uppsättningar och varje uppsättning omskrivning kan tillämpas på flera lyssnare. Du kan dock använda endast en omarbetning inställd på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure-konto.

## <a name="configure-header-rewrite"></a>Konfigurera huvud-omskrivning

I det här exemplet ska vi ändra omdirigerings-URL genom att skriva om location-huvudet i http-svar som skickas av backend-applikationer. 

1. Välj **alla resurser**, och välj sedan din application gateway.

2. Välj **returpaket** menyn till vänster.

3. Klicka på **+ omarbetning set**. 

   ![Lägg till uppsättning av omarbetning](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Ange namn till omskrivning uppsättningen och koppla den till en regel för vidarebefordran:

   - Ange namnet på omarbetning som angetts i den **namn** textrutan.
   - Välj en eller flera regler som anges i den **associerade routningsregler** lista. Du kan bara välja de regler som inte är associerade med andra omskrivning uppsättningar. Vilka regler som redan är associerade med andra omskrivning uppsättningar är nedtonade.
   - Klicka på Nästa.
   
     ![Lägg till namn och association](media/rewrite-http-headers-portal/name-and-association.png)

5. Skapa en omskrivningsregel:

   - Klicka på **+ Lägg till omskrivningsregel**.![ Lägg till omskrivningsregel](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Ange ett namn som omskrivningsregel i textrutan för Skriv om regeln och ange en regelsekvens.![Lägg till namn](media/rewrite-http-headers-portal/rule-name.png)

6. I det här exemplet ska vi skriva om location-huvudet endast när den innehåller en referens till ”azurewebsites.net”. Lägg till ett villkor att utvärdera om location-huvudet i svaret innehåller azurewebsites.net för att göra detta:

   - Klicka på **+ Lägg till villkor** och klicka sedan på avsnittet som innehåller den **om** anvisningar för att expandera den.![ Lägg till namn](media/rewrite-http-headers-portal/add-condition.png)

   - Välj **HTTP-huvud** från den **typen av variabel att kontrollera** listrutan. 

   - Välj **Rubriktyp** som **svar**.

   - Eftersom det här exemplet vi utvärderar location-huvudet som råkar vara en gemensam rubrik väljer **gemensam rubrik** alternativknappen som den **rubriknamn**.

   - Välj **plats** från den **gemensam rubrik** listrutan.

   - Välj **nr** som den **skiftlägeskänsliga** inställningen.

   - Välj **är lika med (=)** från den **operatorn** listrutan.

   - Ange mönster för reguljärt uttryck. I det här exemplet använder vi mönstret `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Klicka på **OK**.

     ![Ändra location-huvudet](media/rewrite-http-headers-portal/condition.png)

7. Lägg till en åtgärd för att skriva om location-huvudet:

   - Välj **ange** som den **åtgärdstyp**.

   - Välj **svar** som den **Rubriktyp**.

   - Välj **gemensam rubrik** som den **rubriknamn**.

   - Välj **plats** från den **gemensam rubrik** listrutan.

   - Ange huvudets värde. I det här exemplet ska vi använda `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde. Det här ersätter *azurewebsites.net* med *contoso.com* i location-huvudet.

   - Klicka på **OK**.

     ![Ändra location-huvudet](media/rewrite-http-headers-portal/action.png)

8. Klicka på **skapa** att skapa omarbetning.

   ![Ändra location-huvudet](media/rewrite-http-headers-portal/create.png)

9. Då kommer du till vyn omskrivning set. Kontrollera att Skriv om uppsättningen som du skapade ovan finns i listan över omskrivning uppsättningar.

   ![Ändra location-huvudet](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om den konfiguration som krävs för att utföra några vanliga användningsfall i [gemensam rubrik omarbetning scenarier](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
