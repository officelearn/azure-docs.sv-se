---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 12/11/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320237"
---
Använd antingen ett rotcertifikat som genererades med en företagslösning (rekommenderas) eller generera ett självsignerat rotcertifikat. När du har skapat rotcertifikatet exporterar du offentliga certifikatdata (inte den privata nyckeln) som en Base64-kodad X.509 .cer-fil. Ladda sedan upp offentliga certifikatdata till Azure-servern.

* **Företagscertifikat:** Om du använder en företagslösning kan du använda din befintliga certifikatkedja. Hämta .cer-filen för det rotcertifikat som du vill använda.
* **Självsignerat rotcertifikat:** Om du inte använder en företagscertifikatlösning skapar du ett självsignerat rotcertifikat. I annat fall kommer inte de klientcertifikat som du skapar vara kompatibla med dina P2S-anslutningar, och klienterna får då ett anslutningsfel när de försöker ansluta. Du kan använda Azure PowerShell, MakeCert eller OpenSSL. Stegen i följande artiklar beskriver hur du genererar ett kompatibelt självsignerat rotcertifikat:

  * [Windows 10 PowerShell-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): För de här anvisningarna krävs Windows 10 och PowerShell för att generera certifikat. Klientcertifikaten som skapas från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [MakeCert-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att generera certifikat. MakeCert är inaktuellt, men du kan fortfarande använda det för att generera certifikat. Klientcertifikat som du genererar från rotcertifikatet kan installeras på valfri P2S-klient som stöds.
  * [Linux-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
