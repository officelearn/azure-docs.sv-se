---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764010"
---
När du har skapat ett självsignerat rotcertifikat, exportera den offentliga nyckeln .cer-filen med rotcertifikatet (inte den privata nyckeln). Du kommer senare att överföra filen till Azure. Följande steg hjälper dig att exportera .cer-filen för ditt självsignerade rotcertifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas. Om du inte kan hitta certifikatet under aktuell användare\personligt\certifikat, kan du av misstag öppnade ”certifikat - lokal Computer”, i stället för ”certifikat – aktuell användare”). Om du vill öppna Certifikathanteraren i aktuella omfång med hjälp av PowerShell kan du skriva *certmgr* i konsolfönstret.

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
