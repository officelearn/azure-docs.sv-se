---
title: Ladda ned globala VPN-profiler för Azure Virtual WAN eller nav | Microsoft-dokument
description: Lär dig mer om Virtual WAN-automatisk skalbar anslutning mellan för gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 4/20/2020
ms.author: alzam
ms.openlocfilehash: b63bb861f4df087f852bb1bf599d32100d063f7e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733188"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Ladda ned en global eller hubbbaserad profil för VPN-klienter för användare

Azure Virtual WAN erbjuder två typer av anslutning för fjärranvändare: Globala och Hub-baserade. Använd följande avsnitt för att lära dig mer om och hämta en profil. 

> [!IMPORTANT]
> RADIUS-autentisering stöder endast den Hub-baserade profilen.

## <a name="global-profile"></a>Global profil

Profilen pekar på en belastningsutjämnare som innehåller alla aktiva VPN-hubbar för användare. Användaren dirigeras till det nav som är närmast användarens geografiska plats. Den här typen av anslutning är användbar när användare reser till olika platser ofta. Så här laddar du ned den **globala** profilen:

1. Navigera till det virtuella WAN-nätverket.
2. Klicka på **Användar-VPN-konfiguration**.
3. Markera den konfiguration som du vill hämta profilen för.
4. Klicka på **Ladda ner virtuell WAN-användare VPN-profil**.

   ![Global profil](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Hub-baserad profil

Profilen pekar på ett enda nav. Användaren kan bara ansluta till den aktuella hubben med den här profilen. Så här hämtar du **den navbaserade** profilen:

1. Navigera till det virtuella WAN-nätverket.
2. Klicka på **Hubb** på sidan Översikt.

    ![Hubbprofil 1](./media/global-hub-profile/hub1.png)
3. Klicka på **Användar-VPN (Peka på plats)**.
4. Klicka på **Ladda ner VPN-profil för virtuella hubbanvändare**.

   ![Hubbprofil 2](./media/global-hub-profile/hub2.png)
5. Kontrollera **EAPTLS**.
6. Klicka på **Generera och hämta profil**.

   ![Hubbprofil 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
