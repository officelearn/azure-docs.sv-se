---
title: Skapa en anpassad avsökning – Azure Application Gateway – Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en anpassad avsökning för Programgateway med hjälp av portalen
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862173"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Skapa en anpassad avsökning för Programgateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln får du till en anpassad avsökning i en befintlig Programgateway via Azure portal. Anpassade avsökningar är användbara för program som har en specifik hälsokontrollsida eller för program som inte uppger ett lyckat svar på standardwebbprogrammet.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har en application gateway, gå till [skapa en Programgateway](application-gateway-create-gateway-portal.md) att skapa en Programgateway för att arbeta med.

## <a name="createprobe"></a>Skapa avsökningen

Avsökningar konfigureras i en tvåstegsprocess via portalen. Det första steget är att skapa avsökningen. I det andra steget du till avsökningen i serverdelens http-inställningar för programgatewayen.

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/free)

1. Klicka på alla resurser i Azure portal rutan Favoriter. Klicka på application gateway i bladet alla resurser. Om den prenumeration du valde redan har flera resurser kan ange du partners.contoso.net i rutan filtrera efter namn... för att enkelt få åtkomst till Application Gateway.

1. Klicka på **avsökningar** och klicka på den **Lägg till** för att lägga till en avsökning.

   ![Lägg till avsökning bladet med information som har fyllt i][1]

1. På den **Lägg till hälsoavsökning** bladet fylla i informationen som krävs för avsökningen och när du är klar klickar du på **OK**.

   |**Inställning** | **Värde** | **Information**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn som avsökningen som är tillgänglig i portalen.|
   |**Protokoll**|HTTP eller HTTPS | Det protokoll som använder hälsoavsökningen.|
   |**Värd**|i.e contoso.com|Det här värdet är det värdnamn som används för avsökningen. Gäller endast när flera platser har konfigurerats på Application Gateway, annars använda ”127.0.0.1”. Det här värdet skiljer sig från virtuella datorns värdnamn.|
   |**Sökväg**|/ eller en annan sökväg|Resten av den fullständiga URL: en för anpassad avsökning. En giltig sökväg som börjar med ”/”. Standard för HTTP-sökväg:\//contoso.com bara använda '/' |
   |**Intervall (sek)**|30|Hur ofta körs avsökningen för att söka efter hälsotillstånd. Det rekommenderas inte att ställa in lägre än 30 sekunder.|
   |**Timeout (sek)**|30|Hur lång tid avsökningen väntar innan den avbryts. Timeout-intervall måste vara tillräckligt högt för att ett http-anrop kan göras för att säkerställa sidan backend-hälsotillstånd är tillgänglig.|
   |**Tröskelvärde för Ej felfri**|3|Antal misslyckade försök för att anses vara felaktiga. Ett tröskelvärde för 0 betyder som om en hälsokontroll misslyckas backend-bestäms feltillstånd omedelbart.|

   > [!IMPORTANT]
   > Värdnamnet är inte detsamma som servernamnet på. Det här värdet är namnet på den virtuella värden som körs på programservern. Avsökningen skickas till http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Lägg till avsökning i gatewayen

Nu när avsökningen har skapats, är det dags att lägga till den gatewayen. Avsökningen inställningar är inställda på serverdelens http-inställningar för programgatewayen.

1. Klicka på **HTTP-inställningar** på application-gateway för att få fram den konfigurationen bladet klickar du på den aktuella serverdelen http-inställningar visas i fönstret.

   ![fönstret för HTTPS-inställningar][2]

1. På den **appGatewayBackEndHttpSettings** inställningsbladet, kontrollera den **Använd anpassad avsökning** kryssrutan och välj avsökningen som skapats i den [skapar du avsökningen](#createprobe) avsnitt på  **Anpassad avsökning** listrutan...
   När du är klar klickar du på **spara** och inställningarna tillämpas.

Standard-avsökningen söker standardåtkomst till webbprogrammet. Nu när du har skapat en anpassad avsökning använder application gateway anpassade sökvägen som definierats för att övervaka hälsotillståndet för backend-servrarna. Application gateway kontrollerar baserat på kriterier som har definierats, den angivna sökvägen i avsökningen. Om anropet till värd: Port / sökväg inte returnerar en HTTP 200 – 399 statussvar, servern tas bort från roteringen efter feltillstånd tröskelvärdet har uppnåtts. Avsökning fortsätter på den felaktiga instansen att avgöra när den blir felfri igen. När instansen har lagts till felfria serverpoolen kan trafik börjar flöda till den igen och avsökning till instansen fortsätter vid angivna intervall som användaren som vanligt.

## <a name="next-steps"></a>Nästa steg

Läs hur du konfigurerar SSL-avlastning med Azure Application Gateway i [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

