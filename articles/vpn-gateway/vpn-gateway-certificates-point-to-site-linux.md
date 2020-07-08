---
title: 'Skapa och exportera certifikat för punkt-till-plats: Linux: CLI'
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat med Linux-CLI (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984608"
---
# <a name="generate-and-export-certificates"></a>Skapa och exportera certifikat

Punkt-till-plats-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med hjälp av Linux CLI och strongSwan. Om du letar efter olika certifikat instruktioner, se [PowerShell](vpn-gateway-certificates-point-to-site.md) -eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) -artiklarna. Information om hur du installerar strongSwan med GUI i stället för CLI finns i stegen i artikeln [klient konfiguration](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Installera strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Skapa och exportera certifikat

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med din punkt-till-plats-konfiguration för att [skapa och installera konfigurationsfiler för VPN-klienten](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
