---
title: Skapa en Azure CDN-slutpunkt | Microsoft Docs
description: Den här artikeln visar hur du skapar en ny slutpunkt för Azure Content Delivery Network (CDN), inklusive avancerade inställningar.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: a3fd85d1dbcba769f47e537e0231f63110752492
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093688"
---
# <a name="create-an-azure-cdn-endpoint"></a>Skapa en Azure CDN-slutpunkt
Den här artikeln beskrivs alla inställningar för att skapa en [Azure Content Delivery Network (CDN)](cdn-overview.md) slutpunkt i en befintlig CDN-profil. När du har skapat en profil och en slutpunkt kan börja du leverera innehåll till dina kunder. En Snabbstart om hur du skapar en profil och slutpunkt finns [Snabbstart: skapa en Azure CDN-profil och slutpunkt](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en CDN-slutpunkt, måste du ha skapat minst en CDN-profil som kan innehålla en eller flera CDN-slutpunkter. Du kan organisera dina CDN-slutpunkter efter internetdomän, webbapp eller något annat kriterium genom att använda flera profiler. Eftersom CDN-priserna tillämpas på CDN-profilnivå, måste du skapa flera CDN-profiler om du vill använda en blandning av Azure CDN-prisnivåer. Om du vill skapa en CDN-profil, [skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen
Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt

1. Gå till din CDN-profil på [Azure-portalen](https://portal.azure.com). Du kanske fäste den på instrumentpanelen i föregående steg. Om inte så kan du hitta den genom att välja **Alla tjänster** och sedan **CDN-profiler**. Välj den profil du vill lägga till på slutpunkten i fönstret **CDN-profiler**. 
   
    Fönstret för CDN-profilen visas.

2. Välj **Slutpunkt**.
   
    ![Välj CDN-slutpunkt](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Sidan **Lägg till en slutpunkt** visas.
   
    ![Lägg till slutpunkt-sida](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. I **Namn** anger du ett unikt namn för den nya CDN-slutpunkten. Det här namnet används för att komma åt dina cachelagrade resurser i domänen _<endpointname>_. azureedge.net.

4. För **ursprungstyp**, väljer du något av följande typer av ursprung: 
   - **Storage** för Azure Storage
   - **Molntjänst** för Azure Cloud Services
   - **Web App** för Azure Web Apps
   - **Anpassat ursprung** för alla andra ursprung offentligt tillgänglig webbserver som (som finns i Azure eller någon annanstans)

5. För **ursprungsvärdnamn**väljer eller anger din ursprungsdomän för servern. Listrutan visar en lista över alla tillgängliga ursprungsservrar av den typ du angav i steg 4. Om du har valt **anpassat ursprung** som typ av ursprung anger du domänen för ditt anpassade ursprung-server.
    
6. För **sökväg till ursprung**, ange sökvägen till de resurser som du vill cachelagra. Lämna inställningen tom om du vill tillåta cachelagring av alla resurser i domänen du angav i steg 5.
    
7. I **Ursprungsvärdadress** anger du värdhuvudet som du vill att Azure CDN ska skicka med varje begäran, eller så lämnar du standardvärdet.
   
   > [!NOTE]
   > Vissa typer av ursprung, till exempel Azure Storage och Web Apps, kräver att värdhuvudet matchar ursprungets domän. Lämna standardvärdet såvida du inte har ett ursprung som kräver ett värdhuvud som skiljer sig från dess domän.
   > 
    
8. För **protokollet** och **ursprungsport**, ange protokoll och portar du använder för att komma åt resurser i den ursprungliga servern. Du måste välja minst ett protokoll (HTTP eller HTTPS). Använd den CDN-definierade domänen (_<endpointname>_. azureedge.net) att komma åt HTTPS-innehåll. 
   
   > [!NOTE]
   > Den **ursprungsport** värdet påverkar bara vilken port slutpunkten använder för att hämta information från den ursprungliga servern. Själva slutpunkten är bara tillgänglig för slutklienter via HTTP- och HTTPS-standardportarna (80 och 443), oavsett värdet för **Ursprungsport**.  
   > 
   > Slutpunkter i **Azure CDN from Akamai**-profiler tillåter inte hela TCP-portintervallet för ursprungsportar. En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > HTTPS-stöd för anpassade Azure CDN-domäner stöds inte på **Azure CDN från Akamai** produkter. Mer information finns i [Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md).
    
9. För **optimerade för**, Välj en typ av lagringsoptimering som bäst passar scenariot och typ av innehåll som du vill att slutpunkten att leverera. Mer information finns i [optimera Azure CDN för typ av innehållsleverans](cdn-optimization-overview.md).

    Följande inställningar för optimering av typen stöds enligt Profiltyp:
    - **Azure CDN Standard från Microsoft** profiler:
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** profiler:
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)
       - [**Acceleration av dynamisk webbplats**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN Standard från Akamai** profiler:
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)
       - [**Allmän mediedirektuppspelning**](cdn-optimization-overview.md#general-media-streaming)
       - [**Video på begäran**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Nedladdning av stora filer**](cdn-optimization-overview.md#large-file-download)
       - [**Acceleration av dynamisk webbplats**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Välj **Lägg till** för att skapa den nya slutpunkten.
   
    När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
    ![CDN-slutpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Slutpunkten kan inte användas direkt, eftersom det tar tid för registreringen att sprida sig: 
    - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom tio minuter. 
    - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
    - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 
   
    Om du försöker använda CDN-domännamnet innan konfigurationen av slutpunkten har spritts till (POP)-servrar point of presence kan du få ett HTTP 404-svarsstatus. Om det har gått flera timmar sedan du skapade slutpunkten och du fortfarande får 404 svarsstatusen, se [felsöka Azure CDN-slutpunkter som returnerar 404-statuskod](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en slutpunkt när den inte längre behövs, markera den och välj sedan **ta bort**. 

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om anpassade domäner kan du fortsätta till självstudien för att lägga till en anpassad domän till CDN-slutpunkten.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](cdn-map-content-to-custom-domain.md)


