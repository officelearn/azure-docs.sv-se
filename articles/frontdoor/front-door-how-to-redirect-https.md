---
title: Skapa en ytterdörr med HTTP till HTTPS-omdirigering med Azure-portalen
description: Lär dig hur du skapar en ytterdörr med omdirigerad trafik från HTTP till HTTPS med Hjälp av Azure-portalen.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: b7385ef27cd17705f2c86b6f57d4780511b6935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246865"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en ytterdörr med HTTP till HTTPS-omdirigering med Azure-portalen

Du kan använda Azure-portalen för att skapa en [ytterdörr](front-door-overview.md) med ett certifikat för SSL-avslutning. En routningsregel används för att omdirigera HTTP-trafik till HTTPS.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en ytterdörr med en befintlig Web App-resurs
> * Lägga till en anpassad domän med SSL-certifikat 
> * Konfigurera HTTPS-omdirigering på den anpassade domänen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Skapa en ytterdörr med en befintlig Web App-resurs

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
3. Sök efter **ytterdörren** med hjälp av sökfältet och när du hittar resurstypen klickar du på **Skapa**.
4. Välj en prenumeration och använd sedan en befintlig resursgrupp eller skapa en ny. Den plats som efterfrågas i användargränssnittet är endast för resursgruppen. Konfigurationen för ytterdörren distribueras på alla [Azure Front Doors POP-platser.](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)

    ![Konfigurera grunderna för nya ytterdörren](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klicka på **Nästa** om du vill ange konfigurationsfliken. Konfigurationen för ytterdörren sker i tre steg - lägga till en standard frontend-värd, lägga till serverdelspool i en serverdelspool och sedan skapa routningsregler för att mappa routningsbeteendet för frontend-värden. 

     ![Konfigurationsdesigner för ytterdörren](./media/front-door-url-redirect/front-door-designer.png)

6. Klicka på**+** ikonen ' ' på _Frontend-värden_ för att skapa en frontend-värd, ange ett`\<**name**\>.azurefd.net`globalt unikt namn för din standard frontend-värd för din ytterdörr ( ). Klicka på **Lägg** till för att gå vidare till nästa steg.

     ![Lägga till en klientdelsvärd](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klicka på**+** ikonen ' ' i _backend-poolerna_ för att skapa en backend-pool. Ange ett namn för backend-poolen och klicka sedan på "**Lägg till en backend**".
8. Välj tjänsten Värd för bakåtsträvning som _app_. Välj den prenumeration där webbappen finns och välj sedan den specifika webbappen i listrutan för **Backend-värdnamnet**.
9. Klicka på **Lägg till** om du vill spara backend och klicka på **Lägg till** igen om du vill spara konfigurationen för backend-poolen.   ![Lägga till en backend i en backend pool](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klicka på**+** ikonen ' ' på _routningsreglerna_ för att skapa en rutt. Ange ett namn för rutten, säg "HttpToHttpsRedirect" och ange sedan fältet _Godkända protokoll_ till **endast HTTP.** Kontrollera att lämplig _klientdelsvärd_ är vald.  
11. I avsnittet _Väginformation_ anger du _vägtypen_ till **Omdirigering**, se till att _omdirigeringstypen_ är inställd **på Found (302)** och _Omdirigeringsprotokollet_ är **inställt på HTTPS endast**. 
12. Klicka på Lägg till om du vill spara routningsregeln för HTTP i HTTPS-omdirigering.
     ![Lägga till en HTTP-omdirigeringsväg för HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Lägg till ytterligare en routningsregel för hantering av HTTPS-trafik. Klicka på**+**' ' sign on the _Routing rules_ and provide a name for the route, say 'DefaultForwardingRoute', and then set the _Accepted Protocols_ field to **'HTTPS only'**. Kontrollera att lämplig _klientdelsvärd_ är vald.
14. I avsnittet Flödesinformation anger du _vägtypen_ till **Framåt**, se till att den högra serverdelspoolen är markerad och att _vidarebefordringsprotokollet_ endast är inställt på **HTTPS**. 
15. Klicka på Lägg till om du vill spara routningsregeln för vidarebefordran av begäran.
     ![Lägga till en framåtväg för HTTPS-trafik](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klicka på **Granska + skapa** och sedan **skapa**, för att skapa din ytterdörr profil. Gå till resursen när den har skapats.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Lägga till en anpassad domän i ytterdörren och aktivera HTTPS på den
Följande steg visar hur du kan lägga till en anpassad domän på en befintlig frontdörrresurs och sedan aktivera HTTP till HTTPS-omdirigering på den. 

### <a name="add-a-custom-domain"></a>Lägga till en anpassad domän

I det här exemplet lägger du `www` till en CNAME-post för underdomänen (till exempel `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Lägg till en CNAME-post för att mappa en underdomän`<name>.azurefd.net`till `<name>` frontluckans standardvärd för klientdel (, där är namnet på din frontdörrsprofil).

För `www.contoso.com` domänen, som ett exempel, lägger du `www` `<name>.azurefd.net`till en CNAME-post som mappar namnet till .

När du har lagt till CNAME ser sidan med DNS-poster ut så här:

![CNAME anpassad domän till ytterdörren](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Ombord på den anpassade domänen på din ytterdörr

1. På fliken Designer på ytterdörren klickar du på ikonen +på frontend-värdavsnittet för att lägga till en ny anpassad domän. 
2. Ange det fullständigt kvalificerade anpassade DNS-namnet i `www.contosonews.com`det anpassade värdnamnsfältet, exempel . 
3. När CNAME-mappningen från domänen till ytterdörren har validerats klickar du på **Lägg** till för att lägga till den anpassade domänen.
4. Klicka på **Spara** om du vill skicka ändringarna.

![Meny för anpassad domän](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din anpassade domän

1. Klicka på den anpassade domänen som lades till och ändra statusen till **Aktiverad**under avsnittet **Anpassad domän HTTPS**.
2. Du kan lämna **certifikathanteringstypen** inställd på _Ytterdörren som hanteras_ för det kostnadsfria certifikat som underhålls, hanteras och roteras automatiskt av ytterdörren. Du kan också välja att använda ditt eget anpassade SSL-certifikat som lagras med Azure Key Vault. Den här självstudien förutsätter att användningen av Front Door hanterat certifikat.
![Aktivera HTTPS för anpassad domän](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klicka på **Uppdatera** för att spara markeringen och klicka sedan på **Spara**.
4. Klicka på **Uppdatera** efter ett par minuter och klicka sedan på den anpassade domänen igen för att se förloppet för certifikatetablering. 

> [!WARNING]
> Det kan ta flera minuter att aktivera HTTPS för en anpassad domän och beror också på `<name>.azurefd.net`validering av domänägarskap om CNAME inte mappas direkt till din dörrvärd . Läs mer om [hur du aktiverar HTTPS för en anpassad domän](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurera routningsreglerna för den anpassade domänen

1. Klicka på den omdirigeringsregel som skapats tidigare.
2. Klicka på listrutan för Frontend värdar och välj din anpassade domän för att tillämpa denna väg för din domän också.
3. Klicka på **Uppdatera**.
4. Gör samma åtgärd för den andra routningsregeln också, för att vidarebefordringsvägen ska lägga till den anpassade domänen.
5. Klicka på **Spara** om du vill skicka in ändringarna.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Läs mer om [URL-omdirigering på Ytterdörren](front-door-url-redirect.md).
