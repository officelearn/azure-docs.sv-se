---
title: Redundans över flera Azure CDN slut punkter med Traffic Manager
description: Lär dig hur du konfigurerar Azure-Traffic Manager med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: de91f61385942db077bc98721eabe9f3f0b8624c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083007"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurera redundans över flera Azure CDN slut punkter med Azure Traffic Manager

När du konfigurerar Azure Content Delivery Network (CDN) kan du välja den optimala providern och pris nivån för dina behov. Azure CDN, med globalt distribuerad infrastruktur, skapar som standard lokal och geografisk redundans och global belastnings utjämning för att förbättra tjänstens tillgänglighet och prestanda. Om en plats inte är tillgänglig för att hantera innehåll dirigeras begär Anden automatiskt till en annan plats och optimal POP (baserat på sådana faktorer som begär ande plats och server belastning) används för att betjäna varje klientbegäran. 
 
Om du har flera CDN-profiler kan du ytterligare förbättra tillgängligheten och prestandan med Azure Traffic Manager. Du kan använda Azure Traffic Manager med Azure CDN för att belastningsutjämna mellan flera CDN-slutpunkter för redundansväxling, belastnings utjämning och andra scenarier. I ett typiskt failover-scenario dirigeras alla klient begär Anden först till den primära CDN-profilen. om profilen inte är tillgänglig skickas begär anden till den sekundära CDN-profilen tills din primära CDN-profil är online igen. Genom att använda Azure Traffic Manager på det här sättet ser du till att ditt webb program alltid är tillgängligt. 

Den här artikeln innehåller vägledning och ett exempel på hur du ställer in redundans med **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler.

## <a name="set-up-azure-cdn"></a>Konfigurera Azure CDN 
Skapa två eller fler Azure CDN profiler och slut punkter med olika leverantörer.

1. Skapa en **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profil genom att följa stegen i [skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN flera profiler](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. I var och en av de nya profilerna skapar du minst en slut punkt genom att följa stegen i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurera Azure-Traffic Manager
Skapa en Azure Traffic Manager-profil och konfigurera belastnings utjämning för dina CDN-slutpunkter. 

1. Skapa en Azure Traffic Manager-profil genom att följa stegen i [skapa en Traffic Manager profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    För **routningsmetod**väljer du **prioritet**.

2. Lägg till dina CDN-slutpunkter i din Traffic Manager profil genom att följa stegen i [Lägg till Traffic Manager slut punkter](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    I **typ**väljer du **externa slut punkter**. Ange en siffra som **prioritet**.

    Skapa till exempel *cdndemo101akamai.azureedge.net* med prioritet *1* och *cdndemo101verizon.azureedge.net* med prioritet *2*.

   ![CDN Traffic Manager-slutpunkter](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Konfigurera en anpassad domän på Azure CDN och Azure Traffic Manager
När du har konfigurerat ditt CDN och Traffic Manager profiler, följer du dessa steg för att lägga till DNS-mappning och registrera anpassad domän i CDN-slutpunkterna. I det här exemplet är det anpassade domän namnet *cdndemo101. dustydogpetcare. online*.

1. Gå till webbplatsen för domän leverantören för din anpassade domän, till exempel GoDaddy, och skapa två CNAME-poster för DNS. 

    a. För den första CNAME-posten mappar du din anpassade domän med under domänen cdnverify till din CDN-slutpunkt. Posten är ett obligatoriskt steg för att registrera den anpassade domänen till den CDN-slutpunkt som du lade till i Traffic Manager i steg 2.

      Exempel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. För den andra CNAME-posten mappar du din anpassade domän utan cdnverify-underdomänen till CDN-slutpunkten. Den här posten mappar den anpassade domänen till Traffic Manager. 

      Exempel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Om din domän är Live och inte kan avbrytas, gör du det här steget sist. Kontrol lera att CDN-slutpunkterna och Traffic Manager-domänerna är aktiva innan du uppdaterar din anpassade domän-DNS till Traffic Manager.
    >


2.  Välj den första CDN-slutpunkten (Akamai) från din Azure CDN profil. Välj **Lägg till anpassad domän** och indatamängd *cdndemo101. dustydogpetcare. online*. Kontrol lera att kryss rutan kontrol lera att den anpassade domänen är grön. 

    Azure CDN använder under domänen *cdnverify* för att verifiera DNS-mappningen för att slutföra den här registrerings processen. Mer information finns i [skapa en CNAME DNS-post](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Det här steget gör det möjligt Azure CDN att identifiera den anpassade domänen så att den kan svara på begär Anden.
    
    > [!NOTE]
    > Om du vill aktivera SSL på en **Azure CDN från Akamai** -profiler måste du direkt CNAME den anpassade domänen till din slut punkt. cdnverify för att aktivera SSL stöds inte ännu. 
    >

3.  Gå tillbaka till webbplatsen för domän leverantören för din anpassade domän och uppdatera den första DNS-mappningen som du skapade i så att den anpassade domänen mappas till din andra CDN-slutpunkt.
                             
    Exempel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Välj den andra CDN-slutpunkten (Verizon) från din Azure CDN profil och upprepa steg 2. Välj **Lägg till anpassad domän**och mata in *cdndemo101. dustydogpetcare. online*.
 
När du har slutfört de här stegen konfigureras tjänsten multi-CDN med funktioner för redundans med Azure Traffic Manager. Du kommer att kunna komma åt test-URL: erna från din anpassade domän. Om du vill testa funktionen inaktiverar du den primära CDN-slutpunkten och kontrollerar att begäran har flyttats till den sekundära CDN-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
Du kan också ställa in andra routningsmetoder, till exempel geografisk, för att utjämna belastningen mellan olika CDN-slutpunkter. Mer information finns i [Konfigurera metoden för att dirigera geografisk trafik med hjälp av Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



