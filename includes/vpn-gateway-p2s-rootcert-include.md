---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afee9450bc1485f781eb0d70b5d4dd2f50424573
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343197"
---
Du kan antingen använda ett rotcertifikat som genererades med en företagslösning (rekommenderas) eller så kan du generera ett självsignerat rotcertifikat. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil och laddar upp offentliga certifikatdata till Azure.

* **Företagscertifikat:** Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Hämta .cer-filen för det rotcertifikat som du vill använda.
* **Självsignerat rotcertifikat:** Om du inte använder en företagscertifikatlösning måste du skapa ett självsignerat rotcertifikat. Det är viktigt att du följer anvisningarna i någon av P2S-certifikatartiklarna nedan. I annat fall kommer inte de klientcertifikat du skapar vara kompatibla med P2S-anslutningar och klienterna får då ett anslutningsfelmeddelande när de försöker ansluta. Du kan använda Azure PowerShell, MakeCert eller OpenSSL. Med anvisningarna i de angivna artiklarna skapas ett kompatibelt certifikat:

  * [Windows 10 PowerShell-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): För de här anvisningarna krävs Windows 10 och PowerShell för att skapa certifikat. Klientcertifikaten som skapas från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [MakeCert-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att skapa certifikat. MakeCert har tagits ur bruk men du kan fortfarande använda det för att skapa certifikat. Klientcertifikaten som skapas från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [Linux-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)
