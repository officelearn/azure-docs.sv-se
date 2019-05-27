---
title: 'Självstudie: Aktivera anpassad domän med SSL på en statisk webbplats med hjälp av Azure CDN – Azure Storage'
description: Lär dig hur du konfigurerar en anpassad domän för hantering av en statisk webbplats.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226117"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Självstudie: Använda Azure CDN för att aktivera en anpassad domän med SSL för en statisk webbplats

Den här självstudien är del två i en serie. Där lär du dig att aktivera en slutpunkt för anpassad domän med SSL för din statiska webbplats. 

Självstudien visar hur du använder [Azure CDN](../../cdn/cdn-overview.md) för att konfigurera slutpunkten för anpassad domän för din statiska webbplats. Med Azure CDN kan du etablera anpassade SSL-certifikat, använda en anpassad domän och konfigurera anpassade omskrivningsregler på samma gång. Konfiguration av Azure CDN resulterar i ytterligare avgifter men ger konsekvent korta svarstider till din webbplats från hela världen. Azure CDN ger även SSL-kryptering med ditt eget certifikat. Information om priser för Azure CDN finns i [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/).

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen
> * Aktivera anpassad domän och SSL

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar med den här självstudien slutför du del ett [Självstudie: Hantera en statisk webbplats på Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen

1. Leta upp ditt storage-konto i Azure-portalen och visa översikten konto.
1. Välj **Azure CDN** under menyn **Blob Service** för att konfigurera Azure CDN.
1. I den **CDN-profil** anger du en ny eller befintlig CDN-profil. Mer information finns i [ Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](../../cdn/cdn-create-new-endpoint.md).
1. Ange en prisnivå för CDN-slutpunkten. Den här självstudien används den **Standard Akamai** prisnivå, eftersom det sprids snabbt, vanligtvis inom några minuter. Andra prisnivåerna kan ta längre tid att sprida, men kan också erbjuda andra fördelar. Mer information finns i [jämför Azure CDN produktfunktioner](../../cdn/cdn-features.md).
1. I den **CDN-slutpunktnamn** fältet, ange ett namn för CDN-slutpunkten. CDN-slutpunkten måste vara unikt inom Azure.
1. Ange nu webbplatsslutpunkt i den **ursprungsvärdnamn** fält. Om du vill hitta din slutpunkt för statisk webbplats, navigerar du till den **statisk webbplats** inställningar för ditt lagringskonto. Kopiera den primära slutpunkten och klistra in den i CDN-konfiguration, ta bort protokoll-ID (*t.ex.* , HTTPS).

    Följande bild visar en exempelkonfiguration för slutpunkten:

    ![Skärmbild som visar exempel CDN slutpunktskonfiguration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Skapa CDN-slutpunkten och vänta tills den har spridits.
1. Kontrollera att CDN-slutpunkten är korrekt konfigurerad, klicka på på slutpunkten för att navigera till dess inställningar. Leta upp slutpunktens värdnamn från CDN-översikt för ditt lagringskonto och navigera till slutpunkten som visas i följande bild. Formatet för CDN-slutpunkten ska vara detsamma som `https://staticwebsitesamples.azureedge.net`.

    ![Skärmbild som visar översikt över CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    När CDN-slutpunkten spridningen är klar, visar gå till CDN-slutpunkten innehållet i den index.html-fil som du tidigare har överförts till din statiska webbplats.

1. Om du vill granska inställningarna för ursprunget för CDN-slutpunkten, gå till **ursprung** under den **inställningar** för CDN-slutpunkten. Visas som den **ursprungstyp** anges till *anpassat ursprung* och att den **ursprungsvärdnamn** fält visar dina webbplatsslutpunkt.

    ![Skärmbild som visar ursprung inställningar för CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aktivera anpassad domän och SSL

1. Skapa en CNAME-post hos din domänregistrator för att omdirigera din anpassade domän till CDN-slutpunkten. CNAME-posten för *www*-underdomänen bör likna följande:

    ![Ange CNAME-post för www-underdomänen](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Visa inställningarna för CDN-slutpunkten i Azure-portalen. Gå till **anpassade domäner** under **inställningar** att konfigurera anpassade domäner och SSL-certifikatet.
1. Välj **Lägg till anpassad domän**, ange ditt domännamn och klicka sedan på **Lägg till**.
1. Välj den nya anpassade domänmappning att etablera ett SSL-certifikat.
1. Ange **anpassad domän HTTPS** till **på**, klicka sedan på **spara**. Det kan ta flera timmar att konfigurera din anpassade domän. Portalen visar förloppet som visas i följande bild.

    ![Skärmbild som visar förloppet för konfiguration av anpassad domän](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Testa mappning av din statiska webbplats till din anpassade domän genom att gå till URL: en för din anpassade domän.

Mer information om hur du aktiverar HTTPS för egna domäner finns i [självstudien: Konfigurera HTTPS på en anpassad Azure CDN-domän](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudien lärde du dig att konfigurera en anpassad domän med SSL i Azure CDN för din statiska webbplats.

Mer information om att konfigurera och använda Azure CDN finns i [vad är Azure CDN?](../../cdn/cdn-overview.md).