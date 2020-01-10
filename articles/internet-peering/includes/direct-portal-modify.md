---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775347"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering:

### <a name="add-direct-peering-connections"></a>Lägg till direkta peering-anslutningar
1. Klicka på knappen **+ Lägg till anslutningar** överst och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-modify-addconnection.png)
1. Fyll i formuläret **direkt peering-anslutning** och klicka på **Spara**. Om du behöver hjälp med att konfigurera en peering-anslutning går du igenom stegen under avsnittet "skapa och etablera en direkt peering" ovan.
    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det finns för närvarande inte stöd för att ta bort en anslutning på portalen. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredd på aktiva anslutningar
1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på knappen **...**  > **Redigera anslutning** .
    > [!div class="mx-imgBorder"]
    > Redigera](../media/setup-direct-modify-editconnection.png) för ![peering-anslutning
1. Ändra bandbredden enligt vad som visas nedan och klicka sedan på **Spara**.
    > [!div class="mx-imgBorder"]
    > ![peering-anslutning ändra bandbredd](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägg till IPv4/IPv6-session på aktiva anslutningar.
1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på knappen **...**  > **Redigera anslutning** som visas ovan.
1. Lägg till **sessionens IPv4-prefix** eller sessioner information om **IPv6-prefix** och klicka på **Spara**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-sessionen på aktiva anslutningar.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
