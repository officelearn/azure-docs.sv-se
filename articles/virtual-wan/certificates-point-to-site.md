---
title: Generera och exportera certifikat för Azure Virtual WAN-användare VPN-anslutningar | Microsoft Docs
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514919"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Skapa och exportera certifikat för virtuella WAN-användares VPN-anslutningar

Användares VPN-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.

Du måste utföra stegen i den här artikeln på en dator som kör Windows 10 eller Windows Server 2016. PowerShell-cmdletar som du använder för att generera certifikat är en del av operativ systemet och fungerar inte i andra versioner av Windows. Datorn med Windows 10 eller Windows Server 2016 krävs bara för att skapa certifikaten. När certifikaten har skapats kan du ladda upp dem eller installera dem på alla klient operativ system som stöds.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med de [virtuella WAN-stegen för användares VPN-anslutning](virtual-wan-about.md)
