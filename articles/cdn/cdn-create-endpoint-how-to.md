---
title: Skapa en Azure CDN-slutpunkt | Microsoft-dokument
description: Den här artikeln visar hur du skapar en ny CDN-slutpunkt (Azure Content Delivery Network), inklusive avancerade inställningar.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: afd9599dd1e396dea6975c397688e4b97371fd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593937"
---
# <a name="create-an-azure-cdn-endpoint"></a>Skapa en Azure CDN-slutpunkt
I den här artikeln beskrivs alla inställningar för att skapa en [CDN-slutpunkt (Azure Content Delivery Network)](cdn-overview.md) i en befintlig CDN-profil. När du har skapat en profil och en slutpunkt kan du börja leverera innehåll till dina kunder. En snabbstart för att skapa en profil och slutpunkt finns i [Snabbstart: Skapa en Azure CDN-profil och slutpunkt](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Krav
Innan du kan skapa en CDN-slutpunkt måste du ha skapat minst en CDN-profil, som kan innehålla en eller flera CDN-slutpunkter. Du kan organisera dina CDN-slutpunkter efter internetdomän, webbapp eller något annat kriterium genom att använda flera profiler. Eftersom CDN-priser tillämpas på CDN-profilnivå måste du skapa flera CDN-profiler om du vill använda en blandning av Azure CDN-prisnivåer. En ny CDN-profil finns i [Skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-a-new-cdn-endpoint"></a>Skapa en ny CDN-slutpunkt

1. Gå till din CDN-profil på [Azure-portalen](https://portal.azure.com). Du kanske fäste den på instrumentpanelen i föregående steg. Om inte så kan du hitta den genom att välja **Alla tjänster** och sedan **CDN-profiler**. Välj den profil du vill lägga till på slutpunkten i fönstret **CDN-profiler**. 
   
    Fönstret för CDN-profilen visas.

2. Välj **Slutpunkt**.
   
    ![CDN-välj slutpunkt](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Sidan **Lägg till en slutpunkt** visas.
   
    ![Lägg till slutpunktssida](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. I **Namn** anger du ett unikt namn för den nya CDN-slutpunkten. Det här namnet används för att komma åt cachelagrade resurser i _ \<domänslutpunktsnamnet>_.azureedge.net.

4. För **Origin-typ**väljer du en av följande ursprungstyper: 
   - **Lagring** för Azure Storage
   - **Molntjänst** för Azure Cloud Services
   - **Webbappar** för Azure
   - **Anpassat ursprung** för alla andra allmänt tillgängliga ursprungswebbserver (finns i Azure eller någon annanstans)

5. För **Origin-värdnamn**väljer eller anger du din ursprungsserverdomän. Listrutan visar alla tillgängliga ursprungsservrar av den typ som du angav i steg 4. Om du har valt **Anpassat ursprung** som ursprungstyp anger du domänen för den anpassade ursprungsservern.
    
6. För **Origin-sökvägen**anger du sökvägen till de resurser som du vill cachelagra. Om du vill tillåta cachelagring av resurser i den domän som du angav i steg 5 lämnar du den här inställningen tom.
    
7. I **Ursprungsvärdadress** anger du värdhuvudet som du vill att Azure CDN ska skicka med varje begäran, eller så lämnar du standardvärdet.
   
   > [!NOTE]
   > Vissa typer av ursprung, till exempel Azure Storage och Web Apps, kräver att värdhuvudet matchar ursprungets domän. Lämna standardvärdet såvida du inte har ett ursprung som kräver ett värdhuvud som skiljer sig från dess domän.
   > 
    
8. För **protokoll-** och **Origin-port anger**du de protokoll och portar som ska användas för att komma åt dina resurser på ursprungsservern. Du måste välja minst ett protokoll (HTTP eller HTTPS). Använd den CDN-angivna domänen_\<(slutpunktsnamn>_.azureedge.net) för att komma åt HTTPS-innehåll. 
   
   > [!NOTE]
   > **Ursprungsportvärdet** bestämmer bara den port som slutpunkten använder för att hämta information från ursprungsservern. Själva slutpunkten är bara tillgänglig för slutklienter via HTTP- och HTTPS-standardportarna (80 och 443), oavsett värdet för **Ursprungsport**.  
   > 
   > Slutpunkter i **Azure CDN from Akamai**-profiler tillåter inte hela TCP-portintervallet för ursprungsportar. En lista över ursprungsportar som inte tillåts finns i [Azure CDN från Akamai-tillåtna ursprungsportar](/previous-versions/azure/mt757337(v=azure.100)).  
   > 
   > HTTPS-stöd för Azure CDN-anpassade domäner stöds inte på **Azure CDN från Akamai-produkter.** Mer information finns i [Konfigurera HTTPS på en anpassad Azure CDN-domän](cdn-custom-ssl.md).
    
9. För **Optimerad för**väljer du en optimeringstyp som bäst matchar scenariot och typen av innehåll som du vill att slutpunkten ska leverera. Mer information finns i [Optimera Azure CDN för typ av innehållsleverans](cdn-optimization-overview.md).

    Följande inställningar för optimeringstyp stöds enligt profiltyp:
    - **Azure CDN Standard från** Microsoft-profiler:
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN Standard från Verizon** och **Azure CDN Premium från** Verizon-profiler:
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dynamisk webbplatsacceleration**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN Standard från Akamai-profiler:**
       - [**Allmän webbleverans**](cdn-optimization-overview.md#general-web-delivery)
       - [**Allmän medieströmning**](cdn-optimization-overview.md#general-media-streaming)
       - [**Video på begäran media streaming**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Stor fil nedladdning**](cdn-optimization-overview.md#large-file-download)
       - [**Dynamisk webbplatsacceleration**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Välj **Lägg till** för att skapa den nya slutpunkten.
   
    När slutpunkten har skapats, visas den i en lista över slutpunkter för profilen.
    
    ![CDN-slutpunkt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Slutpunkten kan inte användas direkt, eftersom det tar tid för registreringen att sprida sig: 
    - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
    - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
    - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 90 minuter. 
   
    Om du försöker använda CDN-domännamnet innan slutpunktskonfigurationen har spridits till POP-servrarna (point-of-presence) kan du få en HTTP 404-svarsstatus. Om det har gått flera timmar sedan du skapade slutpunkten och du fortfarande får 404 svarsstatus läser du [Felsöka Azure CDN-slutpunkter som returnerar en 404-statuskod](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort en slutpunkt när den inte längre behövs markerar du den och väljer sedan **Ta bort**. 

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om anpassade domäner fortsätter du till självstudien för att lägga till en anpassad domän i CDN-slutpunkten.

> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](cdn-map-content-to-custom-domain.md)


