---
title: 'Självstudie: Aktivera anpassad domän med SSL på en statisk webbplats med hjälp av Azure CDN – Azure Storage'
description: Lär dig hur du konfigurerar en anpassad domän för hantering av en statisk webbplats.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: a65c0e677182eb224f6bfa7ed834740458b97098
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847000"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Självstudie: Använda Azure CDN för att aktivera en anpassad domän med SSL för en statisk webbplats

Den här självstudien är del två i en serie. Där lär du dig att aktivera en slutpunkt för anpassad domän med SSL för din statiska webbplats. 

Självstudien visar hur du använder [Azure CDN](../../cdn/cdn-overview.md) för att konfigurera slutpunkten för anpassad domän för din statiska webbplats. Med Azure CDN kan du etablera anpassade SSL-certifikat, använda en anpassad domän och konfigurera anpassade omskrivningsregler på samma gång. Konfiguration av Azure CDN resulterar i ytterligare avgifter men ger konsekvent korta svarstider till din webbplats från hela världen. Azure CDN ger även SSL-kryptering med ditt eget certifikat. Information om priser för Azure CDN finns i [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/).

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen
> * Aktivera anpassad domän och SSL

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien slutför du del ett [Självstudie: Hantera en statisk webbplats på Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen

1. Leta upp ditt lagrings konto i Azure Portal och Visa konto översikten.
1. Välj **Azure CDN** under menyn **Blob Service** för att konfigurera Azure CDN.
1. I avsnittet **CDN-profil** anger du en ny eller befintlig CDN-profil. Mer information finns i [ Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](../../cdn/cdn-create-new-endpoint.md).
1. Ange en pris nivå för CDN-slutpunkten. I den här självstudien används pris nivån **standard Akamai** , eftersom den sprids snabbt, vanligt vis inom några minuter. Andra pris nivåer kan ta längre tid att sprida, men de kan även ge andra fördelar. Mer information finns i [jämföra Azure CDN produkt funktioner](../../cdn/cdn-features.md).
1. Ange ett namn på CDN-slutpunkten i fältet **namn på CDN-slutpunkt** . CDN-slutpunkten måste vara unik i Azure.
1. Ange att du är den statiska webbplats slut punkten i fältet **Ursprungligt värdnamn** . Du hittar din statiska webbplats slut punkt genom att navigera till inställningarna för den **statiska webbplatsen** för ditt lagrings konto. Kopiera den primära slut punkten och klistra in den i CDN-konfigurationen, och ta bort protokoll identifieraren (*t. ex.* https).

    Följande bild visar ett exempel på en slut punkts konfiguration:

    ![Skärm bild som visar exempel på CDN-slutpunkts konfiguration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Skapa CDN-slutpunkten och vänta tills den har spridits.
1. Kontrol lera att CDN-slutpunkten är korrekt konfigurerad genom att klicka på slut punkten för att navigera till dess inställningar. Från CDN-översikten för ditt lagrings konto letar du reda på slut punktens värdnamn och navigerar till slut punkten, som du ser i följande bild. Formatet på CDN-slutpunkten ser ut ungefär `https://staticwebsitesamples.azureedge.net`så här.

    ![Skärm bild som visar översikt över CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    När CDN-slutpunktens spridning är klar visar CDN-slutpunkten innehållet i filen index. html som du tidigare laddade upp till den statiska webbplatsen.

1. Om du vill granska ursprungs inställningarna för CDN-slutpunkten navigerar du till **ursprung** under avsnittet **Inställningar** för CDN-slutpunkten. Du ser att fältet **ursprungs typ** är inställt på *anpassat ursprung* och att fältet **Ursprungligt värdnamn** visar din statiska webbplats slut punkt.

    ![Skärm bild som visar ursprungs inställningar för CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aktivera anpassad domän och SSL

1. Skapa en CNAME-post hos din domänregistrator för att omdirigera din anpassade domän till CDN-slutpunkten. CNAME-posten för *www*-underdomänen bör likna följande:

    ![Ange CNAME-post för www-underdomänen](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. I Azure Portal visar du inställningarna för CDN-slutpunkten. Navigera till **anpassade domäner** under **Inställningar** för att konfigurera den anpassade domänen och SSL-certifikatet.
1. Välj **Lägg till anpassad domän**, ange ditt domännamn och klicka sedan på **Lägg till**.
1. Välj den nya anpassade domän mappningen för att etablera ett SSL-certifikat.
1. Ange **anpassad domän-https** till **på**och klicka sedan på **Spara**. Det kan ta flera timmar att konfigurera din anpassade domän. Portalen visar förloppet som visas i följande bild.

    ![Skärm bild som visar förloppet för anpassad domän konfiguration](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Testa mappningen av den statiska webbplatsen till din anpassade domän genom att gå till URL: en för din anpassade domän.

Mer information om hur du aktiverar HTTPS för anpassade domäner finns [i Självstudier: Konfigurera HTTPS på en Azure CDN anpassad domän](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudien lärde du dig att konfigurera en anpassad domän med SSL i Azure CDN för din statiska webbplats.

Mer information om hur du konfigurerar och använder Azure CDN finns i [Vad är Azure CDN?](../../cdn/cdn-overview.md).