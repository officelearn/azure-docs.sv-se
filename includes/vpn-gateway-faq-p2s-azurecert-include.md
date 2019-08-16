---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520885"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för att generera certifikat för punkt-till-plats-anslutning?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Kan jag använda certifikat från Azure Key Vault?

Nej.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda lösningen för företags-PKI (din interna PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="certsettings"></a>Finns det anvisningar för certifikatinställningar och -parametrar?

* **Intern PKI/företags-PKI-lösning:** Se stegen för att [Skapa certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Se [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artikeln för steg.

* **MakeCert:** Se [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) -artikeln för steg.

* **OpenSSL:** 

    * Se till att konvertera rotcertifikatet till Base64 när du exporterar certifikat.

    * För klientcertifikatet:

      * Ange längden 4096 när du skapar den privata nyckeln.
      * För parametern *-tillägg* anger du *usr_cert* när du skapar certifikatet.
