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
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197162"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda din Företags-PKI-lösning (ett internt PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="certsettings"></a>Finns det anvisningar för inställningar för certifikat och parametrar?

* **Intern PKI/Enterprise PKI-lösning:** Se stegen för att [generera certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** finns i [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artikel anvisningar.

* **MakeCert:** finns i [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) artikel anvisningar.

* **OpenSSL:** 

    * När du exporterar certifikat, bör du konvertera rotcertifikatet till Base64.

    * För klientcertifikat:

      * När du skapar den privata nyckeln måste du ange hur länge som 4096.
      * När du skapar certifikatet för den *-tillägg* parameter, ange *usr_cert*.