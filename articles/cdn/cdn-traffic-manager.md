---
title: Konfigurera redundans över flera Azure CDN-slutpunkter med Azure Traffic Manager | Microsoft Docs
description: Lär dig mer om hur du ställer in Azure Traffic Manager med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: b52cad1f32cc3d16cf70bb81640dcb1d9f8614bf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133043"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurera redundans över flera Azure CDN-slutpunkter med Azure Traffic Manager

När du konfigurerar Azure Content Delivery Network (CDN) måste välja du optimala providern och prisnivå för dina behov. Azure CDN skapar med dess globalt distribuerad infrastruktur som standard lokala och geografisk redundans och globala belastningsutjämning för att förbättra tjänstetillgänglighet och prestanda. Om en plats inte är tillgänglig för att hantera innehåll begäranden dirigeras automatiskt till en annan plats och optimala POP (baserat på faktorer som belastningen för plats- och server) används för att hantera varje klientbegäran. 
 
Om du har flera CDN-profiler kan du ytterligare förbättra tillgänglighet och prestanda med Azure Traffic Manager. Du kan belastningsutjämna mellan flera CDN-slutpunkter för växling vid fel, geo belastningen belastningsutjämning och andra scenarier genom att använda Azure Traffic Manager med Azure CDN. I ett scenario med vanliga redundans dirigeras först alla klientbegäranden till den primära CDN-profilen. Om profilen inte är tillgänglig, skickas begäranden till den sekundära CDN-profilen tills primära CDN-profilen är tillbaka online. Med hjälp av Azure Traffic Manager på så sätt säkerställer du ditt webbprogram alltid är tillgängligt. 

Den här artikeln innehåller vägledning och ett exempel på hur du ställer in redundans med **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler.

## <a name="set-up-azure-cdn"></a>Konfigurera Azure CDN 
Skapa två eller flera Azure CDN profiler och slutpunkter med olika providrar.

1. Skapa en **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profilen genom att följa stegen i [skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN flera profiler](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Skapa minst en slutpunkt i var och en av de nya profilerna genom att följa stegen i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurera Azure Traffic Manager
Skapa en Azure Traffic Manager-profil och konfigurera belastningsutjämning över dina CDN-slutpunkter. 

1. Skapa en Azure Traffic Manager-profil genom att följa stegen i [skapa en trafikhanterarprofil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1). 

    För **routningsmetod**väljer **prioritet**.

2. Lägga till dina CDN-slutpunkter i Traffic Manager-profilen genom att följa stegen i [lägga till Traffic Manager-slutpunkter](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    För **typen**väljer **externa slutpunkter**. För **prioritet**, ange ett tal.

    Till exempel skapa *cdndemo101akamai.azureedge.net* med prioritet *1* och *cdndemo101verizon.azureedge.net* med prioritet *2*.

   ![CDN traffic manager-slutpunkter](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurera anpassade domäner på Azure CDN och Azure Traffic Manager
När du har skapat din CDN- och Traffic Manager-profiler, Följ dessa steg om du vill lägga till DNS-mappning och registrera anpassade domäner till CDN-slutpunkter. Det här exemplet är det anpassade domännamnet *cdndemo101.dustydogpetcare.online*.

1. Gå till webbplatsen för domän-providern för din domän, till exempel GoDaddy, och skapa två DNS CNAME-poster. 

    a. Posten CNAME mappa i din domän, med underdomänen cdnverify till CDN-slutpunkten. Den här posten är ett steg som krävs för att registrera den anpassade domänen som du lade till i Traffic Manager i steg 2 CDN-slutpunkten.

      Exempel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Mappa dina anpassade domäner utan underdomänen cdnverify till CDN-slutpunkten för den andra CNAME-posten. Den här posten mappar den anpassade domänen i Traffic Manager. 

      Exempel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Om din domän är för närvarande är aktiv och kan inte avbrytas, gör du det här steget senast. Kontrollera att CDN-slutpunkter och traffic manager-domäner är live innan du uppdaterar din anpassade domän DNS i Traffic Manager.
    >


2.  Välj den första CDN-slutpunkten (Akamai) från Azure CDN-profilen. Välj **Lägg till anpassad domän** och inkommande *cdndemo101akamai.azureedge.net*. Kontrollera att markeringen att verifiera den anpassade domänen är grön. 

    Azure CDN använder den *cdnverify* underdomän att verifiera DNS-mappning för att slutföra registreringsprocessen. Mer information finns i [skapa en CNAME DNS-post](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Det här steget aktiverar Azure CDN att identifiera den anpassade domänen så att den kan svara på begäranden.

3.  Gå tillbaka till webbplatsen för domän-providern på din domän och uppdatera den första DNS-mappning som du skapade i så att den anpassade domänen är mappad till andra CDN-slutpunkten.
                             
    Exempel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Välj andra CDN-slutpunkten (Verizon) från Azure CDN-profilen, och upprepa steg 2. Välj **Lägg till anpassad domän**, och inkommande *cdndemo101akamai.azureedge.net*.
 
När du har slutfört de här stegen har multi-CDN-tjänsten med funktioner för redundans ställts in med Azure Traffic Manager. Du kommer att kunna komma åt testet URL från domänen. Om du vill testa funktionen för att inaktivera primära CDN-slutpunkten och verifiera att begäran ska flyttas över till den sekundära CDN-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
Du kan också konfigurera andra metoder, exempelvis geografiska för belastningsutjämning bland annat CDN-slutpunkter. Mer information finns i [konfigurera geografiska trafikroutningsmetod med Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



