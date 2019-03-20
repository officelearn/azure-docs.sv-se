---
title: Hantera trafik till appar för flera klienter, till exempel App service web apps med Azure Application Gateway – Portal
description: Den här artikeln innehåller råd om hur du konfigurerar Azure App service-webbappar som medlemmar i serverdelspool på en befintlig eller ny Programgateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176216"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurera App Service med Application Gateway

Application gateway kan du ha en Azure App service-webapp eller andra tjänster med flera innehavare som en medlem i serverdelspoolen. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> - Skapa en serverdelspool och Lägg till en App Service
> - Skapa HTTP-inställningar och anpassad avsökning med ”Välj värdnamnet” växlar aktiverat

## <a name="prerequisites"></a>Förutsättningar

- Application gateway: Om du inte har en befintlig application gateway kan du läsa hur du [skapar en application gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Apptjänst: Om du inte har en befintlig App service kan du läsa [dokumentationen till App service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Lägg till App service som backend-pool

1. Öppna Konfigurationsvy för application gateway i Azure-portalen.

2. Under **serverdelspooler**, klicka på **Lägg till** att skapa en ny serverdelspool.

3. Ange ett lämpligt namn till i serverdelspoolen. 

4. Under **mål**, klickar du på listrutan och välj **Apptjänster** som alternativ.

5. En listruta som nedanför den **mål** listrutan visas som innehåller en lista med din App Services. Välj App Service som du vill lägga till backend-pool och klicka på Lägg till den här listrutan.

   ![App service-serverdelen](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Skapa HTTP-inställningar för App service

1. Under **HTTP-inställningar**, klickar du på **Lägg till** att skapa en ny HTTP-inställning.

2. Ange ett namn för HTTP-inställning och du kan aktivera eller inaktivera Cookie-baserad tillhörighet enligt dina behov.

3. Välj protokollet som HTTP eller HTTPS enligt ditt användningsområde. 

4. Markera kryssrutan för **för App Service** och det så aktiveras den **skapa en avsökning med Välj värdnamnet från serverdelsadressen** och **Välj värdnamnet från serverdelsadressen** alternativ. Det här alternativet kommer också automatiskt skapa en avsökning med växeln aktiverad och koppla den till den här HTTP-inställning.

5. Klicka på **OK** att skapa HTTP-inställning.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Skapa regel för att knyta lyssnaren och Serverdelspoolen HTTP-inställning

1. Under **regler**, klickar du på **grundläggande** att skapa en ny grundläggande regel.

2. Ange ett lämpligt namn och välj den lyssnare som kommer att acceptera inkommande begäranden för App service.

3. I den **serverdelspool** listrutan Välj serverdelen programpool du skapade ovan.

4. I den **HTTP-inställning** listrutan Välj HTTP ställningen du skapade ovan.

5. Klicka på **OK** att spara den här regeln.

   ![Regel](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Begränsa åtkomst

Webbprogram som distribueras i de här exemplen använder offentliga IP-adresser som kan nås direkt från Internet. Detta hjälper med felsökning när du lär dig om en ny funktion och försök nya saker. Men om du planerar att distribuera en funktion i produktion, ska du lägga till fler begränsningar.

Ett sätt som du kan begränsa åtkomsten till dina webbprogram är att använda [Azure App Service statiska IP-adressbegränsningar](../app-service/app-service-ip-restrictions.md). Du kan till exempel begränsa webbappen så att den endast tar emot trafik från programgatewayen. Använd funktionen app service IP-begränsning visa en lista över application gateway VIP som den enda adressen med åtkomst.

## <a name="next-steps"></a>Nästa steg

Läs mer om App service och andra stöd för flera klienter med application gateway i [multiklienttjänst support med application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Om svaret från din App service omdirigera till App service-URL, se hur du [felsöka omdirigering till App service-URL problemet](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
