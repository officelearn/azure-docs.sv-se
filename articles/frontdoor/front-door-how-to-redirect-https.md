---
title: Skapa en frontend-dörr med HTTP till HTTPS-omdirigering med hjälp av Azure Portal
description: Lär dig hur du skapar en frontend-dörr med Omdirigerad trafik från HTTP till HTTPS med hjälp av Azure Portal.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399698"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en frontend-dörr med HTTP till HTTPS-omdirigering med hjälp av Azure Portal

Du kan använda Azure Portal för att skapa en [frontend-dörr](front-door-overview.md) med ett certifikat för TLS-avslutning. En regel för routning används för att omdirigera HTTP-trafik till HTTPS.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en frontend-dörr med en befintlig Web App-resurs
> * Lägg till en anpassad domän med TLS/SSL-certifikat 
> * Konfigurera HTTPS-omdirigering på den anpassade domänen

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Skapa en frontend-dörr med en befintlig Web App-resurs

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
3. Sök efter **front dörr** med Sök fältet och när du har hittat resurs typen klickar du på **skapa**.
4. Välj en prenumeration och Använd antingen en befintlig resurs grupp eller skapa en ny. Observera att den plats som visas i användar gränssnittet bara är resurs gruppen. Konfigurationen av din front dörr kommer att distribueras över alla [pop-platser i Azures front dörr](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Konfigurera grunderna för ny front dörr](./media/front-door-url-redirect/front-door-create-basics.png)

5. Klicka på **Nästa** för att ange fliken konfiguration. Konfigurationen för front dörren sker i tre steg – lägga till en standard klient dels värd, lägga till Server delar i en backend-pool och sedan skapa routningsregler för att mappa routnings beteendet för klient dels värden. 

     ![Konfigurations design för front dörr](./media/front-door-url-redirect/front-door-designer.png)

6. Klicka på **+** ikonen på _klient dels_ värdarna för att skapa en klient dels värd, ange ett globalt unikt namn för din standard klient dels värd för din front dörr ( `\<**name**\>.azurefd.net` ). Klicka på **Lägg till** för att fortsätta till nästa steg.

     ![Lägg till en klient dels värd](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Klicka på **+** ikonen på _backend-poolerna_ för att skapa en backend-pool. Ange ett namn för backend-poolen och klicka sedan på**Lägg till en server**del.
8. Välj server dels värd typen som _App Service_. Välj den prenumeration där din webbapp finns och välj sedan den aktuella webbappen i list rutan för **Server dels värdens namn**.
9. Klicka på **Lägg** till för att spara Server delen och klicka på **Lägg till** igen för att spara konfiguration av backend-poolen.   ![Lägg till en server del i en backend-pool](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Klicka på **+** ikonen i _routningsregler_ för att skapa en väg. Ange ett namn för vägen, säg "HttpToHttpsRedirect" och ange sedan fältet _accepterade protokoll_ till **"endast http"**. Kontrol lera att rätt _klient dels värd_ är markerad.  
11. I avsnittet _flödes information_ anger du vilken typ av _routning_ som ska **omdirigeras**, se till att _omdirigerings typen_ är inställd på **found (302)** och _omdirigerings protokollet_ är inställt på **endast https**. 
12. Klicka på Lägg till för att spara regeln för omdirigering av HTTP till HTTPS.
     ![Lägg till en HTTP till HTTPS-Omdirigerad väg](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Lägg till en annan routningsprincip för hantering av HTTPS-trafiken. Klicka på **+** signerings _reglerna_ för "" och ange ett namn för vägen, säg "DefaultForwardingRoute" och ange sedan fältet _accepterade protokoll_ till **"endast https"**. Kontrol lera att rätt _klient dels värd_ är markerad.
14. I avsnittet flödes information anger du vilken _typ av routning_ som ska **vidarebefordras**, se till att rätt backend-pool är markerad och att _vidarebefordrings protokollet_ är inställt på **endast https**. 
15. Klicka på Lägg till för att spara regeln för vidarebefordran av förfrågningar.
     ![Lägg till en vidarebefordrande väg för HTTPS-trafik](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Klicka på **Granska + skapa** och **skapa**för att skapa din profil för din klient. Gå till resursen när den har skapats.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Lägg till en anpassad domän i din frontend-dörr och Aktivera HTTPS på den
Följande steg visar hur du kan lägga till en anpassad domän på en befintlig resurs för en front dörr och sedan Aktivera HTTP till HTTPS-omdirigering. 

### <a name="add-a-custom-domain"></a>Lägga till en anpassad domän

I det här exemplet lägger du till en CNAME-post för under `www` domänen (till exempel `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Lägg till en CNAME-post för att mappa en under domän till din frontend-standardklient dels värd ( `<name>.azurefd.net` , där `<name>` är namnet på din profil för din front dörr).

För `www.contoso.com` domänen, till exempel, lägger du till en CNAME-post som mappar namnet `www` till `<name>.azurefd.net` .

När du har lagt till CNAME ser sidan med DNS-poster ut så här:

![CNAME-anpassad domän till front dörren](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Publicera den anpassade domänen på din front dörr

1. På fliken front dörr designer klickar du på ikonen "+" i avsnittet klient dels värdar för att lägga till en ny anpassad domän. 
2. Ange det fullständigt kvalificerade DNS-namnet i fältet namn på anpassad värd, exempel `www.contosonews.com` . 
3. När CNAME-mappningen från domänen till din front dörr har verifierats klickar du på **Lägg till** för att lägga till den anpassade domänen.
4. Klicka på **Spara** för att skicka ändringarna.

![Meny för anpassad domän](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Aktivera HTTPS på din anpassade domän

1. Klicka på den anpassade domänen som lades till och under avsnittet **anpassad https för domän**ändrar du status till **aktive rad**.
2. Du kan lämna **certifikat hanterings typen** inställd på _front dörren som hanteras_ för det kostnads fria certifikatet som underhålls, hanteras och autoroteras av front dörren. Du kan också välja att använda ett eget anpassat TLS/SSL-certifikat som lagras med Azure Key Vault. I den här självstudien förutsätter vi att du använder certifikat med front dörr som hanteras.
![Aktivera HTTPS för anpassad domän](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Klicka på **Uppdatera** för att spara valet och klicka sedan på **Spara**.
4. Klicka på **Uppdatera** efter några minuter och klicka sedan på den anpassade domänen igen för att se förloppet för certifikat etableringen. 

> [!WARNING]
> Det kan ta flera minuter att aktivera HTTPS för en anpassad domän, och det beror också på verifiering av domän ägarskap om CNAME-filen inte är direkt mappad till värddatorn för din klient `<name>.azurefd.net` . Läs mer om [hur du aktiverar HTTPS för en anpassad domän](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Konfigurera routningsregler för den anpassade domänen

1. Klicka på regeln för routning av omdirigering som skapades tidigare.
2. Klicka på list rutan för klient dels värdar och välj den anpassade domän som du vill använda den här vägen för din domän också.
3. Klicka på **Uppdatera**.
4. Utför samma åtgärd för den andra regeln för routning, så att du kan lägga till den anpassade domänen för vägen för vidarebefordran.
5. Klicka på **Spara** för att skicka ändringarna.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Läs mer om [URL-omdirigering på front dörren](front-door-url-redirect.md).
