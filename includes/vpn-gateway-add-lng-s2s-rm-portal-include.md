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
ms.openlocfilehash: 0270fa207b782d2d89f2408a380b766f58fa05cb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
1. Klicka på **+Lägg till** från **Alla resurser** i portalen. 
2. I sökrutan på sidan **Allt** skriver du **Lokal nätverksgateway** och klickar sedan för att söka. Detta gör att en lista returneras. Klicka på **Lokal nätverksgateway** för att öppna sidan och klicka sedan på **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

  ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

  - **Namn:** Ange ett namn för ditt lokala gateway-objekt.
  - **IP address:** Det här är den offentliga IP-adressen för den VPN-enhet som du vill att Azure ska ansluta till. Ange en giltig offentlig IP-adress. IP-adressen får inte vara bakom en NAT och måste kunna nås av Azure. Om du inte har IP-adress just nu, du kan använda de värden som visas i skärmbilden, men du behöver gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för VPN-enheten. Azure kommer annars inte kunna ansluta.
  - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd dina egna värden här, inte värdena som visas i skärmbilden*.
  - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
  - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
  - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
  - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

4. När du är klar med att ange värden klickar du på **Skapa** längst ned på sidan för att skapa den lokala nätverksgatewayen.