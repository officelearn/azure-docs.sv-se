---
title: Självstudiekurs – Lägga till en anpassad domän i Azure CDN-slutpunkten | Microsoft Docs
description: I den här kursen mappar du Azure CDN-slutpunktsinnehåll till en anpassad domän.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d20dca9a1248cfb2915ebbc456fba80aadcf81f5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196512"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>Självstudiekurs: Lägga till en anpassad domän i Azure CDN-slutpunkten
Den här självstudien visar hur du lägger till en anpassad domän till en slutpunkt i Azure Content Delivery Network (CDN). När du använder en CDN-slutpunkt för att leverera innehåll behövs en anpassad domän om du vill att ditt eget domännamn ska synas i din URL för CDN. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte. 

När du har skapat en CDN-slutpunkt i din profil inkluderas slutpunktsnamnet (det vill säga en underdomän till azureedge.net) i URL för leverans av CDN-innehåll som standard (till exempel https:\//contoso.azureedge.net/photo.png). Med Azure CDN kan du associera en anpassad domän med en CDN-slutpunkt. På så sätt kan du leverera ditt innehåll med en anpassad domän i din URL i stället för slutpunktsnamnet (till exempel https:\//www.contoso.com/photo.png). 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med CDN-slutpunkten
> - Verifiera den anpassade domänen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan slutföra stegen i den här kursen måste du först skapa en CDN-profil och minst en CDN-slutpunkt. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](cdn-create-new-endpoint.md).

Om du inte redan har en anpassad domän måste du först köpa en sådan av en domänleverantör. Se exempelvis [Köpa ett anpassat domännamn](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).

Om du använder Azure som värd för dina [DNS-domäner](https://docs.microsoft.com/azure/dns/dns-overview) måste du delegera domänleverantörens DNS till en Azure DNS. Mer information finns i [Delegera en domän till Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Om du i stället använder en domänleverantör för att hantera din DNS-domän fortsätter du till [Skapa en CNAME DNS-post](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Skapa en CNAME DNS-post

Innan du kan använda en anpassad domän med en Azure CDN-slutpunkt måste du först skapa en post för kanoniskt namn (CNAME) med din domänleverantör för att peka till din CDN-slutpunkt. En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn. För Azure CDN är källdomännamnet namnet på din anpassade domän och måldomännamnet är CDN-slutpunktens värdnamn. När Azure CDN har verifierat CNAME-posten du skapade dirigeras den trafik som går till den anpassade källdomänen (till exempel www.contoso.com) till CDN-slutpunktens värdnamn (till exempel contoso.azureedge.net). 

En anpassad domän och dess underdomän kan endast associeras med en slutpunkt åt gången. Men du kan använda olika underdomäner från samma anpassade domän för olika Azure-tjänstslutpunkter genom att använda flera CNAME-poster. Du kan också mappa en anpassad domän med olika underdomäner till samma CDN-slutpunkt.


## <a name="map-the-temporary-cdnverify-subdomain"></a>Mappa den tillfälliga underdomänen cdnverify

När du mappar en befintlig domän som är i produktion finns några saker att tänka på. Medan du registrerar din anpassade domän i Azure Portal kan det uppstå en kort tids driftstopp. För att undvika störningar i webbtrafiken mappar du först din anpassade domän till CDN-slutpunktens värdnamn med Azure cdnverify-underdomänen för att skapa en tillfällig CNAME-mappning. Den här metoden gör att användare kan komma åt din domän utan avbrott under tiden DNS-mappningen utförs. 

Om du använder din anpassade domän för första gången och ingen produktionstrafik körs på den, kan du mappa din anpassade domän direkt till CDN-slutpunkten. Gå vidare till [Mappa permanent anpassad domän](#map-permanent-custom-domain).

Skapa en CNAME-post med underdomänen cdnverify:

1. Logga in på webbplatsen för domänleverantören för din anpassade domän.

2. Leta reda på sidan för att hantera DNS-poster med hjälp av leverantörens dokumentation eller genom att söka på webbplatsen efter platser som **domännamn**, **DNS** eller **namnserverhantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa                    | Typ  | Mål                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Källa: Ange ditt anpassade domännamn, inklusive underdomänen cdnverify i följande format: cdnverify._&lt;anpassat domännamn&gt;. Till exempel cdnverify.www.contoso.com.

    - Typ: Ange *CNAME*.

    - Mål: Ange CDN-slutpunktsnamnet, inklusive underdomänen cdnverify i följande format: cdnverify._&lt;slutpunktsnamn&gt;_.azureedge.net. Till exempel cdnverify.contoso.azureedge.net.

4. Spara ändringarna.

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i följande fält för CNAME-posten:

    ![CNAME entry (CNAME-post)](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - Type (Typ): Använd det förvalda värdet *CNAME*.

    - Host (Värd): Ange underdomänen för din anpassade domän, inklusive underdomännamnet cdnverify. Till exempel cdnverify.www.

    - Points to (Pekar på): Ange värdnamnet för CDN-slutpunkten, inklusive underdomännamnet cdnverify. Till exempel cdnverify.contoso.azureedge.net. 

    - TTL: Använd det förvalda värdet *1 Hour (1 Timme)*.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.

    ![Tabell med DNS-poster](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Associera den anpassade domänen med CDN-slutpunkten

När du har registrerat din anpassade domän kan du lägga till den i din CDN-slutpunkt. 

1. Logga in på [Azure Portal](https://portal.azure.com/) och gå till den CDN-profil som innehåller den slutpunkt som du vill mappa till en anpassad domän.
    
2. På sidan **CDN-profil** väljer du den CDN-slutpunkt som du vill associera med den anpassade domänen.

   Sidan **Slutpunkt** öppnas.
    
3. Välj **Egen domän**. 

   ![Knapp för anpassad CDN-domän](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   Sidan **Lägg till en anpassad domän** öppnas.

4. För **Värdnamn för slutpunkt** är det värdnamn för slutpunkt som ska användas som måldomän för din CNAME-post ifyllt och hämtas från din URL för CDN-slutpunkt: *&lt;värdnamn för slutpunkt&gt;*.azureedge.net. Det kan inte ändras.

5. För **Anpassat värdnamn** anger du din anpassade domän, inklusive underdomänen, som ska användas som källdomän för din CNAME-post. Till exempel www.contoso.com eller cdn.contoso.com. Använd inte underdomännamnet cdnverify.

   ![Dialogrutan för anpassad CDN-domän](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. Välj **Lägg till**.

   Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän. 

   Det kan ta lite tid innan inställningarna för den nya anpassade domänen sprids till alla CDN-gränsnoder: 
    - För **Azure CDN Standard från Microsoft**-profiler är spridningen vanligtvis klar inom 10 minuter. 
    - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
    - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter.   


## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har slutfört registreringen av den anpassade domänen kan du kontrollera att den anpassade domänen refererar till CDN-slutpunkten.
 
1. Se till att du har offentligt innehåll som cachelagras på slutpunkten. Om CDN-slutpunkten till exempel är associerad med ett lagringskonto cachelagrar Azure CDN innehållet i en offentlig behållare. Om du vill testa den anpassade domänen kontrollerar du att behållaren är inställd på att tillåta offentlig åtkomst och att den innehåller minst en fil.

2. Navigera till filens adress genom att använda den anpassade domänen i webbläsaren. Om din anpassade domän till exempel är cdn.contoso.com ska URL:en till den cachelagrade filen se ut ungefär som följande URL: http:\//cdn.contoso.com/my-public-container/my-file.jpg. Kontrollera att resultatet är detsamma som när du öppnar CDN-slutpunkten direkt på *&lt;värdnamn för slutpunkt&gt;*.azureedge.net.


## <a name="map-the-permanent-custom-domain"></a>Mappa den permanenta anpassade domänen

Om du har kontrollerat att underdomänen cdnverify har mappats till slutpunkten (eller om du använder en ny anpassad domän som inte är i produktion) kan du mappa den anpassade domänen direkt till CDN-slutpunktens värdnamn.

Skapa en CNAME-post för den anpassade domänen:

1. Logga in på webbplatsen för domänleverantören för din anpassade domän.

2. Leta reda på sidan för att hantera DNS-poster med hjälp av leverantörens dokumentation eller sök efter områden på webbplatsen som heter **Domännamn**, **DNS** eller **Namnserverhantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa          | Typ  | Mål           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Källa: Ange ett anpassat domännamn (till exempel www.contoso.com).

    - Typ: Ange *CNAME*.

    - Mål: Ange CDN-slutpunktens värdnamn. Det måste vara i följande format:_&lt;slutpunktsnamnet&gt;_.azureedge.net. Till exempel contoso.azureedge.net.

4. Spara ändringarna.

5. Om du tidigare skapade en tillfällig CNAME-post för underdomänen cdnverify tar du bort den. 

6. Om du använder den här anpassade domänen i produktion för första gången följer du anvisningarna under [Associera en anpassad domän med CDN-slutpunkten](#associate-the-custom-domain-with-your-cdn-endpoint) och [Verifiera den anpassade domänen](#verify-the-custom-domain).

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i fälten för CNAME-posten:

    ![CNAME entry (CNAME-post)](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - Type (Typ): Använd det förvalda värdet *CNAME*.

    - Host (Värd): Ange underdomänen för den anpassade domänen. Till exempel www eller cdn.

    - Points to (Pekar på): Ange värdnamnet för CDN-slutpunkten. Till exempel contoso.azureedge.net. 

    - TTL: Använd det förvalda värdet *1 Hour (1 Timme)*.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.

    ![Tabell med DNS-poster](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. Om du har en CNAME-post för underdomänen cdnverify väljer du pennikonen bredvid den och sedan papperskorgsikonen.

8. Välj **Ta bort** för att ta bort CNAME-posten.


## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg lade du till en anpassad domän i en CDN-slutpunkt. Om du inte längre vill associera slutpunkten med en anpassad domän kan du ta bort den anpassade domänen med följande steg:
 
1. I din CDN-profil väljer du slutpunkten med den anpassade domän som du vill ta bort.

2. På sidan **Slutpunkt** högerklickar du på den anpassade domänen som du vill ta bort under Anpassade domäner. Välj sedan **Ta bort** på snabbmenyn.  

   Den anpassade domänen kopplas bort från slutpunkten.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med CDN-slutpunkten
> - Verifiera den anpassade domänen

Gå vidare till nästa kurs om du vill lära dig hur du konfigurerar HTTPS på en anpassad Azure CDN-domän.

> [!div class="nextstepaction"]
> [Självstudiekurs: Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md)


