---
title: Hantera DNS för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du installerar DNS-serverns verktyg för att hantera DNS för en Azure Active Directory Domain Services hanterad domän.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704939"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Administrera DNS i en Azure AD Domain Services hanterad domän

I Azure Active Directory Domain Services (Azure AD DS) är en nyckel komponent DNS (domän namns matchning). Azure AD DS innehåller en DNS-server som tillhandahåller namn matchning för den hanterade domänen. Den här DNS-servern innehåller inbyggda DNS-poster och uppdateringar för de viktiga komponenter som gör att tjänsten kan köras.

När du kör dina egna program och tjänster kan du behöva skapa DNS-poster för datorer som inte är anslutna till domänen, konfigurera virtuella IP-adresser för belastningsutjämnare eller konfigurera externa DNS-vidarebefordrare. Användare som tillhör gruppen *AAD DC-administratörer* beviljas behörighet för DNS-administration på den hanterade domänen i Azure AD DS och kan skapa och redigera anpassade DNS-poster.

I en hybrid miljö synkroniseras inte DNS-zoner och-poster som kon figurer ATS i en lokal AD DS-miljö med Azure AD DS. Om du vill definiera och använda dina egna DNS-poster skapar du poster i Azure AD DS DNS-servern eller använder villkorliga vidarebefordrare som pekar på befintliga DNS-servrar i din miljö.

Den här artikeln visar hur du installerar verktyg för DNS-server och sedan använder DNS-konsolen för att hantera poster i Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
    * Om det behövs, slutför du själv studie kursen för att [skapa en virtuell Windows Server-dator och koppla den till en hanterad domän][create-join-windows-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="install-dns-server-tools"></a>Installera verktyg för DNS-Server

Om du vill skapa och ändra DNS-poster i Azure AD DS måste du installera verktyg för DNS-server. Dessa verktyg kan installeras som en funktion i Windows Server. Mer information om hur du installerar administrations verktyg på en Windows-klient finns i installera [verktyg för fjärrserveradministration (RSAT)][install-rsat].

1. Logga in på den virtuella hanterings datorn. Anvisningar om hur du ansluter med hjälp av Azure Portal finns i [ansluta till en virtuell Windows Server-dator][connect-windows-server-vm].
1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start** -menyn och väljer **Serverhanteraren**.
1. I fönstret *instrument panel* i fönstret **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
1. På sidan **innan du börjar** i *guiden Lägg till roller och funktioner*väljer du **Nästa**.
1. För *installations typen*låter du alternativet för **rollbaserad eller funktions baserad installation** vara markerat och väljer **Nästa**.
1. På sidan **Server val** väljer du den aktuella virtuella datorn från serverpoolen, till exempel *myvm.aadds.contoso.com*, och väljer sedan **Nästa**.
1. På sidan **Server roller** klickar du på **Nästa**.
1. På sidan **funktioner** expanderar du noden **verktyg för fjärrserveradministration** och expandera sedan noden **roll administrations verktyg** . Välj funktionen **verktyg för DNS-Server** från listan över roll administrations verktyg.

    ![Välj att installera verktyg för DNS-server från listan över tillgängliga roll administrations verktyg](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. På sidan **bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera grupprincip hanterings verktyg.
1. När funktions installationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Öppna DNS-hanteringskonsolen för att administrera DNS

Med verktyg för DNS-server installerat kan du administrera DNS-poster på den hanterade domänen i Azure AD DS.

> [!NOTE]
> Om du vill administrera DNS i en Azure AD DS-hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **administrations verktyg**på Start skärmen. En lista över tillgängliga hanterings verktyg visas, inklusive **DNS** installerat i föregående avsnitt. Välj **DNS** för att starta konsolen DNS-hantering.
1. I dialog rutan **Anslut till DNS-Server** väljer **du följande dator**och anger sedan DNS-domännamnet för den hanterade domänen, till exempel *aadds.contoso.com*:

    ![Ansluta till den hanterade Azure AD DS-domänen i DNS-konsolen](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS-konsolen ansluter till den angivna Azure AD DS-hanterade domänen. Expandera zoner för **vanlig sökning** eller **zoner för omvänd sökning** för att skapa DNS-poster som krävs eller redigera befintliga poster efter behov.

    ![DNS-konsol – administrera domän](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> När du hanterar poster med hjälp av verktyg för DNS-server, se till att du inte tar bort eller ändrar de inbyggda DNS-posterna som används av Azure AD DS. Inbyggda DNS-poster inkluderar domän-DNS-poster, namn server poster och andra poster som används för lokalisering av domänkontrollant. Om du ändrar dessa poster avbryts domän tjänster på det virtuella nätverket.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar DNS finns i [artikeln DNS-verktyg på TechNet](https://technet.microsoft.com/library/cc753579.aspx).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
