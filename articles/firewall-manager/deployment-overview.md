---
title: Översikt över distributionen av Azure Firewall Manager
description: Lär dig mer om distributions steg på hög nivå som krävs för för hands versionen av Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502032"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Översikt över distributionen av Azure Firewall Manager

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Det finns fler än ett sätt att distribuera för hands versionen av Azure Firewall Manager, men följande allmänna process rekommenderas.

## <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> För hands versionen av Azure Firewall Manager måste aktive ras explicit med kommandot `Register-AzProviderFeature` PowerShell.
>Kör följande kommandon från en PowerShell-kommandotolk:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>Det tar upp till 30 minuter innan funktions registreringen har slutförts. Kör följande kommando för att kontrol lera > registrerings status:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Allmän distributions process

1. Skapa din hubb och eker-arkitektur

   - Skapa en säker virtuell hubb med Azure Firewall Manager och Lägg till virtuella nätverks anslutningar.<br>*eller*<br>
   - Skapa en virtuell WAN-hubb och Lägg till virtuella nätverks anslutningar.
2. Välj säkerhets leverantörer

   - Klar när du skapade en säker virtuell hubb.<br>*eller*<br>
   - Konvertera en befintlig virtuell WAN-hubb till en säker virtuell hubb.
3. Skapa en brand Väggs princip och koppla den till din hubb

   - Gäller endast om du använder Azure-brandväggen.
   - SECaaS-principer (från tredje part) konfigureras med hjälp av partner hanterings upplevelser.
4. Konfigurera väg inställningar för att dirigera trafik till din skyddade hubb

   - Dirigera snabbt trafik till din säkra hubb för filtrering och loggning utan användardefinierade vägar (UDR) i ekrar virtuella nätverk med hjälp av den säkra väg inställnings sidan för virtuella hubbar.

> [!NOTE]
> - Du kan inte ha mer än en hubb per virtuell WAN per region. Men du kan lägga till flera virtuella WAN-näti regionen för att uppnå detta.
> - Du kan inte ha överlappande IP-utrymmen för hubbar i en vWAN.
> - Hubbens VNet-anslutningar måste finnas i samma region som hubben.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skydda ditt moln nätverk med för hands versionen av Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md)