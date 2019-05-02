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
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947167"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Skriv om HTTP-begäran och svarshuvuden med Azure Application Gateway – Azure-portalen

Den här artikeln beskriver hur du använder Azure-portalen för att konfigurera en [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) instans att skriva om HTTP-huvuden i begäranden och svar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en Application Gateway v2 SKU-instans att slutföra stegen i den här artikeln. Skriva om rubriker stöds inte i v1-SKU. Om du inte har v2-SKU, skapar du en [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) instans innan du börjar.

## <a name="create-required-objects"></a>Skapa nödvändiga objekt

För att konfigurera HTTP-huvud omskrivning, måste du slutföra de här stegen.

1. Skapa de objekt som krävs för Skriv om HTTP-huvud:

   - **Skriv om åtgärden**: Används för att ange begäran och begäran huvudfält som du avser att skriva om och det nya värdet för sidhuvudena. Du kan koppla ett eller flera omarbetning villkor med en omarbetning åtgärd.

   - **Skriv om villkoret**: En valfri konfiguration. Skriv om villkor utvärdera innehållet i HTTP (S)-begäranden och svar. Skriv om-åtgärd som ska utföras om den HTTP (S)-begäran eller ett svar matchar villkoret omarbetning.

     Om du associerar mer än ett villkor med åtgärden utförs åtgärden endast när alla villkor är uppfyllda. Åtgärden är med andra ord en logiskt AND-åtgärd.

   - **Skriva om regeln**: Innehåller flera omskrivning åtgärd / omarbetning kombinationer av villkor.

   - **Regeln sekvens**: Hjälper dig att fastställa i vilken ordning som omskrivningsregler köra. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omarbetning. En omskrivningsregel som har ett lägre värde för regeln sekvens körs första. Om du tilldelar två omskrivningsregler samma regel sekvens värde är ordningen för körningen icke-deterministisk.

   - **Skriv om set**: Innehåller flera omskrivningsregler som ska associeras med en regel för vidarebefordran av begäran.

2. Koppla omskrivning inställd på en regel för vidarebefordran. Skriv om konfigurationen är kopplat till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När du använder en sökvägsbaserad regel har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. I så fall gäller endast för området angiven sökväg för en plats.

Du kan skapa flera HTTP-huvud omskrivning uppsättningar och tillämpa varje omarbetning som angetts till flera lyssnare. Men du kan använda endast en omarbetning inställd på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure-konto.

## <a name="configure-header-rewrite"></a>Konfigurera huvud-omskrivning

I det här exemplet ändrar vi en omdirigerings-URL genom att skriva om location-huvudet i HTTP-svar som skickas av en backend-programmet.

1. Välj **alla resurser**, och välj sedan din application gateway.

2. Välj **returpaket** i den vänstra rutan.

3. Välj **omarbetning set**:

   ![Lägg till uppsättning av omarbetning](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Ange ett namn för omarbetning och koppla den till en regel för vidarebefordran:

   - Ange namn för omarbetning som angetts i den **namn** box.
   - Välj en eller flera av de regler som anges i den **associerade routningsregler** lista. Du kan välja endast de regler som inte har associerats med andra omskrivning uppsättningar. De regler som redan är associerade med andra omskrivning uppsättningar är nedtonade.
   - Välj **Nästa**.
   
     ![Lägg till namn och association](media/rewrite-http-headers-portal/name-and-association.png)

5. Skapa en omskrivningsregel:

   - Välj **Add-omskrivningsregel**.

     ![Lägg till omskrivningsregel](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Ange ett namn för omskrivningsregel i den **omskrivning Regelnamn** box. Ange ett tal i den **regel sekvens** box.

     ![Lägg till omskrivning Regelnamn](media/rewrite-http-headers-portal/rule-name.png)

6. I det här exemplet ska vi skriva om location-huvudet endast när den innehåller en referens till azurewebsites.net. Lägg till ett villkor att utvärdera om location-huvudet i svaret innehåller azurewebsites.net för att göra detta:

   - Välj **Lägg till villkor** och välj sedan rutan som innehåller den **om** anvisningar för att expandera den.

     ![Lägg till ett villkor](media/rewrite-http-headers-portal/add-condition.png)

   - I den **typen av variabel att kontrollera** väljer **HTTP-huvud**.

   - I den **Rubriktyp** väljer **svar**.

   - Eftersom i det här exemplet vi utvärderar location-huvudet som är en gemensam rubrik, Välj **gemensam rubrik** under **rubriknamn**.

   - I den **gemensam rubrik** väljer **plats**.

   - Under **skiftlägeskänsliga**väljer **nr**.

   - I den **operatorn** väljer **är lika med (=)**.

   - Ange ett mönster för reguljärt uttryck. I det här exemplet använder vi mönstret `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Välj **OK**.

     ![Konfigurera en om villkor](media/rewrite-http-headers-portal/condition.png)

7. Lägg till en åtgärd för att skriva om location-huvudet:

   - I den **åtgärdstyp** väljer **ange**.

   - I den **Rubriktyp** väljer **svar**.

   - Under **rubriknamn**väljer **gemensam rubrik**.

   - I den **gemensam rubrik** väljer **plats**.

   - Ange huvudets värde. I det här exemplet använder vi `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` som huvudets värde. Det här värdet kommer att ersätta *azurewebsites.net* med *contoso.com* i location-huvudet.

   - Välj **OK**.

     ![Lägga till en åtgärd](media/rewrite-http-headers-portal/action.png)

8. Välj **skapa** att skapa omarbetning:

   ![Välj Skapa](media/rewrite-http-headers-portal/create.png)

9. Skriv om set vyn öppnas. Kontrollera att det är uppsättningen omarbetning som du skapade i listan över omskrivning uppsättningar:

   ![Skriv om set-vy](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du ställer in några vanliga användningsområden i [gemensam rubrik omarbetning scenarier](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).