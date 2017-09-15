---
title: Windows-autentisering och Azure MFA Server | Microsoft Docs
description: "Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera Windows-autentisering och Azure Multi-Factor Authentication Server."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: e3ad33dd12b4fb831ba43738bc5af4e5561a0682
ms.contentlocale: sv-se
ms.lasthandoff: 02/17/2017

---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Windows-autentisering och Azure Multi-Factor Authentication Server
Använd avsnittet Windows-autentisering för Azure Multi-Factor Authentication Server för att aktivera och konfigurera Windows-autentisering för program. Ta följande lista i beaktande innan du konfigurerar Windows-autentisering:

* Efter konfigurationen måste du starta om Azure Multi-Factor Authentication för att Terminal Services ska börja gälla.
* Om ”Kräv Azure Multi-Factor Authentication-användarmatchning” är markerat och du inte finns med i användarlistan kan du inte logga in på datorn efter omstarten.
* Användningen av tillförlitliga IP-adresser beror på om programmet kan tillhandahålla klient-IP-adressen med autentiseringen. För närvarande stöds endast Terminal Services.  

> [!NOTE]
> Den här funktionen stöds inte om du vill skydda Terminal Services i Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Följ stegen nedan om du vill skydda ett program med Windows-autentisering.
1. Klicka på ikonen för Windows-autentisering i Azure Multi-Factor Authentication Server.
   ![Windows-autentisering](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Markera kryssrutan **Aktivera Windows-autentisering**. Den här rutan är avmarkerad som standard.
3. På fliken Program kan administratören konfigurera ett eller flera program för Windows-autentisering.
4. Välj en server eller ett program – ange om servern/programmet är aktiverat. Klicka på **OK**.
5. Klicka på **Lägg till...**
6. På fliken Tillförlitliga IP-adresser kan du välja att hoppa över Azure Multi-Factor Authentication för Windows-sessioner som kommer från specifika IP-adresser. Om anställda till exempel använder programmet från kontoret och hemifrån kan du välja att du inte vill att deras telefoner ska ringa för Azure Multi-Factor Authentication när de är på kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.
7. Klicka på **Lägg till...**
8. Välj **Enkel IP** om du vill hoppa över en enstaka IP-adress.
9. Välj **IP-intervall** om du vill hoppa över ett helt IP-adressintervall. Exempel: 10.63.193.1-10.63.193.100.
10. Välj **Undernät** om du vill ange ett intervall med IP-adresser med hjälp av undernätsnotation. Ange undernätets start-IP och välj lämplig nätmask i listrutan.
11. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera VPN-installationer från tredje part för Azure MFA Server](multi-factor-authentication-advanced-vpn-configurations.md)

- [Utöka din befintliga infrastruktur för autentisering med NPS-tillägget för Azure MFA](multi-factor-authentication-nps-extension.md)

