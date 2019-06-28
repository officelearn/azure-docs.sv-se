---
title: Skapa en ytterdörren med HTTP till HTTPS-omdirigering i Azure Portal
description: Lär dig hur du skapar en ytterdörren med omdirigerade trafik från HTTP till HTTPS med Azure-portalen.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332955"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en ytterdörren med HTTP till HTTPS-omdirigering i Azure Portal

Du kan använda Azure-portalen för att skapa en [ytterdörren](front-door-overview.md) med ett certifikat för SSL-avslutning. En routningsregel används för att omdirigera HTTP-trafik till HTTPS.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en ytterdörren med en befintlig Webbapp-resurs
> * Lägg till en anpassad domän med SSL-certifikat 
> * Konfigurera HTTPS-omdirigering på den anpassade domänen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Skapa en ytterdörren med en befintlig Webbapp-resurs

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
3. Sök efter **ytterdörren** med hjälp av sökfunktionen och när du har hittat resurstypen klickar du på **skapa**.
4. Välj en prenumeration och Använd en befintlig resursgrupp eller skapa en ny. Observera att platsen och svar i Användargränssnittet är för resursgruppen bara. Konfigurationen ytterdörren kommer att distribueras på alla [POP-platser för Azure Front dörren](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Konfigurera grundläggande inställningar för nya ytterdörren](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klicka på **nästa** ange konfigurationsfliken. Konfiguration för ytterdörren sker i tre steg – lägga till en värd för standard-klientdel, lägga till serverdelar i en serverdelspool och sedan skapa routningsregler för att mappa routning beteendet för frontend-värd. 

     ![Ytterdörren configuration designer](./media/front-door-url-redirect/front-door-designer.png)

6. Klickar du på den ” **+** ”-ikonen i den _klientdel värdar_ för att skapa en frontend-värd, kan du ange ett globalt unikt namn för standardvärd för klientdelen för ytterdörren (`\<**name**\>.azurefd.net`). Klicka på **Lägg till** för att gå vidare till nästa steg.

     ![Lägger till en frontend-värd](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klicka på den ' **+** ”-ikonen i den _serverdelspooler_ att skapa en serverdelspool. Ange ett namn för serverdelspoolen och klicka sedan på ”**lägga till en serverdel**'.
8. Välj typ för Backend-värd som _apptjänst_. Välj den prenumeration där webbappen finns och välj sedan de specifika webbappen i listrutan för **serverdel värdnamn**.
9. Klicka på **Lägg till** att spara serverdelen och klicka på **Lägg till** igen för att spara konfigurationen för backend-poolen.   ![Lägga till en serverdel i en serverdelspool](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klicka på den ” **+** ”-ikonen i den _regler för routning_ att skapa en väg. Ange ett namn för flödet, säg ”HttpToHttpsRedirect” och sedan ange den _godkänt protokoll_ fältet **”HTTP”** . Se till att rätt _klientdel värden_ har valts.  
11. På den _flödesinformation_ anger den _Vägtyp_ till **omdirigera**, se till att den _omdirigera typ_ är inställd på  **Hittades (302)** och _omdirigera protokollet_ är inställd på **endast HTTPS**. 
12. Klicka på Lägg till om du vill spara en routningsregel för HTTP till HTTPS-omdirigering.
     ![Lägg till en HTTP till HTTPS omdirigerings-flöde](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Lägg till en annan regel för vidarebefordran för hantering av HTTPS-trafik. Klickar du på den ' **+** ”logga in i _regler för routning_ och ange ett namn för flödet, säg” DefaultForwardingRoute ”och ange sedan den _godkänt protokoll_ fält att **”endast HTTPS-** . Se till att rätt _klientdel värden_ har valts.
14. I avsnittet flödesinformation ange den _Vägtyp_ till **framåt**, kontrollera att rätt serverdelspoolen är valt och _vidarebefordran protokollet_ är inställd på  **Endast HTTPS**. 
15. Klicka på Lägg till om du vill spara en routningsregel för vidarebefordran för begäran.
     ![Lägg till en vanlig väg för HTTPS-trafik](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klicka på **granska + skapa** och sedan **skapa**, för att skapa din ytterdörren-profil. Gå till resursen efter att de skapats.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Lägg till en anpassad domän till dörren och aktivera HTTPS på den
Följande steg visar hur du kan lägga till en anpassad domän på en befintlig ytterdörren resurs och sedan aktivera HTTP till HTTPS-omdirigering på den. 

### <a name="add-a-custom-domain"></a>Lägga till en anpassad domän

I det här exemplet kan du lägga till en CNAME-post för den `www` underdomän (till exempel `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Lägg till en CNAME-post för att mappa en underdomän till din ytterdörren standardvärden för klientdelen (`<name>.azurefd.net`, där `<name>` är namnet på profilen ytterdörren).

För den `www.contoso.com` domän, till exempel lägga till en CNAME-post som mappar namnet `www` till `<name>.azurefd.net`.

När du har lagt till CNAME ser sidan med DNS-poster ut så här:

![CNAME anpassad domän till ytterdörren](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Publicera den anpassade domänen på ytterdörren

1. På fliken ytterdörren designer klickar du på ”+”-ikonen i Frontend-värdarna avsnittet för att lägga till en ny anpassad domän. 
2. Ange det fullständigt kvalificerade DNS-namnet i namnfältet på anpassade värden exempel `www.contosonews.com`. 
3. När CNAME-mappningen från domänen till dörren har verifierats klickar du på **Lägg till** att lägga till den anpassade domänen.
4. Klicka på **spara** att skicka ändringarna.

![Meny för anpassad domän](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din domän

1. Klicka på den anpassade domänen som har lagts till och under avsnittet **HTTPS för anpassad domän**, ändrar du statusen till **aktiverad**.
2. Du kan lämna den **certifikattyp management** inställd _ytterdörren hanteras_ kostnadsfria certifikatet underhålls, hanterade, och autorotated av ytterdörren. Du kan också välja att använda ditt eget anpassade SSL-certifikat som lagras med Azure Key Vault. Den här självstudien förutsätter att användningen av ytterdörren hanteras certifikat.
![Aktivera HTTPS för anpassad domän](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klicka på **uppdatering** spara markeringen och klicka sedan på **spara**.
4. Klicka på **uppdatera** efter ett par minuter och sedan klicka på den anpassade domänen igen för att se förloppet för tilldelning av certifikat. 

> [!WARNING]
> Att aktivera HTTPS för en anpassad domän kan ta flera minuter och beror också på att verifiera domänen ägarskap om CNAME inte mappas direkt till din ytterdörren värd `<name>.azurefd.net`. Läs mer om [aktivera HTTPS för en anpassad domän](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurera regler för vidarebefordran för den anpassade domänen

1. Klicka på en routningsregel för omdirigering som skapades tidigare.
2. Klicka på listrutan för Frontend-värdar och välj din anpassade domän att tillämpa den här vägen för din domän samt.
3. Klicka på **Uppdatera**.
4. Gör på samma gång för andra routingregeln samt det vill säga för din vägen för vidarebefordringen att lägga till den anpassade domänen.
5. Klicka på **spara** att skicka ändringarna.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Läs mer om [URL-omdirigering på ytterdörren](front-door-url-redirect.md).
