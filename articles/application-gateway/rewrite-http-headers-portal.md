---
title: Skriv om HTTP-begäranden och svarshuvuden i portalen - Azure Application Gateway
description: Lär dig hur du använder Azure-portalen för att konfigurera en Azure Application Gateway för att skriva om HTTP-huvudena i begäranden och svar som passerar genom gatewayen
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012860"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Skriva om HTTP-begäranden och svarshuvuden med Azure Application Gateway - Azure-portal

I den här artikeln beskrivs hur du använder Azure-portalen för att konfigurera en [Application Gateway v2 SKU-instans](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) för att skriva om HTTP-huvudena i begäranden och svar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en Application Gateway v2 SKU-instans för att kunna slutföra stegen i den här artikeln. Att skriva om rubriker stöds inte i v1 SKU. Om du inte har v2 SKU skapar du en [Application Gateway v2 SKU-instans](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) innan du börjar.

## <a name="create-required-objects"></a>Skapa obligatoriska objekt

Om du vill konfigurera OMskrivning av HTTP-huvuden måste du utföra de här stegen.

1. Skapa de objekt som krävs för HTTP-huvud skriva om:

   - **Skriv om åtgärd:** Används för att ange de fält för begäran och begäran om huvud som du tänker skriva om och det nya värdet för rubrikerna. Du kan associera ett eller flera omskrivningsvillkor med en omskrivningsåtgärd.

   - **Skriv om villkor**: En valfri konfiguration. Omskrivningsvillkor utvärderar innehållet i HTTP(S) begäranden och svar. Omskrivningsåtgärden inträffar om HTTP(S)-begäran eller -svaret matchar omskrivningsvillkoret.

     Om du associerar mer än ett villkor med en åtgärd inträffar åtgärden endast när alla villkor är uppfyllda. Med andra ord är åtgärden en logisk OCH-åtgärd.

   - **Skriv om regel**: Innehåller flera omskrivningsåtgärder/skriva om villkorskombinationer.

   - **Regelsekvens**: Hjälper till att bestämma i vilken ordning omskrivningsreglerna ska köras. Den här konfigurationen är användbar när du har flera omskrivningsregler i en omskrivningsuppsättning. En omskrivningsregel som har ett lägre regelsekvensvärde körs först. Om du tilldelar samma regelsekvensvärde till två omskrivningsregler är körningsordningen icke-deterministisk.

   - **Skriv om set**: Innehåller flera omskrivningsregler som ska associeras med en routningsregel för begäran.

2. Koppla omskrivningsuppsättningen till en routningsregel. Omskrivningskonfigurationen är kopplad till källavlyssningsaren via routningsregeln. När du använder en grundläggande routningsregel associeras konfigurationen för huvudreformat med en källlyssnare och en global rubrikskrivning. När du använder en sökvägsbaserad routningsregel definieras konfigurationen för rubrikreda på URL-sökvägskartan. I så fall gäller det endast för området för en platss specifika sökväg.

Du kan skapa flera HTTP-rubrikstrevuppsättningar och använda varje omskrivningsuppsättning på flera lyssnare. Men du kan bara använda en omskrivningsuppsättning på en viss lyssnare.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/) med ditt Azure-konto.

## <a name="configure-header-rewrite"></a>Konfigurera omskrivning av sidhuvud

I det här exemplet ändrar vi en url för omdirigering genom att skriva om platshuvudet i HTTP-svaret som skickas av ett backend-program.

1. Välj **Alla resurser**och välj sedan programgatewayen.

2. Välj **Skriva om** i den vänstra rutan.

3. Välj **Skriv om set:**

   ![Lägg till omskrivningsuppsättning](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Ange ett namn för omskrivningsuppsättningen och associera den med en routningsregel:

   - Ange namnet på den omskrivningsuppsättning som angetts i rutan **Namn.**
   - Välj en eller flera av reglerna i listan **Associerade routningsregler.** Du kan bara välja regler som inte har associerats med andra omskrivningsuppsättningar. De regler som redan har associerats med andra omskrivningsuppsättningar är nedtonade.
   - Välj **Nästa**.
   
     ![Lägg till namn och association](media/rewrite-http-headers-portal/name-and-association.png)

5. Skapa en skrivregel:

   - Välj **Lägg till skriv om regel**.

     ![Lägga till skrivregel](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Ange ett namn på skriv-regeln i rutan **Skriv om regelnamn.** Ange ett nummer i rutan **Regelsekvens.**

     ![Lägg till skriv om regelnamn](media/rewrite-http-headers-portal/rule-name.png)

6. I det här exemplet skriver vi bara om platshuvudet när det innehåller en referens till azurewebsites.net. Det gör du genom att lägga till ett villkor för att utvärdera om platshuvudet i svaret innehåller azurewebsites.net:

   - Välj **Lägg till villkor** och markera sedan rutan som innehåller **if-instruktionerna** för att expandera det.

     ![Lägg till ett villkor](media/rewrite-http-headers-portal/add-condition.png)

   - Välj **HTTP-huvud**i listan **Typ av variabel som ska kontrolleras** .

   - Välj **Svar**i listan **Rubriktyp** .

   - Eftersom vi i det här exemplet utvärderar platshuvudet, som är ett vanligt huvud, väljer **Du Gemensam rubrik** under **Rubriknamn**.

   - Välj **Plats**i listan **Gemensamt huvud** .

   - Under **Skiftlägeskänslig**väljer du **Nej**.

   - Välj **lika (=)** i listan **Operator** .

   - Ange ett mönster för reguljärt uttryck. I det här exemplet använder `(https?):\/\/.*azurewebsites\.net(.*)$`vi mönstret .

   - Välj **OK**.

     ![Konfigurera ett If-villkor](media/rewrite-http-headers-portal/condition.png)

7. Lägg till en åtgärd för att skriva om platshuvudet:

   - Välj **Ange**i listan **Åtgärdstyp** .

   - Välj **Svar**i listan **Rubriktyp** .

   - Under **Rubriknamn**väljer du **Gemensamt huvud**.

   - Välj **Plats**i listan **Gemensamt huvud** .

   - Ange rubrikvärdet. I det här exemplet `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` använder vi som huvudvärde. Det här värdet ersätter *azurewebsites.net* med *contoso.com* i platshuvudet.

   - Välj **OK**.

     ![Lägga till en åtgärd](media/rewrite-http-headers-portal/action.png)

8. Välj **Skapa** om du vill skapa omskrivningsuppsättningen:

   ![Välj Skapa](media/rewrite-http-headers-portal/create.png)

9. Vyn Skriv om uppsättningen öppnas. Kontrollera att omskrivningsuppsättningen som du skapade finns i listan över omskrivningsuppsättningar:

   ![Skriv om uppsättningsvyn](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar några vanliga användningsfall finns i [vanliga scenarier för omskrivning av huvudhuvuden](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).