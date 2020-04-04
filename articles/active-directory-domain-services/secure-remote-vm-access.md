---
title: Säker åtkomst till fjärrda virtuella datorer i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du skyddar fjärråtkomst till virtuella datorer med NPS (Network Policy Server) och Azure Multi Factor Authentication med distributionen av Fjärrskrivbordstjänster i en hanterad Azure Active Directory Domain Services-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8bc36dfdf3010b2bde485228f6ee110b0b826d31
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654754"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Säker fjärråtkomst till virtuella datorer i Azure Active Directory Domain Services

Om du vill skydda fjärråtkomst till virtuella datorer som körs i en Azure Active Directory Domain Services (Azure AD DS) hanterad domän kan du använda FJÄRRSKRIVBORDSTJÄNSTER (RDS) och NPS (Network Policy Server). Azure AD DS autentiserar användare när de begär åtkomst via RDS-miljön. För förbättrad säkerhet kan du integrera Azure Multi-Factor-autentisering för att tillhandahålla en ytterligare autentiseringsfråga under inloggningshändelser. Azure Multi-Factor Authentication använder ett tillägg för NPS för att tillhandahålla den här funktionen.

> [!IMPORTANT]
> Det rekommenderade sättet att på ett säkert sätt ansluta till dina virtuella datorer i en Azure AD DS-hanterad domän är att använda Azure Bastion, en fullständigt plattformshanterad PaaS-tjänst som du etablerar i ditt virtuella nätverk. En bastionvärd tillhandahåller säker och sömlös RDP-anslutning (Remote Desktop Protocol) till dina virtuella datorer direkt i Azure-portalen via SSL. När du ansluter via en skyddsvärd behöver dina virtuella datorer inte en offentlig IP-adress och du behöver inte använda nätverkssäkerhetsgrupper för att exponera åtkomst till RDP på TCP-port 3389.
>
> Vi rekommenderar starkt att du använder Azure Bastion i alla regioner där det stöds. I regioner utan Azure Bastion-tillgänglighet följer du stegen som beskrivs i den här artikeln tills Azure Bastion är tillgängligt. Var försiktig med att tilldela offentliga IP-adresser till virtuella datorer som är anslutna till Azure AD DS där all inkommande RDP-trafik är tillåten.
>
> Mer information finns i [Vad är Azure Bastion?][bastion-overview].

Den här artikeln visar hur du konfigurerar RDS i Azure AD DS och eventuellt använda Azure Multi-Factor Authentication NPS-tillägget.

![Översikt över fjärrskrivbordstjänster](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs [kan du skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett *arbetsbelastningsundernät* som skapats i det virtuella azure Active Directory Domain Services-nätverket.
    * Konfigurera [virtuella nätverk för en hanterad domän för Azure Active Directory Domain Services][configure-azureadds-vnet]om det behövs.
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Distribuera och konfigurera miljön för fjärrskrivbord

Kom igång genom att skapa minst två virtuella Azure-datorer som kör Windows Server 2016 eller Windows Server 2019. För redundans och hög tillgänglighet för fjärrskrivbordsmiljön kan du lägga till och belastningsutjämna ytterligare värdar senare.

En föreslagen RDS-distribution innehåller följande två virtuella datorer:

* *RDGVM01* - Kör servern för anslutningsutjämning för fjärrskrivbord, webbåtkomstserver för fjärrskrivbord och servern för fjärrskrivbordsgateway.
* *RDSHVM01* - Kör värdservern för fjärrskrivbordssessionen.

Se till att virtuella datorer distribueras till ett *arbetsbelastningsundernät* i ditt virtuella Azure AD DS-nätverk och anslut sedan de virtuella datorerna till Azure AD DS-hanterad domän. Mer information finns i hur du [skapar och ansluter till en virtuell Windows Server-dator till en Azure AD DS-hanterad domän][tutorial-create-join-vm].

Distributionen av fjärrskrivbordsmiljön innehåller ett antal steg. Den befintliga distributionsguiden för fjärrskrivbord kan användas utan några specifika ändringar som ska användas i en Azure AD DS-hanterad domän:

1. Logga in på virtuella datorer som skapats för fjärrskrivbordsmiljön med ett konto som ingår i gruppen *Azure AD DC-administratörer,* till exempel *contosoadmin*.
1. Om du vill skapa och konfigurera RDS använder du den befintliga [distributionsguiden för fjärrskrivbordsmiljö][deploy-remote-desktop]. Distribuera fjärrskrivbordsserverkomponenterna över dina virtuella Azure-datorer som du vill.
1. Om du vill ge åtkomst med en webbläsare [konfigurerar du webbklienten för fjärrskrivbord för användarna][rd-web-client].

Med fjärrskrivbord distribueras till Azure AD DS hanterad domän, kan du hantera och använda tjänsten som du skulle med en lokal AD DS-domän.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>Distribuera och konfigurera NPS- och Azure MFA NPS-tillägget

Om du vill öka säkerheten för användarens inloggningsupplevelse kan du eventuellt integrera fjärrskrivbordsmiljön med Azure Multi-Factor Authentication. Med den här konfigurationen får användarna ytterligare en uppmaning under inloggningen för att bekräfta sin identitet.

För att tillhandahålla den här funktionen installeras ytterligare en NPS-server (Network Policy Server) i din miljö tillsammans med AZURE Multi-Factor Authentication NPS-tillägget. Det här tillägget integreras med Azure AD för att begära och returnera status för multifaktorautentiseringsmeddelanden.

Användare måste vara [registrerade för att använda Azure Multi-Factor Authentication][user-mfa-registration], vilket kan kräva ytterligare Azure AD-licenser.

Om du vill integrera Azure Multi Factor Authentication i din Azure AD DS Remote Desktop-miljö skapar du en NPS-server och installerar tillägget:

1. Skapa ytterligare en virtuell dator med Windows Server 2016 eller 2019, till exempel *NPSVM01,* som är anslutet till ett *arbetsbelastningsundernät* i ditt virtuella Azure AD DS-nätverk. Gå med i den virtuella datorn till den Hanterade Azure AD DS-domänen.
1. Logga in på NPS VM som ett konto som ingår i gruppen *Azure AD DC-administratörer,* till exempel *contosoadmin*.
1. Välj **Lägg till roller och funktioner i** **Serverhanteraren**och installera sedan rollen *Nätverksprincip och Åtkomsttjänster.*
1. Använd den befintliga instruktioner för att [installera och konfigurera Azure MFA NPS-tillägget][nps-extension].

När NPS-servern och Azure Multi-Factor Authentication NPS-tillägget är installerade slutför du nästa avsnitt för att konfigurera det för användning med fjärrskrivbordsmiljön.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>Integrera fjärrskrivbordsgateway och Azure MultiFaktor-autentisering

Om du vill integrera NPS-tillägget för autentisering av Azure Multi Factor använder du den befintliga instruktioner för att [integrera infrastrukturen för fjärrskrivbordsgateway med nps-tillägget (Network Policy Server) och Azure AD][azure-mfa-nps-integration].

Följande ytterligare konfigurationsalternativ behövs för att integrera med en Azure AD DS-hanterad domän:

1. Registrera inte [NPS-servern i Active Directory][register-nps-ad]. Det här steget misslyckas i en Azure AD DS-hanterad domän.
1. I [steg 4 för att konfigurera nätverksprincip][create-nps-policy]markerar du även kryssrutan Ignorera egenskaper för **uppringning av användarkonton**.
1. Om du använder Windows Server 2019 för NPS-servern och Azure Multi-Factor Authentication NPS-tillägget kör du följande kommando för att uppdatera den säkra kanalen så att NPS-servern kan kommunicera korrekt:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Användarna uppmanas nu att ange ytterligare en autentiseringsfaktor när de loggar in, till exempel ett textmeddelande eller en uppmaning i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du förbättrar återhämtningen för distributionen finns i [Fjärrskrivbordstjänster – Hög tillgänglighet][rds-high-availability].

Mer information om hur du skyddar användarloggning finns i [Så här fungerar det: Azure Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
