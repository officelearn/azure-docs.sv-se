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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774567"
---
I det här avsnittet beskrivs hur du utför följande ändringsåtgärder för direkt peering:

### <a name="add-exchange-peering-connections"></a>Lägga till Exchange-peering-anslutningar

1. Klicka på knappen **+ Lägg till anslutningar** högst upp och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-exchange-modify-addconnection.png)
1. Fyll i formuläret **Exchange-peering-anslutning** och klicka på **Spara**. Om du vill ha hjälp med att konfigurera en peering-anslutning kan du granska stegen under Avsnittet Skapa och etablera en direkt peering ovan.
    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Ta bort Exchange-peering-anslutningar

1. Klicka på en peering-anslutning som du vill ta bort och klicka sedan på **...**  >  **Knappen Ta bort anslutning.**
    > [!div class="mx-imgBorder"]
    > ![Borttagning av peering-anslutning](../media/setup-exchange-modify-deleteconnection.png)
1. Ange resurs-ID:t i rutan **Bekräfta borttagning** enligt de markerade rutorna och klicka på **Ta bort**.
    > [!div class="mx-imgBorder"]
    > ![Borttagning av peer-anslutnings bekräftad](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägga till IPv4/IPv6-session vid aktiva anslutningar

1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på **...**  >  **Redigera** anslutningsknappen.
    > [!div class="mx-imgBorder"]
    > ![Redigera peering-anslutning](../media/setup-exchange-modify-editconnection.png)
1. Lägg till **IPv4-adress** eller **IPv6-adressinformation** och klicka på **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning ändra](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-session på aktiva anslutningar

Det går inte att ta bort en IPv4/IPv6-session från en befintlig anslutning på portalen. Kontakta [Microsoft peering](mailto:peeringexperience@microsoft.com).