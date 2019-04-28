---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320256"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda lösningen för företags-PKI (din interna PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="certsettings"></a>Finns det anvisningar för certifikatinställningar och -parametrar?

* **Interna PKI/Företags-PKI-lösningen:** Se hur du [generera certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Se den [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) i artikeln.

* **MakeCert:** Se den [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) i artikeln.

* **OpenSSL:** 

    * Se till att konvertera rotcertifikatet till Base64 när du exporterar certifikat.

    * För klientcertifikatet:

      * Ange längden 4096 när du skapar den privata nyckeln.
      * För parametern *-tillägg* anger du *usr_cert* när du skapar certifikatet.