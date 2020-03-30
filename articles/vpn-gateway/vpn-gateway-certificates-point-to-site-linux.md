---
title: 'Generera och exportera certifikat för Point-to-Site: Linux: CLI'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med hjälp av Linux (strongSwan) CLI.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779764"
---
# <a name="generate-and-export-certificates"></a>Generera och exportera certifikat

Point-to-Site-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och genererar klientcertifikat med Linux CLI och strongSwan. Om du letar efter olika certifikatinstruktioner läser du artiklarna [Powershell](vpn-gateway-certificates-point-to-site.md) eller [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Information om hur du installerar strongSwan med hjälp av GUI i stället för CLI finns i stegen i [klientkonfigurationsartikeln.](point-to-site-vpn-client-configuration-azure-cert.md#install)

## <a name="install-strongswan"></a>Installera strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generera och exportera certifikat

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationen för Point-to-Site för att [skapa och installera konfigurationsfiler för VPN-klienten](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
