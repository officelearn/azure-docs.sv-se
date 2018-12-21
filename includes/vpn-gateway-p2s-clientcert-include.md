---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 7ae3886db6391836cd8d281e44c95c5253cc8dd5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323891"
---
Varje klientdator som du ansluter till ett virtuellt nätverk med punkt-till-plats-anslutning måste ha ett klientcertifikat installerat. Du kan generera det från rotcertifikatet och installera det på varje klientdator. Om du inte installerar ett giltigt klientcertifikat misslyckas autentiseringen när klienten försöker ansluta till det virtuella nätverket.

Du kan antingen generera ett unikt certifikat för varje klient eller använda samma certifikat för flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat. Om flera klienter i stället använder samma klientcertifikat och du behöver återkalla det, så måste du generera och installera nya certifikat för varje klient som använder certifikatet.

Du kan generera klientcertifikat på följande sätt:

- **Företagscertifikat:**

  - Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet *name@yourdomain.com*. Använd det här formatet i stället för formatet *domännamn\användarnamn*.
  - Se till att klientcertifikatet baseras på en användarcertifikatmall där *Klientautentisering* är den första posten i användningslistan. Du kan kontrollera certifikatet genom att dubbelklicka på det och visa **Förbättrad nyckelanvändning** på fliken **Information**.

- **Självsignerat rotcertifikat:** Följ anvisningarna i någon av följande P2S-certifikatartiklar så att de klientcertifikat som du skapar är kompatibla med P2S-anslutningar. Med anvisningarna i de angivna artiklarna skapas ett kompatibelt klientcertifikat: 

  * [Windows 10 PowerShell-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): För de här anvisningarna krävs Windows 10 och PowerShell för att skapa certifikat. Certifikaten som skapas kan installeras på valfri P2S-klient som stöds.
  * [MakeCert-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att generera certifikat. MakeCert är inaktuellt, men du kan fortfarande använda det för att generera certifikat. Du kan installera de genererade certifikaten på valfri P2S-klient som stöds.
  * [Linux-instruktioner](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  När du skapar ett klientcertifikat från ett självsignerat rotcertifikat installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator ska du exportera det som .pfx tillsammans med hela certifikatkedjan. Då skapas en .pfx-fil som innehåller den rotcertifikatinformation som krävs för att autentisera klienten. Anvisningar för hur du exporterar ett certifikat finns i [Generate and export certificates for Point-to-Site using PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) (Generera och exportera certifikat för punkt-till-plats med hjälp av PowerShell).