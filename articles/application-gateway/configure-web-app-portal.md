---
title: Hantera trafik till appar för flera klienter med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar Azure App tjänst webbappar som medlemmar i backend-poolen på en befintlig eller ny Programgateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075168"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurera App Service med Application Gateway

Eftersom App Service är en tjänst för flera innehavare i stället för en dedikera distribution använder den värd rubriken i den inkommande begäran för att lösa begäran till rätt app service-slutpunkt. DNS-namnet på programmet, som i sin tur är det DNS-namn som är associerat med programgatewayen fram till App Service, skiljer sig vanligt vis från domän namnet för Server delens app service. Därför är värd rubriken i den ursprungliga begäran som togs emot av Application Gateway inte samma som värd namnet för backend-tjänsten. På grund av detta, om inte värd huvudet i begäran från programgatewayen till Server delen ändras till Server dels tjänstens värdnamn, kan inte Server delen för flera klient organisationer lösa begäran till rätt slut punkt.

Application Gateway innehåller en växel med namnet `Pick host name from backend address` som åsidosätter värd rubriken i begäran med värd namnet för Server delen när begäran dirigeras från Application Gateway till Server delen. Den här funktionen aktiverar stöd för Server delar för flera klient organisationer, till exempel Azure App service och API Management. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> - Skapa en backend-pool och Lägg till en App Service
> - Skapa HTTP-inställningar och anpassad avsökning med växeln Välj värdnamn aktiverat

## <a name="prerequisites"></a>Krav

- Application Gateway: om du inte har en befintlig Application Gateway, se så här [skapar du en Programgateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- App Service: om du inte har en befintlig App Service kan du läsa [dokumentationen om App Service](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Lägg till App Service som backend-pool

1. Öppna konfigurations visningen av Application Gateway i Azure Portal.

2. Under **backend-pooler**klickar du på **Lägg till** för att skapa en ny backend-pool.

3. Ange ett lämpligt namn för backend-poolen. 

4. Under **mål**klickar du på list rutan och väljer **app Services** som alternativ.

5. En listruta direkt under List rutan **mål** visas som innehåller en lista över dina app Services. I den här List rutan väljer du App Service som du vill lägga till som medlem i en server del och klickar på Lägg till.

   ![App Service-backend](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > List rutan fyller bara i de app-tjänster som finns i samma prenumeration som din Application Gateway. Om du vill använda en app-tjänst som finns i en annan prenumeration än den där Application Gateway är, i stället för att välja **app Services** i list rutan **mål** , väljer du **IP-adress eller värdnamn** och anger värdnamn (exempel. azurewebsites.net) för App Service.

## <a name="create-http-settings-for-app-service"></a>Skapa HTTP-inställningar för App Service

1. Under **http-inställningar**klickar du på **Lägg till** för att skapa en ny http-inställning.

2. Ange ett namn för HTTP-inställningen och du kan aktivera eller inaktivera cookie-baserad tillhörighet enligt ditt krav.

3. Välj protokollet som HTTP eller HTTPS enligt ditt användnings fall. 

   > [!NOTE]
   > Om du väljer HTTPS behöver du inte ladda upp något autentiseringscertifikat eller ett betrott rot certifikat för att vitlista App Service-Dataservern eftersom App Service är en betrodd Azure-tjänst.

4. Markera kryss rutan om du vill **använda App Service** . Observera att växlarna `Create a probe with pick host name from backend address` och `Pick host name from backend address` automatiskt kommer att aktive ras.`Pick host name from backend address` åsidosätter värd rubriken i begäran med värd namnet för Server delen när begäran dirigeras från Application Gateway till Server delen.  

   `Create a probe with pick host name from backend address` skapar automatiskt en hälso avsökning och kopplar den till den här HTTP-inställningen. Du behöver inte skapa någon annan hälso avsökning för den här HTTP-inställningen. Du kan kontrol lera att en ny avsökning med namnet <HTTP Setting name><Unique GUID> har lagts till i listan över hälso avsökningar och att den redan har växeln `Pick host name from backend http settings enabled`.

   Om du redan har en eller flera HTTP-inställningar som används för app service och om dessa HTTP-inställningar använder samma protokoll som det du använder i den som du skapar, kommer du att få en listruta i stället för den `Create a probe with pick host name from backend address` växeln, och välja en av de anpassade avsökningarna. Det beror på att eftersom det redan finns en HTTP-inställning med App Service, så finns det också en hälso avsökning som har växeln `Pick host name from backend http settings enabled`. Välj anpassad avsökning i list rutan.

5. Klicka på **OK** för att skapa http-inställningen.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Skapa regel för att knyta lyssnare, backend-pool och HTTP-inställning

1. Under **regler**klickar du på **grundläggande** för att skapa en ny grundläggande regel.

2. Ange ett lämpligt namn och välj den lyssnare som ska acceptera inkommande begär Anden för App Service.

3. I list rutan **backend-pool** väljer du den backend-pool som du skapade ovan.

4. I list rutan **http-inställningar** väljer du den http-inställning som du skapade ovan.

5. Spara regeln genom att klicka på **OK** .

   ![Regel](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Ytterligare konfiguration i händelse av omdirigering till app Services relativa sökväg

När App Service skickar ett svar för omdirigering till klienten för att omdirigera till dess relativa sökväg (till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2), använder den samma värdnamn i plats huvudet för svar som den som finns i den begäran som togs emot från Application Gateway. Klienten kommer därför att göra begäran direkt till contoso.azurewebsites.net/path2 i stället för att gå igenom Application Gateway (contoso.com/path2). Att kringgå Application Gateway är inte önskvärt.

Om du är i ditt användnings fall finns det scenarier där App Service måste skicka ett svar på omdirigering till klienten, utföra de [ytterligare stegen för att skriva om plats rubriken](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Begränsa åtkomst

Webbapparna som distribueras i de här exemplen använder offentliga IP-adresser som kan nås direkt från Internet. Detta hjälper till med fel sökning när du lär dig mer om en ny funktion och testar nya saker. Men om du planerar att distribuera en funktion till produktion, vill du lägga till fler begränsningar.

Ett sätt som du kan begränsa åtkomsten till dina webbappar är att använda [Azure App Service statiska IP-begränsningar](../app-service/app-service-ip-restrictions.md). Du kan till exempel begränsa webbappen så att den bara tar emot trafik från Application Gateway. Använd funktionen för IP-begränsning i App Service för att Visa programgatewayens VIP som den enda adressen med åtkomst.

## <a name="next-steps"></a>Nästa steg

Mer information om app service och andra stöd för flera innehavare med Application Gateway finns i [service support för flera innehavare med Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
