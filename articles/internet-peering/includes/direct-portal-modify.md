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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681035"
---
I det här avsnittet beskrivs hur du utför följande ändringsåtgärder för direkt peering.

### <a name="add-direct-peering-connections"></a>Lägga till direkt peering-anslutningar
1. Välj knappen **+ Lägg till anslutningar** och konfigurera en ny peering-anslutning.
    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-modify-addconnection.png)

1. Fyll i formuläret **Direkt peering-anslutning** och välj **Spara**. Om du vill ha hjälp med att konfigurera en peering-anslutning läser du stegen i avsnittet Skapa och etablera en direkt peering.
    > [!div class="mx-imgBorder"]
    > ![Direktkopplingsformulär för peering](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det finns för närvarande inte stöd för att ta bort en anslutning i Azure-portalen. Kontakta [Microsoft peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredden på aktiva anslutningar
1. Välj en peering-anslutning som du vill ändra och välj sedan **...**  >  **Redigera anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Redigera anslutning](../media/setup-direct-modify-editconnection.png)

1. Ändra bandbredden genom att flytta skjutreglaget och välj sedan **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Ändra bandbredd](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Lägga till IPv4- eller IPv6-sessionsinformation om aktiva anslutningar
1. Välj en peering-anslutning som du vill ändra och välj sedan **...**  >  **Redigera anslutning** som visas i steg 1.
1. Ange **sessions-IPv4-prefix** eller **sessions-IPv6-prefixinformation** och välj **Spara**.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Ta bort IPv4- eller IPv6-sessionsinformation om aktiva anslutningar
Det finns för närvarande inte stöd för att ta bort **prefixinformationen session IPv4** eller **Prefixet Session IPv6** i portalen. Kontakta [Microsoft peering](mailto:peeringexperience@microsoft.com)om du vill ha mer information.
