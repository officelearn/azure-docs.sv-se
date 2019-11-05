---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523915"
---
1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa en resurs**. 

   ![Skapa en resurs i Azure Portal](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. I fältet **Sök på Marketplace skriver du** **lokal nätverksgateway**och trycker sedan på **RETUR** för att söka. En lista med resultat returneras. Klicka på **Lokal nätverksgateway** och sedan på knappen **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

   ![Skapa den lokala Nätverksgatewayen](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Skapa den lokala nätverksgatewayen")

3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

   - **Namn:** Ange ett namn för ditt lokala gateway-objekt.
   - **IP address:** Det här är den offentliga IP-adressen för den VPN-enhet som du vill att Azure ska ansluta till. Ange en giltig offentlig IP-adress. Om du inte har IP-adressen just nu kan du använda de värden som visas i exemplet. Du behöver dock gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för din VPN-enhet. Azure kommer annars inte kunna ansluta.
   - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd egna värden här om du vill ansluta till din lokala plats. Använd inte de värden som visas i exemplet*.
   - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
   - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
   - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
   - **Plats:** Platsen är samma som **region** i andra inställningar. Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

4. När du är klar med att ange värden klickar du på knappen **Skapa** längst ned på sidan för att skapa den lokala nätverksgatewayen.
