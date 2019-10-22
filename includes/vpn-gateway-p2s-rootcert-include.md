---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "67187203"
---
Använd antingen ett rotcertifikat som genererades med en företagslösning (rekommenderas) eller generera ett självsignerat rotcertifikat. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil. Ladda sedan upp offentliga certifikatdata till Azure-servern.

* **Företags certifikat:** Om du använder en företags lösning kan du använda din befintliga certifikat kedja. Hämta .cer-filen för det rotcertifikat som du vill använda.
* **Självsignerat rot certifikat:** Om du inte använder en företags certifikat lösning skapar du ett självsignerat rot certifikat. I annat fall kommer inte de klientcertifikat som du skapar vara kompatibla med dina P2S-anslutningar, och klienterna får då ett anslutningsfel när de försöker ansluta. Du kan använda Azure PowerShell, MakeCert eller OpenSSL. Stegen i följande artiklar beskriver hur du genererar ett kompatibelt självsignerat rotcertifikat:

  * [Windows 10 PowerShell-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): För de här anvisningarna krävs Windows 10 och PowerShell för att skapa certifikat. Klientcertifikaten som skapas från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [MakeCert-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att skapa certifikat. MakeCert är inaktuellt, men du kan fortfarande använda det för att generera certifikat. Klientcertifikat som du genererar från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [Linux-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
