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
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059912"
---
När du genererar ett klientcertifikat installeras det automatiskt på den dator som du använde för att generera det. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera klientcertifikatet som du har skapat.

1. Öppna **Hantera användarcertifikat** om du vill exportera ett certifikat. De klientcertifikat som du har skapat finns som standard i Certifikat - Aktuell användare\Personliga\Certifikat. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter**och klicka sedan på **Exportera** för att öppna guiden **Exportera certifikat**.

   ![Exportera](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Klicka på **Nästa** i guiden Exportera certifikat om du vill fortsätta.

   ![Nästa](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Välj **Ja, exportera den privata nyckeln**och klicka sedan på **Nästa**.

   ![exportera privat nyckel](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Den här inställningen exporterar dessutom den rotcertifikatinformation som krävs för lyckad klientautentisering. Utan den misslyckas klientautentiseringen eftersom klienten inte har det betrodda rotcertifikatet. Klicka sedan på **Nästa**.

   ![exportera filformat](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. På sidan **Fil som ska exporteras****bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![fil som ska exporteras](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klicka på **Slutför** för att exportera certifikatet.

   ![avsluta](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)