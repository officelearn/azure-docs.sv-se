---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523912"
---
Följ stegen nedan för att skapa en VNet i Resource Manager-distributionsmodellen med hjälp av Azure Portal. Använd **exempel värdena** om du använder dessa steg som självstudier. Om du inte genomför de här stegen som en vägledning, bör du ersätter värdena med dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>För att det här virtuella nätverket ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras inte trafiken som du förväntar dig. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Var noga med att planera din nätverkskonfiguration på lämpligt sätt.
>

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa en resurs**. 

   ![Skapa en resurs i Azure Portal](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. Skriv ”virtuellt nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna sidan **Virtuellt nätverk**.
3. Klicka på **Skapa**. Då öppnas sidan **Skapa virtuellt nätverk** .
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Ange följande värden:

   - **Namn**: VNet1
   - **Adress utrymme**: 10.1.0.0/16
   - **Prenumeration**: kontrol lera att prenumerationen som visas är den som du vill använda. Du kan ändra prenumerationer i listrutan.
   - **Resurs grupp**: TestRG1 (klicka på **Skapa ny** för att skapa en ny grupp)
   - **Plats**: östra USA
   - **Undernät**: frontend
   - **Adress intervall**: 10.1.0.0/24

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Lämna DDoS som Basic, tjänstens slut punkter som inaktiverade och brand väggen som inaktive rad.
6. Skapa VNet genom att klicka på **Skapa**.