---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2dba0f657b418267db90c07014dc8996ed12a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059950"
---
När du har skapat ett självsignerat rotcertifikat exporterar du den offentliga nyckeln .cer-fil för rotcertifikat (inte den privata nyckeln). Du kommer senare att överföra den här filen till Azure. Följande steg hjälper dig att exportera .cer-filen för ditt självsignerade rotcertifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte hittar certifikatet under Aktuell användare\Personliga\Certifikat kan du av misstag ha öppnat "Certifikat - lokal dator", i stället för "Certifikat - aktuell användare"). Om du vill öppna Certifikathanteraren i det aktuella användaromfattningen med PowerShell skriver du *certmgr* i konsolfönstret.

   ![Exportera](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Bas-64 kodad](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. För **Fil att exportera** **bläddrar** du till den plats där du vill exportera certifikatet. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![Bläddra](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klicka på **Slutför** för att exportera certifikatet.

   ![Slutför](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certifikatet har exporterats.

   ![Lyckades](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Det exporterade certifikatet ser ut ungefär så här:

   ![Exporteras](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Om du öppnar det exporterade certifikatet med Anteckningar visas något som liknar det här exemplet. Avsnittet i blått innehåller den information som överförs till Azure. Om du öppnar certifikatet med Anteckningar och det inte liknar detta, innebär det vanligtvis att du inte exporterade det med base-64-kodade X.509(. CER)-format. Om du vill använda en annan textredigerare förstår du dessutom att vissa redigerare kan införa oavsiktlig formatering i bakgrunden. Detta kan skapa problem när du överför texten från det här certifikatet till Azure.

   ![Öppna med Anteckningar](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
