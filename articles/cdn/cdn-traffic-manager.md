---
title: Redundans över flera Azure CDN-slutpunkter med Traffic Manager
description: Lär dig mer om hur du konfigurerar Azure Traffic Manager med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 3d4f77a0fb9b8005729a6e9d35f254eb522b690e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259858"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurera redundans över flera Azure CDN-slutpunkter med Azure Traffic Manager

När du konfigurerar CDN (Azure Content Delivery Network) kan du välja den optimala leverantörs- och prisnivån för dina behov. Azure CDN, med sin globalt distribuerade infrastruktur, skapar som standard lokal och geografisk redundans och global belastningsutjämning för att förbättra tjänstens tillgänglighet och prestanda. Om en plats inte är tillgänglig för att betjäna innehåll dirigeras begäranden automatiskt till en annan plats och den optimala POP-funktionen (baserat på sådana faktorer som begärandeplats och serverbelastning) används för att betjäna varje klientbegäran. 
 
Om du har flera CDN-profiler kan du ytterligare förbättra tillgängligheten och prestandan med Azure Traffic Manager. Du kan använda Azure Traffic Manager med Azure CDN för att läsa in aldot mellan flera CDN-slutpunkter för redundans, geobelastningsutjämning och andra scenarier. I ett typiskt redundansscenario dirigeras alla klientbegäranden först till den primära CDN-profilen. Om profilen inte är tillgänglig skickas begäranden sedan till den sekundära CDN-profilen tills din primära CDN-profil är online igen. Om du använder Azure Traffic Manager på det här sättet säkerställer du att ditt webbprogram alltid är tillgängligt. 

Den här artikeln innehåller vägledning och ett exempel på hur du konfigurerar redundans med **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler.**

## <a name="set-up-azure-cdn"></a>Konfigurera Azure CDN 
Skapa två eller flera Azure CDN-profiler och slutpunkter med olika leverantörer.

1. Skapa en **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profilen** genom att följa stegen i [Skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN flera profiler](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Skapa minst en slutpunkt i var och en av de nya profilerna genom att följa stegen i [Skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurera Azure Traffic Manager
Skapa en Azure Traffic Manager-profil och konfigurera belastningsutjämning över cdn-slutpunkterna. 

1. Skapa en Azure Traffic Manager-profil genom att följa stegen i [Skapa en Traffic Manager-profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    För **routningsmetod**väljer du **Prioritet**.

2. Lägga till CDN-slutpunkter i Traffic Manager-profilen genom att följa stegen i [Lägg till Traffic Manager-slutpunkter](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    För **Text**väljer du **Externa slutpunkter**. För **Prioritet**anger du ett tal.

    Skapa till exempel *cdndemo101akamai.azureedge.net* med *prioriteten 1* och *cdndemo101verizon.azureedge.net* med prioritet *2*.

   ![Slutpunkter för CDN-trafikhanteraren](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurera anpassad domän på Azure CDN och Azure Traffic Manager
När du har konfigurerat CDN- och Traffic Manager-profilerna följer du dessa steg för att lägga till DNS-mappning och registrera anpassad domän till CDN-slutpunkterna. I det här exemplet är det anpassade domännamnet *cdndemo101.dustydogpetcare.online*.

1. Gå till webbplatsen för domänleverantören för din anpassade domän, till exempel GoDaddy, och skapa två DNS CNAME-poster. 

    a. För den första CNAME-posten mappar du din anpassade domän, med cdnverify-underdomänen, till CDN-slutpunkten. Den här posten är ett obligatoriskt steg för att registrera den anpassade domänen till CDN-slutpunkten som du lade till i Traffic Manager i steg 2.

      Ett exempel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. För den andra CNAME-posten mappar du din anpassade domän, utan cdnverify-underdomänen, till CDN-slutpunkten. Den här posten mappar den anpassade domänen till Traffic Manager. 

      Ett exempel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Om din domän är live och inte kan avbrytas gör du det här steget sist. Kontrollera att CDN-slutpunkterna och trafikhanterare domänerna är aktiva innan du uppdaterar din anpassade domän DNS till Traffic Manager.
    >


2.  Välj den första CDN-slutpunkten (Akamai) från din Azure CDN-profil. Välj **Lägg till anpassad domän** och ingång *cdndemo101.dustydogpetcare.online*. Kontrollera att bocken för att validera den anpassade domänen är grön. 

    Azure CDN använder underdomänen *cdnverify* för att validera DNS-mappningen för att slutföra den här registreringsprocessen. Mer information finns i [Skapa en CNAME DNS-post](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Det här steget gör det möjligt för Azure CDN att känna igen den anpassade domänen så att den kan svara på sina begäranden.
    
    > [!NOTE]
    > Om du vill aktivera TLS på en **Azure CDN från Akamai-profiler** måste du direkt cname den anpassade domänen till din slutpunkt. cdnverify för att aktivera TLS stöds ännu inte. 
    >

3.  Gå tillbaka till webbplatsen för domänleverantören för din anpassade domän och uppdatera den första DNS-mappningen som du skapade i så att den anpassade domänen mappas till din andra CDN-slutpunkt.
                             
    Ett exempel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Välj den andra CDN-slutpunkten (Verizon) och upprepa steg 2 i din Azure CDN-profil. Välj **Lägg till anpassad domän**och mata in *cdndemo101.dustydogpetcare.online*.
 
När du har slutfört de här stegen konfigureras din multi-CDN-tjänst med redundansfunktioner med Azure Traffic Manager. Du kan komma åt test-URL:erna från den anpassade domänen. Om du vill testa funktionen inaktiverar du den primära CDN-slutpunkten och kontrollerar att begäran flyttas över till den sekundära CDN-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
Du kan också ställa in andra routningsmetoder, till exempel geografiska, för att balansera belastningen mellan olika CDN-slutpunkter. Mer information finns i [Konfigurera den geografiska trafikroutningsmetoden med Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



