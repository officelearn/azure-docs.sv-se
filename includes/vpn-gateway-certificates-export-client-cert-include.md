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
ms.openlocfilehash: 517ebbf739c64c0364dc21386fee86ebc740e997
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
När du skapar ett certifikat installeras den automatiskt på den dator som du använde för att skapa den. Om du vill installera klientcertifikatet på en annan dator måste du exportera det klientcertifikat som du genererade.

1. Öppna **Hantera användarcertifikat** om du vill exportera ett certifikat. De klientcertifikat som du genererade finns som standard i 'Certifikat - aktuell User\Personal\Certificates'. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla aktiviteter**, och klicka sedan på **exportera** att öppna den **guiden Exportera certifikat**.

  ![Exportera](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. I guiden Exportera certifikat klickar du på **nästa** att fortsätta.

  ![Nästa](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Välj **Ja, exportera den privata nyckeln**, och klicka sedan på **nästa**.

  ![Exportera privat nyckel](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Den här inställningen exporterar dessutom information för rot-certifikat som krävs för lyckad klientautentisering. Utan den misslyckas autentiseringen för klienten eftersom klienten inte har det betrodda rotcertifikatet. Klicka sedan på **Nästa**.

  ![Exportfilformat](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

  ![fil som ska exporteras](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klicka på **Slutför** för att exportera certifikatet.

  ![slutför](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)