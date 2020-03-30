---
title: Skapa en anpassad avsökning med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar en anpassad avsökning för Application Gateway med hjälp av portalen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074614"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Skapa en anpassad avsökning för Application Gateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln lägger du till en anpassad hälsoavsökning i en befintlig programgateway via Azure-portalen. Med hjälp av hälsoavsökningarna övervakar Azure Application Gateway hälsotillståndet för resurserna i backend-poolen.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har en programgateway besöker du [Skapa en programgateway](application-gateway-create-gateway-portal.md) för att skapa en programgateway att arbeta med.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Skapa avsökning för Application Gateway v2 SKU

Avsökningar konfigureras i en tvåstegsprocess via portalen. Det första steget är att ange de värden som krävs för avsökningskonfigurationen. I det andra steget testar du serverdhälsan med hjälp av den här avsökningskonfigurationen och sparar avsökningen. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Ange avsökningsegenskaper

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri provperiod](https://azure.microsoft.com/free) på en månad

2. Klicka på Alla resurser i rutan Favoriter i Azure Portal. Klicka på programgatewayen i bladet Alla resurser. Om den prenumeration du valde redan har flera resurser kan du ange partners.contoso.net i rutan Filtrera efter namn... för att enkelt få åtkomst till Application Gateway.

3. Välj **Hälsoavsökningar** och välj sedan **Lägg till** om du vill lägga till en ny hälsoavsökning.

   ![Lägg till ny avsökning][4]

4. På sidan **Lägg till hälsoavsökning** fyller du i den information som krävs för avsökningen och välj **OK**när du är klar .

   |**Inställning** | **Värde** | **Detaljer**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn som ges till avsökningen som är tillgänglig i portalen.|
   |**Protokollet**|HTTP eller HTTPS | Protokollet som hälsoavsökningen använder. |
   |**Värd**|Dvs contoso.com|Det här värdet är namnet på den virtuella värden (skiljer sig från vm-värdnamnet) som körs på programservern. Avsökningen skickas till (protokoll)://(värdnamn):(port från httpsetting)/urlPath.  Detta gäller när flera plats konfigureras på Application Gateway. Om Application Gateway är konfigurerad för en enda plats anger du '127.0.0.1'.|
   |**Välj värdnamn från HTTP-inställningar för namnsbakskrift**|Ja eller nej|Anger *värdhuvudet* i avsökningen till värdnamnet för backend-resursen i backend-poolen som är associerad med HTTP-inställningen som den här avsökningen är associerad med. Speciellt krävs vid backend för flera innehavare, till exempel Azure-apptjänst. [Läs mer](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Sökväg**|/ eller annan väg|Resten av den fullständiga url:en för den anpassade avsökningen. En giltig sökväg börjar med '/'. För standardsökvägen för\/http: /contoso.com använd bara '/' |
   |**Intervall (sek)**|30|Hur ofta sonden körs för att kontrollera om det finns hälsa. Det rekommenderas inte att ställa in den nedre delen av 30 sekunder.|
   |**Timeout (sek)**|30|Den tid som sonden väntar innan den går ut. Om ett giltigt svar inte tas emot inom den här time-out-perioden markeras avsökningen som misslyckad. Tidsgränsen måste vara tillräckligt högt för att ett http-samtal ska kunna göras för att säkerställa att hälsosidan för backend är tillgänglig. Observera att timeout-värdet inte bör vara mer än det intervallvärde som används i den här avsökningsinställningen eller värdet "Begär timeout" i HTTP-inställningen som ska associeras med den här avsökningen.|
|**Felfritt tröskelvärde**|3|Antal misslyckade försök i följd som ska betraktas som felaktiga. Tröskelvärdet kan ställas in på 1 eller mer.|
   |**Använd avsökningsmatchningsvillkor**|Ja eller nej|Som standard anses ett HTTP(S)-svar med statuskod mellan 200 och 399 vara felfritt. Du kan ändra det acceptabla intervallet för backend-svarskod eller backend-svarstext. [Läs mer](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**HTTP-inställningar**|markering från listruta|Avsökningen associeras med de HTTP-inställningar som valts här och övervakar därför hälsotillståndet för den servergrupp som är associerad med den valda HTTP-inställningen. Den kommer att använda samma port för avsökningsbegäran som den som används i den valda HTTP-inställningen. Du kan bara välja de HTTP-inställningar som inte är associerade med någon annan anpassad avsökning. <br>Observera att endast de HTTP-inställningar som är tillgängliga för associationer som har samma protokoll som det protokoll som valts i den här avsökningskonfigurationen och som har samma tillstånd för *inställningen Välj värdnamn från servergrupps-HTTP-inställningsväxeln.*|
   
   > [!IMPORTANT]
   > Avsökningen övervakar endast serverningshälsan när den är associerad med en eller flera HTTP-inställningar. Backend-resurser för de backend-pooler som är associerade till de HTTP-inställningar som avsökningen är associerad med övervakar backend-resurser som är associerade med. Avsökningsbegäran skickas till http://(värdnamn):(port från httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Testa ryggsjälva hälsa med sonden

När du har angett avsökningsegenskaperna kan du testa hälsotillståndet för backend-resurser för att kontrollera att avsökningskonfigurationen är korrekt och att backend-resurserna fungerar som förväntat.

1. Välj **Testa** och notera resultatet av avsökningen. Programgatewayen testar hälsotillståndet för alla serverdresurser i serverdapoolerna som är associerade med HTTP-inställningarna som används för den här avsökningen. 

   ![Testa ryggsjäl och hälsa][5]

2. Om det finns några felaktiga serverdresurser kontrollerar du kolumnen **Information** för att förstå orsaken till resursens felaktiga tillstånd. Om resursen har markerats som fel på grund av en felaktig avsökningskonfiguration väljer du länken **Gå tillbaka till avsökningen** och redigerar avsökningskonfigurationen. Annars, om resursen har markerats fel på grund av ett problem med servering, sedan lösa problemen med serveringsresursen och sedan testa serverkopplingen igen genom att välja **länken Gå tillbaka till avsökningen** och välj **Testa**.

   > [!NOTE]
   > Du kan välja att spara avsökningen även med felaktiga serverdresurser, men det rekommenderas inte. Detta beror på att programgateway tar bort serverda resurser från serverda poolen som bedöms vara fel av avsökningen. Om det inte finns några felfria resurser i en backend pool, kommer du inte att kunna komma åt ditt program och kommer att få en 502 fel.

   ![Visa avsökningsresultat][6]

3. Välj **Lägg till** om du vill spara avsökningen. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Skapa avsökning för Application Gateway v1 SKU

Avsökningar konfigureras i en tvåstegsprocess via portalen. Det första steget är att skapa avsökningen. I det andra steget lägger du till avsökningen i serverings-http-inställningarna för programgatewayen.

### <a name="create-the-probe"></a><a name="createprobe"></a>Skapa avsökningen

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri provperiod](https://azure.microsoft.com/free) på en månad

2. Klicka på **Alla resurser** i rutan Favoriter i Azure Portal. Välj programgatewayen på sidan **Alla resurser.** Om den prenumeration du valde redan har flera resurser kan du ange partners.contoso.net i rutan Filtrera efter namn... för att enkelt få åtkomst till Application Gateway.

3. Välj **Avsökningar** och välj sedan **Lägg till** om du vill lägga till en avsökning.

   ![Lägg till probeblad med information ifylld][1]

4. Fyll i den information som krävs för avsökningen på bladet **Lägg till hälsoavsökning** och välj **OK**när du är klar .

   |**Inställning** | **Värde** | **Detaljer**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn som ges till avsökningen som är tillgänglig i portalen.|
   |**Protokollet**|HTTP eller HTTPS | Protokollet som hälsoavsökningen använder. |
   |**Värd**|Dvs contoso.com|Det här värdet är namnet på den virtuella värden (skiljer sig från vm-värdnamnet) som körs på programservern. Avsökningen skickas till (protokoll)://(värdnamn):(port från httpsetting)/urlPath.  Detta gäller när flera plats konfigureras på Application Gateway. Om Application Gateway är konfigurerad för en enda plats anger du '127.0.0.1'.|
   |**Välj värdnamn från HTTP-inställningar för namnsbakskrift**|Ja eller nej|Anger *värdhuvudet* i avsökningen till värdnamnet för backend-resursen i backend-poolen som är associerad med HTTP-inställningen som den här avsökningen är associerad med. Speciellt krävs vid backend för flera innehavare, till exempel Azure-apptjänst. [Läs mer](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Sökväg**|/ eller annan väg|Resten av den fullständiga url:en för den anpassade avsökningen. En giltig sökväg börjar med '/'. För standardsökvägen för\/http: /contoso.com använd bara '/' |
   |**Intervall (sek)**|30|Hur ofta sonden körs för att kontrollera om det finns hälsa. Det rekommenderas inte att ställa in den nedre delen av 30 sekunder.|
   |**Timeout (sek)**|30|Den tid som sonden väntar innan den går ut. Om ett giltigt svar inte tas emot inom den här time-out-perioden markeras avsökningen som misslyckad. Tidsgränsen måste vara tillräckligt högt för att ett http-samtal ska kunna göras för att säkerställa att hälsosidan för backend är tillgänglig. Observera att timeout-värdet inte bör vara mer än det intervallvärde som används i den här avsökningsinställningen eller värdet "Begär timeout" i HTTP-inställningen som ska associeras med den här avsökningen.|
|**Felfritt tröskelvärde**|3|Antal misslyckade försök i följd som ska betraktas som felaktiga. Tröskelvärdet kan ställas in på 1 eller mer.|
   |**Använd avsökningsmatchningsvillkor**|Ja eller nej|Som standard anses ett HTTP(S)-svar med statuskod mellan 200 och 399 vara felfritt. Du kan ändra det acceptabla intervallet för backend-svarskod eller backend-svarstext. [Läs mer](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Värdnamnet är inte samma som servernamnet. Det här värdet är namnet på den virtuella värden som körs på programservern. Avsökningen skickas till http://(värdnamn):(port från httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Lägga till avsökning i gatewayen

Nu när avsökningen har skapats är det dags att lägga till den i gatewayen. Avsökningsinställningarna är inställda på serverd http-inställningarna för programgatewayen.

1. Klicka på **HTTP-inställningar** på programgatewayen om du vill visa konfigurationsbladet genom att klicka på de aktuella bakåtsträvande http-inställningarna i fönstret.

   ![https inställningar fönster][2]

2. På **inställningssidan för appGatewayBackEndHttpSettings** kontrollerar du kryssrutan **Använd anpassad avsökning** och väljer den avsökning som skapats i avsnittet [Skapa avsökningen](#createprobe) i listrutan **Anpassad avsökning.**
   När du är klar klickar du på **Spara** och inställningarna tillämpas.

## <a name="next-steps"></a>Nästa steg

Visa hälsotillståndet för serverdresurserna som bestäms av avsökningen med hjälp av hälsovyn för [servering](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
