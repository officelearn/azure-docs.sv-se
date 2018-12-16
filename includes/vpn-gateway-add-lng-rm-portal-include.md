---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444392"
---
1. Klicka på **+Lägg till** från **Alla resurser** i portalen.
2. I den **allt** sidan Sök-rutan, skriver du in **lokal nätverksgateway**, och sedan klicka för att returnera en lista över resurser. Klicka på **Lokal nätverksgateway** för att öppna sidan och klicka sedan på **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

   ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   - **Namn:** Ange ett namn för din lokala gateway-objekt. Använd om möjligt något intuitiva, till exempel **ClassicVNetLocal** eller **TestVNet1Local**. Detta gör det enklare för dig att identifiera den lokala nätverksgatewayen i portalen.
   - **IP-adress:** Ange en giltig offentlig **IP-adress** för VPN-enheten eller virtuell nätverksgateway som du vill ansluta till.

     * **Om det lokala nätverket representerar en lokal plats:** Ange den offentliga IP-adressen för VPN-enheten som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure.
     * **Om det lokala nätverket representerar ett annat VNet:** Ange den offentliga IP-adressen som tilldelades till den virtuella nätverksgatewayen för det virtuella nätverket.
     * **Om du ännu inte har IP-adressen:** Du kan göra upp en giltig platshållaren IP-adress och gå tillbaka och ändra den här inställningen innan du ansluter.
   - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till.
   - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
   - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
   - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

4. Klicka på **Skapa** för att skapa den lokala nätverksgatewayen.
