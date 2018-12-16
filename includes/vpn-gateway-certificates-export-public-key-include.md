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
ms.openlocfilehash: 3c6485406c67bf84b9e0fdfb9f4683abe5062d6c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444394"
---
När du har skapat ett självsignerat rotcertifikat, exportera den offentliga nyckeln .cer-filen med rotcertifikatet (inte den privata nyckeln). Du kommer senare att överföra filen till Azure. Följande steg hjälper dig att exportera .cer-filen för ditt självsignerade rotcertifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell användare\personligt\certifikat bero det på att du har öppnat Certifikathanteraren för lokal dator-certifikat (rubrik är ”certifikat – lokal dator” som motsats till ”certifikat – aktuell användare”). Öppna Certifikathanteraren i den aktuella användaromfattning starta den från samma PowerShell där certifikat som har skapats genom att skriva ```certmgr```.

   ![Exportera](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klicka på **Nästa** i guiden.

   ![Exportera certifikatet](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

   ![Exportera inte den privata nyckeln](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

   ![Base 64-kodad](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. För **fil som ska exporteras**, **Bläddra** till den plats som du vill exportera certifikatet. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![Bläddra](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klicka på **Slutför** för att exportera certifikatet.

   ![Slutför](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Ditt certifikat har exporterats.

   ![Lyckades](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Det exporterade certifikatet ser ut ungefär så här:

   ![Exporterat](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Om du öppnar det exporterade certifikatet med hjälp av anteckningar, visas något som liknar det här exemplet. I avsnittet i blått innehåller den information som överförs till Azure. Om du vill öppna certifikatet med anteckningar och ser inte ut ungefär så här, vanligtvis det innebär att du inte exportera det med Base64-kodad X.509 (. CER)-format. Om du vill använda en annan textredigerare kan du också känna att vissa redigerare kan leda till oönskade formatering i bakgrunden. Detta kan skapa problem när laddat upp text från det här certifikatet till Azure.

   ![Öppna med anteckningar](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
