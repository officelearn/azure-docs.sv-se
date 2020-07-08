---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d16412e4e35714c840516670f520f77daed1676d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80059912"
---
När du genererar ett klient certifikat installeras det automatiskt på den dator som du använde för att generera det. Om du vill installera klient certifikatet på en annan klient dator måste du exportera det klient certifikat som du skapade.

1. Öppna **Hantera användarcertifikat** om du vill exportera ett certifikat. De klient certifikat som du har genererat är som standard placerade i "certificates-Current User\Personal\Certificates". Högerklicka på det klient certifikat som du vill exportera, klicka på **alla aktiviteter**och klicka sedan på **Exportera** för att öppna **guiden Exportera certifikat**.

   ![Exportera](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. I guiden Exportera certifikat klickar du på **Nästa** för att fortsätta.

   ![Nästa](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Välj **Ja, exportera den privata nyckeln**och klicka sedan på **Nästa**.

   ![Exportera privat nyckel](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Den här inställningen exporterar även rot certifikat informationen som krävs för lyckad klientautentisering. Utan den Miss lyckas klientautentisering eftersom klienten inte har det betrodda rot certifikatet. Klicka sedan på **Nästa**.

   ![fil format för export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. På sidan **Fil som ska exporteras****bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![fil att exportera](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klicka på **Slutför** för att exportera certifikatet.

   ![avsluta](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)