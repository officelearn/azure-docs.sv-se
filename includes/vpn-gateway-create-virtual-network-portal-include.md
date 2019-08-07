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
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780264"
---
Följ stegen nedan för att skapa en VNet i Resource Manager-distributionsmodellen med hjälp av Azure Portal. Använd **exempel värdena** om du använder dessa steg som självstudier. Om du inte genomför de här stegen som en vägledning, bör du ersätter värdena med dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>För att det här virtuella nätverket ska anslutas till en lokal plats måste du kontakta den lokala nätverksadministratören för att få ett intervall med IP-adresser som du kan använda specifikt för det här virtuella nätverket. Om det finns ett duplicerat adressintervall på båda sidorna av VPN-anslutningen dirigeras inte trafiken som du förväntar dig. Om du dessutom vill ansluta detta VNet till ett annat VNet kan inte adressutrymmet överlappa med andra VNet. Var noga med att planera din nätverkskonfiguration på lämpligt sätt.
>

1. Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **Skapa en resurs**. Skriv ”virtuellt nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna sidan **Virtuellt nätverk**.
3. Klicka på **Skapa**. Då öppnas sidan **Skapa virtuellt nätverk** .
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga. Ange följande värden:

   - **Namn på**: VNet1
   - **Adressutrymme**: 10.1.0.0/16
   - **Prenumeration**: Kontrol lera att den prenumeration du vill använda är den som du vill använda. Du kan ändra prenumerationer i listrutan.
   - **Resursgrupp**: TestRG1 (skapa en ny grupp genom att klicka på **Skapa nytt** )
   - **Plats**: East US
   - **Undernät**: Klientdel
   - **Adressintervall**: 10.1.0.0/24

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. Lämna DDoS som Basic, tjänstens slut punkter som inaktiverade och brand väggen som inaktive rad.
6. Skapa VNet genom att klicka på **Skapa**.