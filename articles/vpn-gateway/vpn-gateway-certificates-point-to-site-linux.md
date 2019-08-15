---
title: 'Skapa och exportera certifikat för punkt-till-plats: Linux: CLI Azure | Microsoft Docs'
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat med Linux-CLI (strongSwan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 2544df920580745e42aee1fc5e681d40bd1e74f9
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036013"
---
# <a name="generate-and-export-certificates"></a>Skapa och exportera certifikat

Punkt-till-plats-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med hjälp av Linux CLI och strongSwan. Om du letar efter olika certifikat instruktioner, se [PowerShell](vpn-gateway-certificates-point-to-site.md) -eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) -artiklarna. Information om hur du installerar strongSwan med GUI i stället för CLI finns i stegen i artikeln [klient konfiguration](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Installera strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Skapa och exportera certifikat

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med din punkt-till-plats-konfiguration för att [skapa och installera konfigurationsfiler för VPN-klienten](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
