---
title: 'Azure Active Directory Domain Services: Administrera DNS på hanterade domäner | Microsoft Docs'
description: Administrera DNS på hanterade domäner i Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f20b2859f72087e208e8963fb18b297c7c670f4f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504292"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrera DNS på en Azure AD Domain Services-hanterad domän
Azure Active Directory Domain Services innehåller en DNS (Domain Name Resolution)-server som tillhandahåller DNS-matchning för den hanterade domänen. Ibland kan behöva du konfigurera DNS på den hanterade domänen. Du kan behöva skapa DNS-poster för datorer som inte är anslutna till domänen, konfigurera den virtuella IP-adresser för belastningsutjämnare eller konfigurera externa DNS-vidarebefordrare. Därför måste beviljas användare som tillhör gruppen ”AAD DC-administratörer” DNS administratörsbehörighet för den hanterade domänen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Du behöver följande för att slutföra de uppgifter som anges i den här artikeln:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. En **domänansluten VM** varifrån du administrerar den hanterade domänen i Azure AD Domain Services. Om du inte har sådan en virtuell dator, följer du alla uppgifter som beskrivs i artikeln [ansluta en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste ha autentiseringsuppgifter för en **användarkonto som hör till gruppen ”AAD DC-administratörer”** i katalogen för att administrera DNS för din hanterade domän.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Uppgift 1 – skapa en virtuell dator ingår i domänen för att fjärradministrera DNS för den hanterade domänen
Azure AD Domain Services-hanterade domäner kan hanteras via en fjärranslutning med hjälp av välbekanta Active Directory-administrationsverktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. På samma sätt kan DNS för den hanterade domänen fjärradministreras med administrationsverktygen för DNS-Server.

Administratörer i Azure AD-katalogen har inte behörighet för att ansluta till domänkontrollanter i den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen ”AAD DC-administratörer” kan administrera DNS för hanterade domäner via en fjärranslutning med hjälp av DNS-Server-verktyg från en Windows Server/klientdator som är ansluten till den hanterade domänen. DNS-Server-verktyg är en del av funktionen för valfria Remote verktyg för fjärrserveradministration (RSAT).

Den första uppgiften är att skapa en Windows Server-dator som är ansluten till den hanterade domänen. Mer information finns i artikeln [ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Uppgift 2 – installera DNS-serververktyg på den virtuella datorn
Utför följande steg för att installera administrationsverktygen för DNS på den domänanslutna virtuella datorn. Mer information om [installera och använda Administrationsverktyg för fjärrserver](https://technet.microsoft.com/library/hh831501.aspx), finns på Technet.

1. Gå till Azure-portalen. Klicka på **alla resurser** på den vänstra panelen. Leta upp och klicka på den virtuella datorn som du skapade i uppgift 1.
2. Klicka på den **Connect** på fliken Översikt. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. Använd autentiseringsuppgifterna för en användare som tillhör gruppen ”AAD DC-administratörer”. Till exempel ”bob@domainservicespreview.onmicrosoft.com”. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att ansluta.

4. Från startskärmen öppnar **Serverhanteraren**. Klicka på **Lägg till roller och funktioner** i fönstret i mitten av Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På den **innan du börjar** för den **guiden Lägg till roller och funktioner**, klickar du på **nästa**.

    ![Innan du börjar sidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På den **installationstyp** lämnar den **rollbaserad eller funktionsbaserad installation** alternativet är markerat och klicka på **nästa**.

    ![Installationstyp](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På den **Serverval** , Välj den aktuella virtuella datorn från serverpoolen och klicka på **nästa**.

    ![Sida för val av Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På den **serverroller** klickar du på **nästa**.
9. På den **funktioner** sidan, klicka på och expandera den **verktyg för fjärrserveradministration** noden och klicka sedan på Expandera den **Rolladministrationsverktyg** noden. Välj **DNS-serververktyg** funktion i listan med Rolladministrationsverktyg.

    ![Funktionssidan](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. På den **bekräftelse** klickar du på **installera** installera hanteringsverktygen för DNS-Server på den virtuella datorn. När funktionsinstallationen är klar, klickar du på **Stäng** att avsluta den **Lägg till roller och funktioner** guiden.

    ![Bekräftelsesida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Uppgift 3 – Starta DNS-hanteringskonsolen för att administrera DNS
Nu, kan du använda Windows Server-DNS-verktyg för att administrera DNS på den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen ”AAD DC-administratörer” för att administrera DNS på den hanterade domänen.
>
>

1. På startsidan klickar du på **Administrationsverktyg**. Du bör se den **DNS** konsolen har installerats på den virtuella datorn.

    ![Administrativa verktyg - DNS-konsolen](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Klicka på **DNS** att starta DNS-hanteringskonsolen.
3. I den **Anslut till DNS-Server** dialogrutan klickar du på **följande dator**, och ange DNS-domännamnet för den hanterade domänen (till exempel ”contoso100.com”).

    ![DNS-konsolen – Anslut till domän](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS-konsolen ansluter till den hanterade domänen.

    ![DNS-konsolen – administrera domän](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Du kan nu använda DNS-konsolen för att lägga till DNS-poster för datorerna i det virtuella nätverket där du har aktiverat AAD Domain Services.

> [!WARNING]
> Var försiktig när du administrerar DNS för den hanterade domänen med hjälp av DNS-verktyg för fjärrserveradministration. Se till att du **inte bort eller ändra inbyggd DNS-posterna som används av Domain Services i domänen**. Inbyggd DNS-poster är DNS-poster för domänen, namnserverposterna och andra poster som används för DC-plats. Om du ändrar dessa poster följd domäntjänster på det virtuella nätverket.
>
>

Mer information om hur du hanterar DNS finns i den [DNS-verktyg artikel på Technet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Ansluta en Windows Server-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [DNS-verktyg för fjärrserveradministration](https://technet.microsoft.com/library/cc753579.aspx)
