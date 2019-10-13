---
title: 'Skapa och exportera certifikat för punkt-till-plats: PowerShell: Azure | Microsoft Docs'
description: Skapa ett självsignerat rot certifikat, exportera den offentliga nyckeln och generera klient certifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: 8f7d4f71853e1640146a38fb39384c20ca9553eb
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285730"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Skapa och exportera certifikat för punkt-till-plats med hjälp av PowerShell

Punkt-till-plats-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rot certifikat och genererar klient certifikat med hjälp av PowerShell på Windows 10 eller Windows Server 2016. Om du letar efter olika certifikat instruktioner, se [certifikat – Linux](vpn-gateway-certificates-point-to-site-linux.md) eller [certifikat – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Du måste utföra stegen i den här artikeln på en dator som kör Windows 10 eller Windows Server 2016. PowerShell-cmdletar som du använder för att generera certifikat är en del av operativ systemet och fungerar inte i andra versioner av Windows. Datorn med Windows 10 eller Windows Server 2016 krävs bara för att skapa certifikaten. När certifikaten har skapats kan du ladda upp dem eller installera dem på alla klient operativ system som stöds.

Om du inte har åtkomst till en dator med Windows 10 eller Windows Server 2016 kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) för att skapa certifikat. De certifikat som du genererar med någon av metoderna kan installeras på alla klient operativ system som [stöds](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) .

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>Installera ett exporterat klient certifikat

Varje klient som ansluter till VNet över en P2S-anslutning kräver att ett klient certifikat installeras lokalt.

Information om hur du installerar ett klient certifikat finns i [Installera ett klient certifikat för punkt-till-plats-anslutningar](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt med din punkt-till-plats-konfiguration.

* Anvisningar för distributions modellen i **Resource Manager** finns i [Konfigurera P2s med intern Azure-certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* De **klassiska** stegen för distributions modellen finns i [Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).