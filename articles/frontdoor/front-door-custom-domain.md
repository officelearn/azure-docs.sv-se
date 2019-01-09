---
title: Självstudiekurs – Lägga till en anpassad domän i Azure Front Door-konfigurationen | Microsoft Docs
description: I den här självstudien får du lära dig hur du implementerar en anpassad domän i Azure Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 58829bcd1b3c38b70929167beae5d8866483d616
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716505"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Självstudier: Lägg till en anpassad domän i din Front Door
I den här självstudien får du lära dig hur du lägger till en anpassad domän i Front Door. När du använder Azure Front Door-tjänsten för programleverans behövs en anpassad domän om du vill att ditt eget domännamn ska synas i din slutanvändarbegäran. Att ha ett synligt domännamn kan vara praktiskt för dina kunder och användbart i profileringssyfte.

När du har skapat en Front Door ingår standardklientdelsvärden, som är en underdomän till `azurefd.net`, i URL:en för att leverera Front Door-innehåll från serverdelen som standard (till exempel https:\//contoso.azurefd.net/activeusers.htm). Av bekvämlighetsskäl tillhandahåller Azure Front Door möjligheten att associera en anpassad domän med standardvärden. På så sätt kan du leverera ditt innehåll med en anpassad domän i din URL i stället för ett Front Door-ägt domännamn (till exempel https:\//www.contoso.com/photo.png). 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med din Front Door.
> - Verifiera den anpassade domänen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan slutföra stegen i den här kursen behöver du skapa en Front Door. Mer information finns i [ Snabbstart: Skapa en Front Door](quickstart-create-front-door.md).

Om du inte redan har en anpassad domän måste du först köpa en sådan av en domänleverantör. Se exempelvis [Köpa ett anpassat domännamn](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Om du använder Azure som värd för dina [DNS-domäner](https://docs.microsoft.com/azure/dns/dns-overview) måste du delegera domänleverantörens DNS till en Azure DNS. Mer information finns i [Delegera en domän till Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). Om du i stället använder en domänleverantör för att hantera din DNS-domän fortsätter du till [Skapa en CNAME DNS-post](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Skapa en CNAME DNS-post

Innan du kan använda en anpassad domän med din Front Door behöver du skapa en CNAME-post (kanoniskt namn) med din domänleverantör som pekar till din Front Doors standardklientdelsvärd (som contose.azurefd.net). En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn. För Azure Front Door-tjänsten är källdomännamnet namnet på din anpassade domän och måldomännamnet är värdnamnet för Front Door-slutpunkten. När Front Door har verifierat CNAME-posten du skapade dirigeras den trafik som går till den anpassade källdomänen (till exempel www.contoso.com) till den angivna standardklientdelsvärden för Front Door (som contoso.azurefd.net). 

En anpassad domän och dess underdomän kan endast associeras med en Front Door åt gången. Du kan däremot använda olika underdomäner från samma anpassade domän för olika Front Door-tjänster genom att använda flera CNAME-poster. Du kan också mappa en anpassad domän med olika underdomäner till samma Front Door.


## <a name="map-the-temporary-afdverify-sub-domain"></a>Mappa den tillfälliga underdomänen afdverify

När du mappar en befintlig domän som är i produktion finns några saker att tänka på. Medan du registrerar din anpassade domän i Azure Portal kan det uppstå en kort tids driftstopp. För att undvika störningar i webbtrafiken mappar du först din anpassade domän till standardklientdelsvärden för Front Door med Azure-underdomänen afdverify för att skapa en tillfällig CNAME-mappning. Den här metoden gör att användare kan komma åt din domän utan avbrott under tiden DNS-mappningen utförs.

Om du använder din anpassade domän för första gången och ingen produktionstrafik körs på den, kan du mappa din anpassade domän direkt till din Front Door. Gå vidare till [Mappa permanent anpassad domän](#map-the-permanent-custom-domain).

Så här skapar du en CNAME-post med underdomänen afdverify:

1. Logga in på webbplatsen för domänleverantören för den anpassade domänen.

2. Leta reda på sidan för att hantera DNS-poster med hjälp av leverantörens dokumentation eller genom att söka på webbplatsen efter platser som **domännamn**, **DNS** eller **namnserverhantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa                    | Typ  | Mål                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Källa: Ange ditt anpassade domännamn, inklusive underdomänen afdverify, i följande format: afdverify._&lt;anpassat domännamn&gt;_. Till exempel: afdverify.www.contoso.com.

    - Ange: Ange *CNAME*.

    - Mål: Ange standardklientdelsvärden för Front Door, inklusive underdomänen afdverify, i följande format: afdverify._&lt;slutpunktsnamn&gt;_.azurefd.net. Till exempel: afdverify.contoso.azurefd.net.

4. Spara ändringarna.

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i följande fält för CNAME-posten:

    - Ange: Låt *CNAME* vara markerat.

    - Värd: Ange underdomänen för den anpassade domän som du vill använda, inklusive underdomännamnet afdverify. Till exempel: afdverify.www.

    - Pekar mot: Ange värdnamnet för standardklientdelsvärden för Front Door, inklusive underdomänsnamnet afdverify. Till exempel: afdverify.contoso.azurefd.net. 

    - TTL: Låt *1 timme* vara markerat.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Associera den anpassade domänen med din Front Door

När du har registrerat din anpassade domän kan du lägga till den i din Front Door.

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till den Front Door som innehåller klientdelsvärden som du vill mappa till en anpassad domän.
    
2. På sidan **Front Door-designer** klickar du på ”+” för att lägga till en anpassad domän.
    
3. Ange **Anpassad domän**. 

4. För **Klientdelsvärd** är klientdelsvärden som ska användas som måldomän för din CNAME-post redan ifylld och den härleds från din Front Door: *&lt;standardvärdnamn&gt;*.azurefd.NET. Det kan inte ändras.

5. För **Anpassat värdnamn** anger du din anpassade domän, inklusive underdomänen, som ska användas som källdomän för din CNAME-post. Till exempel www.contoso.com eller cdn.contoso.com. Använd inte underdomännamnet afdverify.

6. Välj **Lägg till**.

   Azure verifierar att det finns en CNAME-post för det anpassade domännamnet som du har angett. Om CNAME är korrekt verifieras din anpassade domän.

>[!WARNING]
> Du **måste** se till att alla klientdelsvärdar (inklusive anpassade domäner) i din Front Door har en routningsregel med en associerad standardsökväg ('/\*'). Det betyder att för alla dina routningsregler måste det finnas minst en routningsregel för var och en av dina klientdelsvärdar som har definierats vid standardsökvägen ('/\*'). Om du inte gör det kan det resultera i att slutanvändartrafiken inte dirigeras korrekt.

## <a name="verify-the-custom-domain"></a>Verifiera den anpassade domänen

När du har slutfört registreringen av den anpassade domänen kontrollerar du att den anpassade domänen refererar till Front Door-klientdelsvärden.
 
Navigera till filens adress genom att använda den anpassade domänen i webbläsaren. Om din anpassade domän till exempel är robotics.contoso.com ska URL:en till den cachelagrade filen se ut ungefär som följande URL: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Kontrollera att resultatet är detsamma som när du öppnar Front Door direkt genom *&lt;Front Door-värden&gt;*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mappa den permanenta anpassade domänen

Om du har kontrollerat att underdomänen afdverify har mappats till din Front Door (eller om du använder en ny anpassad domän som inte är i produktion) kan du mappa den anpassade domänen direkt till standardklientdelsvärden för Front Door.

Skapa en CNAME-post för den anpassade domänen:

1. Logga in på webbplatsen för domänleverantören för den anpassade domänen.

2. Leta reda på sidan för att hantera DNS-poster med hjälp av leverantörens dokumentation eller sök efter områden på webbplatsen som heter **Domännamn**, **DNS** eller **Namnserverhantering**. 

3. Skapa en CNAME-post för din anpassade domän och fyll i fälten enligt tabellen nedan (fältnamnen kan variera):

    | Källa          | Typ  | Mål           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azurefd.net |

    - Källa: Ange ditt anpassade domännamn (till exempel www.contoso.com).

    - Ange: Ange *CNAME*.

    - Mål: Ange standardklientdelsvärden för din Front Door. Följande format måste användas:_&lt;värdnamn&gt;_.azurefd.net. Till exempel: contoso.azurefd.net.

4. Spara ändringarna.

5. Om du tidigare skapat en tillfällig CNAME-post för underdomänen afdverify tar du bort den. 

6. Om du använder den här anpassade domänen i produktion för första gången följer du anvisningarna under [Associera den anpassade domänen med din Front Door](#associate-the-custom-domain-with-your-front-door) och [Verifiera den anpassade domänen](#verify-the-custom-domain).

Till exempel är förfarandet för GoDaddy-domänregistratorn följande:

1. Logga in och välj den anpassade domänen som du vill använda.

2. Välj **Manage All** (Hantera alla) i avsnittet för domäner och välj sedan **DNS** | **Manage Zones** (Hantera zoner).

3. Ange din anpassade domän i **Domain Name** (Domännamn) och välj sedan **Search** (Sök).

4. På sidan **DNS Management** (DNS-hantering) väljer du **Add** (Lägg till ) och sedan **CNAME** i listan **Type** (Typ).

5. Fyll i fälten för CNAME-posten:

    - Ange: Låt *CNAME* vara markerat.

    - Värd: Ange underdomänen för den anpassade domän som du vill använda. Till exempel: www eller profil.

    - Pekar mot: Ange standardvärdnamnet för din Front Door. Till exempel: contoso.azurefd.net. 

    - TTL: Låt *1 timme* vara markerat.

6. Välj **Spara**.
 
    CNAME-posten läggs till i tabellen med DNS-poster.

7. Om du har en CNAME-post för afdverify väljer du pennikonen bredvid den och sedan papperskorgsikonen.

8. Välj **Ta bort** för att ta bort CNAME-posten.


## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg lade du till en anpassad domän i en Front Door. Om du inte längre vill associera din Front Door med en anpassad domän kan du ta bort den anpassade domänen med följande steg:
 
1. I din Front Door-designer väljer du den anpassade domänen som du vill ta bort.

2. Klicka på Ta bort på snabbmenyn för den anpassade domänen.  

   Den anpassade domänen kopplas bort från slutpunkten.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> - Skapa en CNAME DNS-post
> - Associera den anpassade domänen med din Front Door.
> - Verifiera den anpassade domänen