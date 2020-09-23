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
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025302"
---
1. Klicka på **+Skapa en resurs** i portalen.
2. I sökrutan skriver du **Lokal nätverksgateway** och trycker sedan på **Retur** för att söka. En lista med resultat returneras. Klicka på **Lokal nätverksgateway** och sedan på knappen **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="Skapa den lokala nätverksgatewayen":::

3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   - **Namn:** Ange ett namn för ditt lokala gateway-objekt.
   - **Slut punkt:** Välj typ av slut punkt för den lokala VPN-enheten – **IP-adress** eller **fullständigt domän namn (fullständigt kvalificerat domän namn)**.
      - **IP-adress**: om du har en statisk offentlig IP-adress som har tilldelats från Internet leverantören för VPN-enheten väljer du alternativet IP-adress och fyller i IP-adressen så som visas i exemplet. Det här är den offentliga IP-adressen för VPN-enheten som du vill att Azure VPN-gatewayen ska ansluta till. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
      - **FQDN**: om du har en dynamisk offentlig IP-adress som kan ändras efter en viss tids period, som vanligt vis bestäms av Internet leverantören, kan du använda ett konstant DNS-namn med en dynamisk DNS-tjänst för att peka på din aktuella offentliga IP-adress för VPN-enheten. Din Azure VPN-gateway kommer att matcha FQDN för att fastställa den offentliga IP-adressen som ska anslutas till. En skärm bild nedan visar ett exempel på hur du använder FQDN i stället för IP-adress.
   - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd egna värden här om du vill ansluta till din lokala plats. Använd inte de värden som visas i exemplet*.
   - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
   - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
   - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

    Detta är samma sida, men med FQDN markerat:
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="Skapa den lokala Nätverksgatewayen – FQDN":::
   
   > [!NOTE]
   >
   > * Azure VPN stöder bara en IPv4-adress för varje fullständigt domän namn. Om domän namnet matchar flera IP-adresser kommer Azure VPN Gateway att använda den första IP-adressen som returneras av DNS-servrarna. För att eliminera osäkerhet rekommenderar vi att ditt FQDN alltid matchas till en enda IPv4-adress. IPv6 stöds inte.
   > * Azure VPN Gateway hanterar en DNS-cache som uppdateras var 5: e minut. Gatewayen försöker bara matcha FQDN: er för frånkopplade tunnlar. Att återställa gatewayen utlöser även FQDN-matchning.
   >

4. När du är klar med att ange värdena väljer du knappen **skapa** för att skapa gatewayen.
