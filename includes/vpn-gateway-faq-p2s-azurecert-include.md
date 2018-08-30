---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97d33bfcc8251b10ba121b7fb013800904450563
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "30197162"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda lösningen för företags-PKI (din interna PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="certsettings"></a>Finns det anvisningar för certifikatinställningar och -parametrar?

* **Lösning för intern PKI/företags-PKI:** Se hur du kan [generera certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Se anvisningarna i artikeln om [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** Se anvisningarna i artikeln om [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Se till att konvertera rotcertifikatet till Base64 när du exporterar certifikat.

    * För klientcertifikatet:

      * Ange längden 4096 när du skapar den privata nyckeln.
      * För parametern *-tillägg* anger du *usr_cert* när du skapar certifikatet.