---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025096"
---
1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa en resurs**.

   ![Resurs](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. I fältet **Sök på Marketplace skriver du** **lokal nätverksgateway**och trycker sedan på **RETUR** för att söka. En lista med resultat returneras. Klicka på **Lokal nätverksgateway** och sedan på knappen **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Skapa en IP-adress för lokal nätverksgateway":::

3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   - **Namn:** Ange ett namn för ditt lokala gateway-objekt.
   - **Slut punkt:** Välj typ av slut punkt för den lokala VPN-enheten – **IP-adress** eller **fullständigt domän namn (fullständigt kvalificerat domän namn)**.
      - **IP-adress**: om du har en statisk offentlig IP-adress som har tilldelats från Internet leverantören för VPN-enheten väljer du alternativet IP-adress och fyller i IP-adressen så som visas i exemplet. Det här är den offentliga IP-adressen för VPN-enheten som du vill att Azure VPN-gatewayen ska ansluta till. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
      - **FQDN**: om du har en dynamisk offentlig IP-adress som kan ändras efter en viss tids period, som vanligt vis bestäms av Internet leverantören, kan du använda ett konstant DNS-namn med en dynamisk DNS-tjänst för att peka på din aktuella offentliga IP-adress för VPN-enheten. Din Azure VPN-gateway kommer att matcha FQDN för att fastställa den offentliga IP-adressen som ska anslutas till. En skärm bild nedan visar ett exempel på hur du använder FQDN i stället för IP-adress.
   - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd egna värden här om du vill ansluta till din lokala plats. Använd inte de värden som visas i exemplet*.
   - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
   - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
   - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   - **Plats:** Platsen är samma som **region** i andra inställningar. Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

   Detta är samma sida, men med FQDN markerat:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Skapa en IP-adress för lokal nätverksgateway":::

   > [!NOTE]
   >
   > * Azure VPN stöder bara en IPv4-adress för varje fullständigt domän namn. Om domän namnet matchar flera IP-adresser kommer Azure VPN Gateway att använda den första IP-adressen som returneras av DNS-servrarna. För att eliminera osäkerheten rekommenderar vi att ditt FQDN alltid matchas till en enda IPv4-adress. IPv6 stöds inte.
   > * Azure VPN Gateway hanterar en DNS-cache som uppdateras var 5: e minut. Gatewayen försöker bara matcha FQDN: er för frånkopplade tunnlar. Att återställa gatewayen utlöser även FQDN-matchning.
   >

4. När du är klar med att ange värdena väljer du knappen **skapa** längst ned på sidan för att skapa den lokala Nätverksgatewayen.
