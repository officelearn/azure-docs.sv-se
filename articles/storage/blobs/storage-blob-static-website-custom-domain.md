---
title: 'Självstudie: Aktivera anpassad domän med SSL på en statisk webbplats med hjälp av Azure CDN – Azure Storage'
description: Lär dig hur du konfigurerar en anpassad domän för hantering av en statisk webbplats.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110383"
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

1. Öppna [Azure-portalen](https://portal.azure.com/) i webbläsaren. 
1. Leta upp ditt lagringskonto och visa kontoöversikten.
1. Välj **Azure CDN** under menyn **Blob Service** för att konfigurera Azure CDN.
1. I avsnittet **Ny slutpunkt** fyller du i fälten för att skapa en ny CDN-slutpunkt.
1. Ange ett slutpunktsnamn, till exempel *mystaticwebsiteCDN*.
1. Ange din webbplatsdomän som värdnamnet för CDN-slutpunkten.
1. För ursprungets värdnamn anger du slutpunkten för den statiska webbplatsen. Du hittar slutpunkten för din statiska webbplats genom att gå till avsnittet **Statisk webbplats** för ditt lagringskonto och kopiera slutpunkten. 
1. Testa CDN-slutpunkten genom att gå till *mywebsitecdn.azureedge.net* i webbläsaren.

## <a name="enable-custom-domain-and-ssl"></a>Aktivera anpassad domän och SSL

1. Skapa en CNAME-post hos din domänregistrator för att omdirigera din anpassade domän till CDN-slutpunkten. CNAME-posten för *www*-underdomänen bör likna följande:

    ![Ange CNAME-post för www-underdomänen](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. På Azure-portalen klickar du på den nyligen skapade slutpunkten för att konfigurera den anpassade domänen och SSL-certifikatet.
1. Välj **Lägg till anpassad domän**, ange ditt domännamn och klicka sedan på **Lägg till**.
1. Välj den nyligen skapade mappningen för den anpassade domänen för att etablera ett SSL-certifikat.
1. Ange **HTTPS för anpassad domän** till **PÅ**. Välj **CDN-hanterat** för att låta Azure CDN hantera SSL-certifikatet. Klicka på **Spara**.
1. Testa webbplatsen genom att gå till din webbplatsens URL.

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudien lärde du dig att konfigurera en anpassad domän med SSL i Azure CDN för din statiska webbplats.

Följ den här länken om du vill veta mer om hantering av statiska webbplatser i Azure Storage.

> [!div class="nextstepaction"]
> [Lär mer om statiska webbplatser](storage-blob-static-website.md)
