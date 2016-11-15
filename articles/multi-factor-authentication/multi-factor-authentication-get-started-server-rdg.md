---
title: "Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS"
description: "Det här är sidan om Azure Multi-Factor-autentisering som beskriver hur du distribuerar Fjärrskrivbordsgateway (RDG) och Azure Multi-Factor Authentication Server med RADIUS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ee868c5ba1a8429a733633edbc7efaa74e512135


---
# <a name="remote-desktop-gateway-and-azure-multifactor-authentication-server-using-radius"></a>Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS
Ofta använder Fjärrskrivbordsgateway (RDG) den lokala nätverksprincipservern (NPS) för att autentisera användare. Det här dokumentet beskriver hur du dirigerar RADIUS-förfrågningar från Fjärrskrivbordsgateway (via den lokala NPS-servern) till Multi-Factor Authentication Server.

Multi-Factor Authentication Server bör installeras på en separat server, som sedan skickar RADIUS-begäran via en proxyanslutning till NPS på RDG-servern. När NPS-servern validerar användarnamnet och lösenordet returnerar den ett svar till Multi-Factor Authentication Server som utför autentiseringen med den andra faktorn innan resultatet returneras till gatewayen.

## <a name="configure-the-rd-gateway"></a>Konfigurera Fjärrskrivbordsgateway
Fjärrskrivbordsgateway måste konfigureras att skicka RADIUS-autentisering till en Azure Multi-Factor Authentication-server. När Fjärrskrivbordsgateway har installerats, konfigurerats och fungerar går du till egenskaperna för Fjärrskrivbordsgateway. Gå till fliken Arkiv med auktoriseringsprinciper för fjärrskrivbordsanslutning och ändra den så att en central server som kör NPS används i stället för en lokal server som kör NPS. Lägg till en eller flera Azure Multi-Factor Authentication-servrar som RADIUS-servrar och ange en delad hemlighet för varje server.

## <a name="configure-nps"></a>Konfigurera NPS
Fjärrskrivbordsgateway använder NPS för att skicka RADIUS-begäran till Azure Multi-Factor Authentication. En timeout måste ändras för att förhindra att tidsgränsen för Fjärrskrivbordsgateway går ut innan multifaktorautentiseringen har slutförts. Följ stegen nedan när du ska konfigurera NPS.

1. Expandera menyn för RADIUS-klienter och RADIUS-server i den vänstra kolumnen och klicka på Fjärr-RADIUS-servergrupper. Gå till egenskaperna för TS Gateway-servergruppen. Redigera RADIUS-servern eller RADIUS-servrarna som visas och gå till fliken Belastningsutjämning. Ändra ”Antal sekunder utan svar innan begäran anses vara förlorad” och ”Antal sekunder mellan begäranden när servern anses vara otillgänglig” till 30–60 sekunder. Klicka på fliken Autentisering/konto och kontrollera att de angivna RADIUS-portarna matchar de portar som Multi-Factor Authentication Server lyssnar på.
2. NPS-servern måste också konfigureras att ta emot RADIUS-autentiseringar från Azure Multi-Factor Authentication Server. Klicka på RADIUS-klienter på den vänstra menyn. Lägg till Azure Multi-Factor Authentication Server som en RADIUS-klient. Välj ett eget namn och ange en delad hemlighet.
3. Expandera avsnittet Principer i det vänstra navigeringsfönstret och klicka på Principer för anslutningsbegäran. Avsnittet bör innehålla en princip för anslutningsbegäran med namnet TS Gateway-auktoriseringsprincip som skapades när Fjärrskrivbordsgateway konfigurerades. Den här principen dirigerar RADIUS-förfrågningar till Multi-Factor Authentication Server.
4. Kopiera den här principen för att skapa en ny. I den nya principen lägger du till ett villkor som matchar Eget namn för klient med det egna namnet som du angav i steg 2 för RADIUS-klienten för Azure Multi-Factor Authentication Server. Ändra autentiseringsprovidern till Lokal dator. Den här principen säkerställer att när en RADIUS-begäran tas emot från Azure Multi-Factor Authentication Server så sker autentiseringen lokalt i stället för att en RADIUS-begäran skickas tillbaka till Azure Multi-Factor Authentication Server vilket skulle resultera i ett looptillstånd. För att förhindra loopsituationen måste den här nya principen placeras OVANFÖR den ursprungliga principen som vidarebefordrar till Multi-Factor Authentication Server.

## <a name="configure-azure-multifactor-authentication"></a>Konfigurera Azure Multi-Factor Authentication
- - -
Azure Multi-Factor Authentication Server är konfigurerat som en RADIUS-proxy mellan Fjärrskrivbordsgateway och NPS.  Den bör installeras på en domänansluten server som är separat från RDG-servern. Konfigurera Azure Multi-Factor Authentication Server genom att följa stegen nedan.

1. Öppna Azure Multi-Factor Authentication Server och klicka på ikonen för RADIUS-autentisering. Markera kryssrutan Aktivera RADIUS-autentisering.
2. På fliken Klienter kontrollerar du att portarna matchar NPS-konfigurationen och klickar sedan på Lägg till. Lägg till RDG-serverns IP-adress, programnamnet (valfritt) och en delad hemlighet. Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och Fjärrskrivbordsgatway.
3. Klicka på fliken Mål och välj alternativknappen för RADIUS-servern eller RADIUS-servrarna.
4. Klicka på Lägg till. Ange IP-adressen, den delade hemligheten och portarna för NPS-servern. Om du använder en central NPS-server är RADIUS-klienten och RADIUS-målet samma. Den delade hemligheten måste matcha den som konfigurerats i avsnittet RADIUS-klient på NPS-servern.

![RADIUS-autentisering](./media/multi-factor-authentication-get-started-server-rdg/radius.png)




<!--HONumber=Nov16_HO2-->


