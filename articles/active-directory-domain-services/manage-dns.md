---
title: Hantera DNS för Azure AD-domäntjänster | Microsoft-dokument
description: Lär dig hur du installerar DNS-serververktygen för att hantera DNS och skapa villkorliga vidarebefordrare för en hanterad Azure Active Directory Domain Services-domän.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603515"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>Administrera DNS och skapa villkorsbefordreringsbefordreare i en hanterad Azure AD Domain Services-domän

I Azure Active Directory Domain Services (Azure AD DS) är en nyckelkomponent DNS (Domännamnsmatchning). Azure AD DS innehåller en DNS-server som tillhandahåller namnmatchning för den hanterade domänen. Den här DNS-servern innehåller inbyggda DNS-poster och uppdateringar för de nyckelkomponenter som gör att tjänsten kan köras.

När du kör dina egna program och tjänster kan du behöva skapa DNS-poster för datorer som inte är anslutna till domänen, konfigurera virtuella IP-adresser för belastningsutjämnare eller konfigurera externa DNS-vidarebefordrare. Användare som tillhör gruppen *AAD DC-administratörer* beviljas DNS-administrationsbehörighet på azure AD DS-hanterad domän och kan skapa och redigera anpassade DNS-poster.

I en hybridmiljö synkroniseras inte DNS-zoner och poster som konfigurerats i andra DNS-namnområden, till exempel en lokal AD DS-miljö, till Azure AD DS. Om du vill lösa namngivna resurser i andra DNS-namnområden skapar och använder du villkorliga vidarebefordrare som pekar på befintliga DNS-servrar i din miljö.

Den här artikeln visar hur du installerar DNS-serververktygen och sedan använda DNS-konsolen för att hantera poster och skapa villkorsstyrda vidarebefordrare i Azure AD DS.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Anslutning från ditt virtuella Azure AD DS-nätverk till där dina andra DNS-namnområden finns.
    * Den här anslutningen kan tillhandahållas med en [Azure ExpressRoute-][expressroute] eller [Azure VPN Gateway-anslutning.][vpn-gateway]
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs slutför du självstudien för att [skapa en virtuell Windows Server-dator och ansluta den till en hanterad domän][create-join-windows-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

## <a name="install-dns-server-tools"></a>Installera DNS Server-verktyg

Om du vill skapa och ändra DNS-poster i Azure AD DS måste du installera DNS-serververktygen. Dessa verktyg kan installeras som en funktion i Windows Server. Mer information om hur du installerar administrationsverktygen på en Windows-klient finns i installera [RSAT (Remote Server Administration Tools).][install-rsat]

1. Logga in på den virtuella datorn för hantering. Steg om hur du ansluter med Azure-portalen finns i [Ansluta till en Virtuell Windows Server][connect-windows-server-vm].
1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start-menyn** och väljer sedan **Serverhanteraren**.
1. Välj **Lägg till roller och funktioner**i *instrumentpanelsfönstret* i **serverhanteraren.**
1. På sidan **Innan du börjar** i guiden Lägg till roller och *funktioner*väljer du **Nästa**.
1. För *installationstypen*lämnar du alternativet **Rollbaserad eller funktionsbaserad installation** markerat och väljer **Nästa**.
1. På sidan **Serverval** väljer du den aktuella virtuella datorn i serverpoolen, till exempel *myvm.aaddscontoso.com*och väljer sedan **Nästa**.
1. Klicka på **Nästa**på sidan **Serverroller** .
1. Expandera noden **Administrationsverktyg** för fjärrserver på sidan **Funktioner** och expandera sedan noden Verktyg **för rolladministrationsverktyg.** Välj **funktionen DNS Server Tools** i listan över rolladministrationsverktyg.

    ![Välj att installera DNS-serververktygen i listan över tillgängliga rolladministrationsverktyg](./media/manage-dns/install-dns-tools.png)

1. På sidan **Bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera verktygen för grupprinciphantering.
1. När funktionsinstallationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner.**

## <a name="open-the-dns-management-console-to-administer-dns"></a>Öppna DNS-hanteringskonsolen för att administrera DNS

Med DNS-serververktygen installerade kan du administrera DNS-poster på azure AD DS-hanterade domänen.

> [!NOTE]
> Om du vill administrera DNS i en Azure AD DS-hanterad domän måste du vara inloggad på ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Välj **Administrationsverktyg**på Startskärmen . En lista över tillgängliga hanteringsverktyg visas, inklusive **DNS som** är installerad i föregående avsnitt. Välj **DNS** för att starta DNS-hanteringskonsolen.
1. I dialogrutan **Anslut till DNS-server** väljer du **Följande dator**och anger sedan DNS-domännamnet för den hanterade domänen, till exempel *aaddscontoso.com:*

    ![Ansluta till azure AD DS-hanterad domän i DNS-konsolen](./media/manage-dns/connect-dns-server.png)

1. DNS-konsolen ansluter till den angivna Azure AD DS-hanterade domänen. Expandera **zoner för framåtsökning** eller **omvänd sökning** om du vill skapa önskade DNS-poster eller redigera befintliga poster efter behov.

    ![DNS-konsolen – administrera domän](./media/manage-dns/dns-manager.png)

> [!WARNING]
> När du hanterar poster med DNS-serververktygen kontrollerar du att du inte tar bort eller ändrar de inbyggda DNS-poster som används av Azure AD DS. Inbyggda DNS-poster omfattar DNS-poster för domäner, namnserverposter och andra poster som används för DC-plats. Om du ändrar dessa poster störs domäntjänster i det virtuella nätverket.

## <a name="create-conditional-forwarders"></a>Skapa villkorade vidarebefordrare

En Azure AD DS DNS-zon bör endast innehålla zonen och posterna för den hanterade domänen själv. Skapa inte ytterligare zoner i Azure AD DS för att matcha namngivna resurser i andra DNS-namnområden. Använd i stället villkorliga vidarebefordrare i Azure AD DS-hanterade domänen för att tala om för DNS-servern vart de ska gå för att matcha adresser för dessa resurser.

En villkorlig vidarebefordrare är ett konfigurationsalternativ på en DNS-server som gör att du kan definiera en DNS-domän, till exempel *contoso.com*, att vidarebefordra frågor till. I stället för att den lokala DNS-servern försöker lösa frågor för poster i den domänen vidarebefordras DNS-frågor till den konfigurerade DNS-servern för den domänen. Den här konfigurationen säkerställer att rätt DNS-poster returneras, eftersom du inte skapar en lokal DNS-zon med dubblettposter i Azure AD DS-hanterade domänen för att återspegla dessa resurser.

Så här skapar du en villkorlig vidarebefordrare i din Azure AD DS-hanterade domän:

1. Välj din Azure AD DS DNS-zon, till exempel *aaddscontoso.com*.vb
1. Välj **Villkorliga vidarebefordrare**och välj sedan **Ny villkorlig vidarebefordrare...**
1. Ange din andra **DNS-domän,** till exempel *contoso.com*, och ange sedan DNS-servrarnas IP-adresser för det namnområdet, vilket visas i följande exempel:

    ![Lägga till och konfigurera en villkorlig vidarebefordrare för DNS-servern](./media/manage-dns/create-conditional-forwarder.png)

1. Markera kryssrutan för **Store den här villkorliga vidarebefordraren i Active Directory och replikera den på följande sätt**och välj sedan alternativet för alla *DNS-servrar i den här domänen*, som visas i följande exempel:

    ![DNS-konsolen – administrera domän](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Om den villkorliga vidarebefordraren lagras i *skogen* i stället för *domänen*misslyckas den villkorliga vidarebefordraren.

1. Om du vill skapa den villkorliga vidarebefordraren väljer du **OK**.

Namnmatchning av resurser i andra namnområden från virtuella datorer som är anslutna till azure AD DS-hanterad domän bör nu matchas korrekt. Frågor om DNS-domänen som konfigurerats i den villkorliga vidarebefordraren skickas till relevanta DNS-servrar.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar DNS finns i [artikeln DNS-verktyg på Technet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
