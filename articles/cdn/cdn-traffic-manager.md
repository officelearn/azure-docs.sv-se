---
title: Konfigurera redundans över flera Azure CDN-slutpunkter med Azure Traffic Manager | Microsoft Docs
description: Läs mer om hur du ställer in Azure Traffic Manager med Azure CDN-slutpunkter.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 4c072ef63c0d4961fba695fc8d9be1d12b4b0e8b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749222"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Konfigurera redundans över flera Azure CDN-slutpunkter med Azure Traffic Manager

När du konfigurerar Azure Content Delivery Network (CDN) kan välja du optimala providern och prisnivå för dina behov. Azure CDN skapar med dess globalt distribuerad infrastruktur som standard lokala och geografisk redundans och global belastningsbalansering för att förbättra tjänstens tillgänglighet och prestanda. Om en plats inte är tillgänglig för att hantera innehåll begäranden dirigeras automatiskt till en annan plats och den optimala POP-plats (baserat på sådana faktorer som belastning för begäranden plats och server) används för att hantera varje klientbegäran. 
 
Om du har flera CDN-profiler, kan du ytterligare förbättra tillgängligheten och prestanda med Azure Traffic Manager. Du kan använda Azure Traffic Manager med Azure CDN för att belastningsutjämna mellan flera CDN-slutpunkter för växling vid fel, geo-load belastningsutjämning och andra scenarier. I en typisk redundansscenario dirigeras först alla klientbegäranden till den primära CDN-profilen. Om profilen inte är tillgänglig skickas begäranden till den sekundära CDN-profilen förrän dina primära CDN-profil är online igen. Med Azure Traffic Manager på så sätt ser du till ditt webbprogram alltid är tillgänglig. 

Den här artikeln innehåller vägledning och ett exempel på hur du ställer in redundans med **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler.

## <a name="set-up-azure-cdn"></a>Konfigurera Azure CDN 
Skapa två eller flera Azure CDN-profiler och slutpunkter med olika leverantörer.

1. Skapa en **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profilen genom att följa stegen i [skapa en ny CDN-profil](cdn-create-new-endpoint.md#create-a-new-cdn-profile).
 
   ![CDN flera profiler](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. Skapa minst en slutpunkt i var och en av de nya profilerna genom att följa stegen i [skapa en ny CDN-slutpunkt](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).

## <a name="set-up-azure-traffic-manager"></a>Konfigurera Azure Traffic Manager
Skapa en Azure Traffic Manager-profil och ställer in belastningsutjämningen i din CDN-slutpunkter. 

1. Skapa en Azure Traffic Manager-profil genom att följa stegen i [skapa en Traffic Manager-profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile). 

    För **routningsmetod**väljer **prioritet**.

2. Lägg till din CDN-slutpunkter i Traffic Manager-profilen genom att följa stegen i [lägga till Traffic Manager-slutpunkter](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)

    För **typ**väljer **externa slutpunkter**. För **prioritet**, ange ett tal.

    Till exempel skapa *cdndemo101akamai.azureedge.net* med en prioritet på *1* och *cdndemo101verizon.azureedge.net* med en prioritet på *2*.

   ![CDN traffic manager-slutpunkter](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Ställ in anpassad domän för Azure CDN och Azure Traffic Manager
När du ställer in din CDN- och Traffic Manager-profiler, följer du stegen nedan för att lägga till DNS-mappningen och registrera anpassad domän till CDN-slutpunkter. Det här exemplet är det anpassade domännamnet *cdndemo101.dustydogpetcare.online*.

1. Gå till webbplatsen för domänleverantören för din anpassade domän, till exempel GoDaddy, och skapar två DNS CNAME-poster. 

    a. Mappa din anpassade domän med cdnverify-underdomänen för den första CNAME-posten i CDN-slutpunkten. Den här posten är ett obligatoriskt steg för att registrera den anpassade domänen till CDN-slutpunkten som du lade till Traffic Manager i steg 2.

      Exempel: 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. Mappa din domän, utan underdomänen cdnverify i CDN-slutpunkten för den andra CNAME-posten. Den här posten mappar den anpassade domänen till Traffic Manager. 

      Exempel: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > Om din domän är för tillfället aktiv och kan inte avbrytas, gör du det här steget senast. Kontrollera att CDN-slutpunkter och domäner för traffic manager är aktiv innan du uppdaterar din anpassade domän DNS till Traffic Manager.
    >


2.  Azure CDN-profilen, Välj den första CDN-slutpunkten (Akamai). Välj **Lägg till anpassad domän** och inkommande *cdndemo101akamai.azureedge.net*. Kontrollera att bockmarkeringen för att verifiera den anpassade domänen är grönt. 

    Azure CDN använder den *cdnverify* underdomänen för att verifiera DNS-mappningen för att slutföra registreringsprocessen. Mer information finns i [skapa en CNAME DNS-post](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record). Det här steget aktiverar Azure CDN för att identifiera den anpassade domänen så att de kan svara på begäranden.

3.  Gå tillbaka till webbplatsen för domänleverantören för din anpassade domän och uppdatera den första DNS-mappning som du skapade i så att den anpassade domänen har mappats till andra CDN-slutpunkten.
                             
    Exempel: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Välj andra CDN-slutpunkten (Verizon) från Azure CDN-profilen, och upprepa steg 2. Välj **Lägg till anpassad domän**, och indata *cdndemo101akamai.azureedge.net*.
 
När du har slutfört de här stegen har din multi-CDN-tjänst med funktioner för redundans ställts in med Azure Traffic Manager. Du kommer att kunna komma åt testet URL: er från din anpassade domän. Testa funktionen genom att inaktivera primära CDN-slutpunkten och verifiera att begäran korrekt flyttas över till den sekundära CDN-slutpunkten. 

## <a name="next-steps"></a>Nästa steg
Du kan också ställa in andra metoder för trafikroutning: till exempel geografiska att balansera belastningen mellan olika CDN-slutpunkter. Mer information finns i [konfigurera geografisk routningsmetod som med Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).



