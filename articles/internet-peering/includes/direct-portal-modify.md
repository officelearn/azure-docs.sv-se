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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775347"
---
I det här avsnittet beskrivs hur du utför följande ändringsåtgärder för direkt peering:

### <a name="add-direct-peering-connections"></a>Lägga till direkt peering-anslutningar
1. Klicka på knappen **+ Lägg till anslutningar** högst upp och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-modify-addconnection.png)
1. Fyll i formuläret **Direkt peering-anslutning** och klicka på **Spara**. Om du vill ha hjälp med att konfigurera en peering-anslutning kan du granska stegen under Avsnittet Skapa och etablera en direkt peering ovan.
    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det går inte att ta bort en anslutning på portalen. Kontakta [Microsoft peering](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredden på aktiva anslutningar
1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på **...**  >  **Redigera** anslutningsknappen.
    > [!div class="mx-imgBorder"]
    > ![Redigera peering-anslutning](../media/setup-direct-modify-editconnection.png)
1. Ändra bandbredd som visas nedan och klicka sedan på **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning ändra bandbredd](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägg till IPv4/IPv6-session på aktiva anslutningar.
1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på **...**  >  **Redigera anslutningsknappen** som visas ovan.
1. Lägg till **Prefix för session IPv4-prefix** eller **Session IPv6-prefixinformation** och klicka på **Spara**.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-session på aktiva anslutningar.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
