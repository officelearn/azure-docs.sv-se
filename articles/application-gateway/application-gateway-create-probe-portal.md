---
title: "Skapa en anpassad avsökning - Azure Application Gateway - Azure-portalen | Microsoft Docs"
description: "Lär dig hur du skapar en anpassad avsökningsåtgärd för Programgateway med hjälp av portalen"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: bb77c9b39e1aa89f6411de8ec3b1fca41e954bf2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Skapa en anpassad avsökningsåtgärd för Programgateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln, lägger du till en anpassad avsökning en befintlig Programgateway via Azure-portalen. Anpassade avsökningar är användbara för program som har ett visst hälsotillstånd Kontrollera sidan eller för program som inte tillhandahåller ett lyckat svar på standardwebbprogrammet.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har en Programgateway finns [skapa en Programgateway](application-gateway-create-gateway-portal.md) att skapa en Programgateway att arbeta med.

## <a name="createprobe"></a>Skapa avsökningen

Avsökningar konfigureras i en tvåstegsprocess via portalen. Det första steget är att skapa avsökningen. I det andra steget du lägger till avsökningen serverdelens http-inställningar för programgatewayen.

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/free)

1. Klicka på alla resurser i Azure portal Favoriter fönstret. Klicka på Programgateway i bladet för alla resurser. Om den prenumeration som du har valt redan har flera resurser i den, kan du ange partners.contoso.net i filtret efter namn... för att enkelt få åtkomst till Application Gateway.

1. Klicka på **avsökningar** och klicka på den **Lägg till** för att lägga till en avsökning.

  ![Lägg till avsökning blad med information som har fyllt i][1]

1. På den **Lägg till hälsoavsökningen** bladet fyller i informationen som krävs för avsökningen och när du är klar klickar du på **OK**.

  |**Inställning** | **Värde** | **Detaljer**|
  |---|---|---|
  |**Namn**|customProbe|Det här värdet är ett eget namn som avsökningen som är tillgänglig i portalen.|
  |**Protokoll**|HTTP eller HTTPS | Det protokoll som använder hälsoavsökningen.|
  |**Värden**|engångsfaktorautentisering contoso.com|Det här värdet är värdnamnet som används för avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway, Använd annars ”127.0.0.1”. Det här värdet skiljer sig från virtuella datorns värdnamn.|
  |**Sökväg**|/ eller en annan sökväg|Resten av en fullständig url för den anpassade avsökningen. En giltig sökväg börjar med '/'. Sökvägen till http://contoso.com bara använda '/' |
  |**Intervall (sek)**|30|Hur ofta avsökningen körs för att söka efter hälsotillstånd. Det rekommenderas inte att ange lägst än 30 sekunder.|
  |**Timeout (sek)**|30|Hur lång tid avsökningen timeout. Timeout-intervall måste vara tillräckligt högt för att en http-anrop kan göras så sidan backend hälsotillstånd är tillgänglig.|
  |**Tröskelvärde för ohälsosamt värde**|3|Antal misslyckade försök att betraktas som ohälsosamt. Ett tröskelvärde 0 betyder som om en hälsokontroll misslyckas serverdel bestäms ohälsosamt omedelbart.|

  > [!IMPORTANT]
  > Värdnamnet är inte detsamma som servernamnet på. Det här värdet är namnet på den virtuella värden som körs på programservern. Avsökningen skickas till http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Lägga till avsökning till gatewayen

Nu när avsökningen har skapats, är det dags att lägga till gatewayen. Avsökningen inställningar konfigureras för programgatewayen serverdelens http-inställningar.

1. Klicka på **HTTP-inställningar** på Programgateway, så att konfigurationen bladet Klicka aktuella serverdelens http-inställningar visas i fönstret.

  ![fönster för HTTPS-inställningar][2]

1. På den **appGatewayBackEndHttpSettings** inställningsbladet, kontrollera den **Använd anpassad avsökningsåtgärd** kryssrutan och välj avsökningen som skapats i den [skapa avsökningen](#createprobe) avsnitt på den **anpassad avsökningen** nedrullningsbara...
När du är klar klickar du på **spara** och inställningarna som tillämpas.

Standard-avsökning kontrollerar standardåtkomst till webbprogrammet. Nu när du har skapat en anpassad avsökning använder programgatewayen anpassad sökväg som definierats för att övervaka hälsan för backend-servrarna. Programgatewayen kontrollerar baserat på kriterier som har definierats, den sökväg som anges i avsökningen. Om anropet till värdnamn: Port / sökvägen inte returnerar ett HTTP 200 399 Statussvaret, servern tas bort från rotationen efter ohälsosamt tröskelvärdet har uppnåtts. Avsökning av fortsätter på ohälsosamt instans att avgöra när det blir felfri igen. När instansen läggs till felfritt serverpoolen trafiken börjar flöda till den igen och sökning till instansen fortsätter vid angivna intervall som användaren som vanligt.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar SSL-avlastning med Azure Programgateway finns [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

