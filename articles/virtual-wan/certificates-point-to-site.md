---
title: Generera och exportera certifikat för VPN-anslutningar för användare | Virtuellt WAN i Azure
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med PowerShell i Windows 10 eller Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059935"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generera och exportera certifikat för VPN-anslutningar för användare

Användares VPN-anslutningar (point-to-site) använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och genererar klientcertifikat med PowerShell på Windows 10 eller Windows Server 2016.

Du måste utföra stegen i den här artikeln på en dator med Windows 10 eller Windows Server 2016. De PowerShell-cmdletar som du använder för att generera certifikat är en del av operativsystemet och fungerar inte på andra versioner av Windows. Windows 10- eller Windows Server 2016-datorn behövs bara för att generera certifikaten. När certifikaten har genererats kan du ladda upp dem eller installera dem på alla klientoperativsystem som stöds.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med [virtual WAN-stegen för användarens VPN-anslutning](virtual-wan-about.md)
