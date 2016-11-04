---
title: Windows-autentisering och Azure Multi-Factor Authentication Server
description: Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera Windows-autentisering och Azure Multi-Factor Authentication Server.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Windows-autentisering och Azure Multi-Factor Authentication Server
I avsnittet Windows-autentisering kan administratören aktivera och konfigurera Windows-autentisering för ett eller flera program.  Listan nedan innehåller saker som du bör tänka på innan du konfigurerar Windows-autentisering.

* En omstart krävs innan Azure Multi-Factor Authentication för Terminal Services börjar gälla.
* Om ”Kräv Azure Multi-Factor Authentication-användarmatchning” är markerat och du inte finns med i användarlistan kan du inte logga in på datorn efter omstarten.
* Användningen av tillförlitliga IP-adresser beror på om programmet kan tillhandahålla klient-IP-adressen med autentiseringen. För närvarande stöds endast Terminal Services.  

> [!NOTE]
> Den här funktionen stöds inte om du vill skydda Terminal Services i Windows Server 2012 R2.
> 
> 

## Följ stegen nedan om du vill skydda ett program med Windows-autentisering.
1. Klicka på ikonen för Windows-autentisering i Azure Multi-Factor Authentication Server.
   ![Windows-autentisering](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Markera kryssrutan Aktivera Windows-autentisering. Den här rutan är avmarkerad som standard.
3. På fliken Program kan administratören konfigurera ett eller flera program för Windows-autentisering.
4. Välj en server eller ett program – ange om servern/programmet är aktiverat. Klicka på OK.
5. Klicka på knappen Lägg till.
6. På fliken Tillförlitliga IP-adresser kan du välja att hoppa över Azure Multi-Factor Authentication för Windows-sessioner som kommer från specifika IP-adresser. Om anställda till exempel använder programmet från kontoret och hemifrån kan du välja att du inte vill att deras telefoner ska ringa för Azure Multi-Factor Authentication när de är på kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.
7. Klicka på knappen Lägg till.
8. Välj Enkel IP om du vill hoppa över en enstaka IP-adress.
9. Välj IP-intervall om du vill hoppa över ett helt IP-adressintervall. Exempel: 10.63.193.1-10.63.193.100.
10. Välj Undernät om du vill ange ett intervall med IP-adresser med hjälp av undernätsnotation. Ange undernätets start-IP och välj lämplig nätmask i listrutan.
11. Klicka på OK.

<!--HONumber=Sep16_HO3-->


