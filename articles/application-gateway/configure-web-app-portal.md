---
title: Hantera trafik till appar med flera innehavare med hjälp av portalen
titleSuffix: Azure Application Gateway
description: Den här artikeln innehåller vägledning om hur du konfigurerar Azure App-tjänstwebbappar som medlemmar i serverdelspoolen på en befintlig eller ny programgateway.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075168"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurera apptjänst med programgateway

Eftersom apptjänsten är en tjänst med flera innehavare i stället för en dedikeringsdistribution, används värdhuvudet i den inkommande begäran för att matcha begäran till rätt slutpunkt för apptjänst. Vanligtvis skiljer sig DNS-namnet på programmet, som i sin tur är DNS-namnet som är associerat med programgatewayen som frontar apptjänsten, från domännamnet för backend-apptjänsten. Därför är värdhuvudet i den ursprungliga begäran som tas emot av programgatewayen inte samma sak som värdnamnet för backend-tjänsten. På grund av detta, om inte värdhuvudet i begäran från programgatewayen till backend ändras till värdnamnet för backend-tjänsten, kan backend-post med flera innehavare inte matcha begäran till rätt slutpunkt.

Application Gateway tillhandahåller `Pick host name from backend address` en växel som anropas som åsidosätter värdhuvudet i begäran med värdnamnet för backend när begäran dirigeras från programgatewayen till backend. Den här funktionen möjliggör stöd för bakåtändringar med flera innehavare, till exempel Azure-apptjänst och API-hantering. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> - Skapa en backend-pool och lägga till en apptjänst i den
> - Skapa HTTP-inställningar och anpassad avsökning med växeln "Plocka värdnamn" aktiverat

## <a name="prerequisites"></a>Krav

- Programgateway: Om du inte har en befintlig programgateway läser du hur [du skapar en programgateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Apptjänst: Om du inte har en befintlig App-tjänst läser du [Dokumentation för Apptjänst](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Lägg till apptjänst som backend-pool

1. Öppna konfigurationsvyn för programgatewayen i Azure-portalen.

2. Under **Backend-pooler**klickar du på **Lägg** till för att skapa en ny backend-pool.

3. Ange ett lämpligt namn till backend poolen. 

4. Under **Mål**klickar du på listrutan och väljer **App Services** som alternativ.

5. En listruta direkt under listrutan **Mål** visas som innehåller en lista över dina Apptjänster. Välj den Apptjänst som du vill lägga till som en backend-poolmedlem i den här listrutan och klicka på Lägg till.

   ![Backend för apptjänsten](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > Listrutan fyller bara de apptjänster som finns i samma prenumeration som programgatewayen. Om du vill använda en apptjänst som finns i en annan prenumeration än den där Application Gateway är, väljer du **IP-adress eller värdnamn** i stället för att välja **AppTjänster** i listrutan **Mål** och anger exempel. azurewebsites.net) av apptjänsten.

## <a name="create-http-settings-for-app-service"></a>Skapa HTTP-inställningar för apptjänsten

1. Klicka på **Lägg till** under **HTTP-inställningar**om du vill skapa en ny HTTP-inställning.

2. Mata in ett namn för HTTP-inställningen och du kan aktivera eller inaktivera cookiebaserad tillhörighet enligt dina krav.

3. Välj protokollet som HTTP eller HTTPS enligt ditt användningsfall. 

   > [!NOTE]
   > Om du väljer HTTPS behöver du inte ladda upp något autentiseringscertifikat eller ett betrott rotcertifikat för att vitlista apptjänstens serverdel eftersom apptjänsten är en betrodd Azure-tjänst.

4. Markera kryssrutan **Använd för apptjänst** . Observera att `Create a probe with pick host name from backend address` växlarna `Pick host name from backend address` och automatiskt aktiveras.`Pick host name from backend address` åsidosätter värdhuvudet i begäran med värdnamnet för backend när begäran dirigeras från programgatewayen till backend.  

   `Create a probe with pick host name from backend address`skapar automatiskt en hälsoavsökning och associerar den till den här HTTP-inställningen. Du behöver inte skapa någon annan hälsoavsökning för den här HTTP-inställningen. Du kan kontrollera att en <HTTP Setting name> <Unique GUID> ny avsökning med namnet har lagts till i `Pick host name from backend http settings enabled`listan över hälsoavsökningar och att den redan har växeln .

   Om du redan har en eller flera HTTP-inställningar som används för App-tjänsten och om dessa HTTP-inställningar använder samma `Create a probe with pick host name from backend address` protokoll som det du använder i den du skapar, får du i stället för växeln en listruta för att välja en av de anpassade avsökningarna . Detta beror på att eftersom det redan finns en HTTP-inställning med apptjänst, `Pick host name from backend http settings enabled` därför skulle det också finnas en hälsoavsökning som har växeln . Välj den anpassade avsökningen från listrutan.

5. Klicka på **OK** om du vill skapa HTTP-inställningen.

   ![HTTP-inställning1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-inställning2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Skapa regel för att binda lyssnaren, backend-poolen och HTTP-inställningen

1. Klicka på **Grundläggande** under **Regler**om du vill skapa en ny grundregel.

2. Ange ett lämpligt namn och välj lyssnaren som kommer att acceptera inkommande förfrågningar för App-tjänsten.

3. I listrutan **Backend-pool** väljer du den backend-pool som du skapade ovan.

4. I listrutan **HTTP-inställning** väljer du den HTTP-inställning som du skapade ovan.

5. Spara regeln genom att klicka på **OK.**

   ![Regel](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Ytterligare konfiguration vid omdirigering till apptjänstens relativa sökväg

När apptjänsten skickar ett omdirigeringssvar till klienten för att omdirigera till dess relativa sökväg (till exempel en omdirigering från contoso.azurewebsites.net/path1 till contoso.azurewebsites.net/path2) använder den samma värdnamn i platshuvudet för sitt svar som det i begäran som den tog emot från programgatewayen. Så klienten kommer att göra begäran direkt till contoso.azurewebsites.net/path2 istället för att gå igenom programmet gateway (contoso.com/path2). Att kringgå programgatewayen är inte önskvärt.

Om det i ditt användningsfall finns scenarier där App-tjänsten måste skicka ett omdirigeringssvar till klienten, utför [ytterligare steg för att skriva om platshuvudet](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Begränsa åtkomst

De webbappar som distribueras i dessa exempel använder offentliga IP-adresser som kan nås direkt från Internet. Detta hjälper till med felsökning när du lär dig om en ny funktion och provar nya saker. Men om du tänker distribuera en funktion i produktionen, bör du lägga till fler begränsningar.

Ett sätt att begränsa åtkomsten till dina webbappar är att använda [statiska IP-begränsningar för Azure App Service](../app-service/app-service-ip-restrictions.md). Du kan till exempel begränsa webbappen så att den bara tar emot trafik från programgatewayen. Använd ip-begränsningsfunktionen för apptjänsten för att lista programmets gateway VIP som den enda adressen med åtkomst.

## <a name="next-steps"></a>Nästa steg

Mer information om App-tjänsten och annan support för flera innehavare med programgateway finns i [tjänstsupport för flera innehavare med programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
