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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774567"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering:

### <a name="add-exchange-peering-connections"></a>Lägg till Exchange peering-anslutningar

1. Klicka på knappen **+ Lägg till anslutningar** överst och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-exchange-modify-addconnection.png)
1. Fyll i formuläret **Exchange peering-anslutning** och klicka på **Spara**. Om du behöver hjälp med att konfigurera en peering-anslutning går du igenom stegen under avsnittet "skapa och etablera en direkt peering" ovan.
    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Ta bort Exchange peering-anslutningar

1. Klicka på en peering-anslutning som du vill ta bort och klicka sedan på knappen **...**  > **ta bort anslutning** .
    > [!div class="mx-imgBorder"]
    > Ta bort](../media/setup-exchange-modify-deleteconnection.png) ![peering-anslutning
1. Ange resurs-ID: t i rutan **Bekräfta borttagning** som visas i de markerade rutorna och klicka på **ta bort**.
    > [!div class="mx-imgBorder"]
    > ![peering-anslutning DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägg till IPv4/IPv6-session på aktiva anslutningar

1. Klicka på en peering-anslutning som du vill ändra och klicka sedan på knappen **...**  > **Redigera anslutning** .
    > [!div class="mx-imgBorder"]
    > Redigera](../media/setup-exchange-modify-editconnection.png) för ![peering-anslutning
1. Lägg till **IPv4-adress** eller **IPv6-adress** information och klicka på **Spara**.
    > [!div class="mx-imgBorder"]
    > Ändra](../media/setup-exchange-modify-editconnectionsettings.png) för ![peering-anslutning

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-session på aktiva anslutningar

Det finns för närvarande inte stöd för att ta bort en IPv4/IPv6-session från en befintlig anslutning på portalen. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com).