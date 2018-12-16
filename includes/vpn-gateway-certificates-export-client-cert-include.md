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
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444407"
---
När du genererar ett klientcertifikat behöver installeras den automatiskt på den dator som du använde för att skapa den. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det klientcertifikat som du skapade.

1. Öppna **Hantera användarcertifikat** om du vill exportera ett certifikat. De klientcertifikat som du genererade finns som standard i ”certifikat – aktuell användare\personligt\certifikat”. Högerklicka på det klientcertifikat som du vill exportera, klicka på **alla uppgifter**, och klicka sedan på **exportera** att öppna den **guiden Exportera certifikat**.

   ![Exportera](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. I guiden Exportera certifikat klickar du på **nästa** att fortsätta.

   ![Nästa](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Välj **Ja, exportera den privata nyckeln**, och klicka sedan på **nästa**.

   ![Exportera privat nyckel](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. På sidan **Filformat för export** låter du standardalternativen vara markerade. Se till att **Ta med om möjligt alla certifikat i certifieringssökvägen** har valts. Den här inställningen exporterar dessutom rotcertifikatinformation som krävs för lyckad klientautentisering. Utan detta kommer misslyckas autentiseringen för klienten eftersom klienten inte har det betrodda rotcertifikatet. Klicka sedan på **Nästa**.

   ![filformat för export](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Du måste skydda den privata nyckeln på sidan **Säkerhet**. Om du väljer att använda ett lösenord måste du vara noga med att skriva ned eller komma ihåg lösenordet som du anger för det här certifikatet. Klicka sedan på **Nästa**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. På sidan **Fil som ska exporteras** **bläddrar** du till den plats som du vill exportera certifikatet till. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

   ![fil som ska exporteras](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klicka på **Slutför** för att exportera certifikatet.

   ![slutför](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)