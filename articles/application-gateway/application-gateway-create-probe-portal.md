---
title: Skapa en anpassad avsökning – Azure Application Gateway – Azure Portal | Microsoft Docs
description: Lär dig hur du skapar en anpassad avsökning för Application Gateway med hjälp av portalen
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
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061803"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Skapa en anpassad avsökning för Application Gateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln lägger du till en anpassad avsökning i en befintlig Application Gateway via Azure Portal. Anpassade avsökningar är användbara för program som har en specifik hälso kontroll sida eller för program som inte ger ett lyckat svar på standard webb programmet.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har en Application Gateway kan du gå till [skapa en Application Gateway](application-gateway-create-gateway-portal.md) för att skapa en Programgateway att arbeta med.

## <a name="createprobe"></a>Skapa avsökningen

Avsökningar konfigureras i en två stegs process via portalen. Det första steget är att skapa avsökningen. I det andra steget lägger du till avsökningen i http-inställningarna för Server delen i Application Gateway.

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte redan har ett konto kan du registrera dig för en [kostnads fri utvärderings period på en månad](https://azure.microsoft.com/free)

1. Klicka på alla resurser i rutan Azure Portal favoriter. Klicka på Application Gateway på bladet alla resurser. Om den prenumeration du valde redan har flera resurser kan du ange partners.contoso.net i filtret efter namn... för att enkelt få åtkomst till Application Gateway.

1. Klicka på avsökningar och klicka på knappen **Lägg** till för att lägga till en avsökning.

   ![Lägg till avsöknings blad med information ifylld][1]

1. Fyll i den information som krävs för avsökningen på bladet **Lägg till hälso avsökning** och slutför genom att klicka på **OK**.

   |**Inställning** | **Värde** | **Detaljer**|
   |---|---|---|
   |**Namn**|customProbe|Det här värdet är ett eget namn på avsökningen som är tillgänglig i portalen.|
   |**Protokoll**|HTTP eller HTTPS | Det protokoll som används av hälso avsökningen.|
   |**Värd**|säga contoso.com|Det här värdet är värd namnet som används för avsökningen. Använd annars 127.0.0.1 om flera platser har kon figurer ATS på Application Gateway. Det här värdet skiljer sig från värd namnet för den virtuella datorn.|
   |**Sökväg**|/eller en annan sökväg|Resten av den fullständiga URL: en för den anpassade avsökningen. En giltig sökväg börjar med "/". För standard Sök vägen för http:\//contoso.com Använd bara "/" |
   |**Intervall (SEK)**|30|Hur ofta avsökningen körs för att kontrol lera hälso tillståndet. Vi rekommenderar inte att du anger de lägre än 30 sekunderna.|
   |**Tids gräns (SEK)**|30|Den tid som avsökningen väntar innan tids gränsen uppnåddes. Timeout-intervallet måste vara tillräckligt högt så att ett HTTP-anrop kan göras för att se till att sidan för Server dels hälsa är tillgänglig.|
   |**Tröskelvärde för ej felfri**|3|Antalet misslyckade försök att anses vara ohälsosamt. Tröskelvärdet kan anges till 1 eller mer.|

   > [!IMPORTANT]
   > Värd namnet är inte detsamma som server namn. Det här värdet är namnet på den virtuella värd som körs på program servern. Avsökningen skickas till http://(värd namnet):(port från httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Lägg till avsökning i gatewayen

Nu när avsökningen har skapats är det dags att lägga till den i gatewayen. Avsöknings inställningar anges i Server delens http-inställningar för programgatewayen.

1. Klicka på **http-inställningar** på programgatewayen för att öppna konfigurations bladet och klicka på de aktuella http-inställningarna för Server delen som anges i fönstret.

   ![fönstret https-inställningar][2]

1. Markera kryss rutan **Använd anpassad avsökning** på bladet inställningar för **appGatewayBackEndHttpSettings** och välj den avsökning som du skapade i avsnittet [skapa avsökning](#createprobe) i list rutan **anpassad avsökning** .
   När du är klar klickar du på **Spara** och inställningarna tillämpas.

Standard avsökningen kontrollerar standard åtkomsten till webb programmet. Nu när en anpassad avsökning har skapats använder Application Gateway den anpassade sökväg som definierats för att övervaka hälsan för backend-servrarna. Baserat på de kriterier som definierats kontrollerar programgatewayen den sökväg som anges i avsökningen. Om anropet till värden: port/Path inte returnerar ett HTTP 200-399-status svar tas servern bort från rotationen när tröskelvärdet för felaktig hälsa har nåtts. Vid avsökning fortsätter den felaktiga instansen att avgöra när den blir felfritt. När instansen har lagts tillbaka till den felfria serverpoolen, börjar trafiken flöda till den igen och avsökningen till instansen fortsätter med det angivna intervallet som normalt.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar SSL-avlastning med Azure Application Gateway finns i [Konfigurera SSL](application-gateway-ssl-portal.md) -avlastning

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

