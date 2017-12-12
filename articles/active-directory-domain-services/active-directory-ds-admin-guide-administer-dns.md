---
title: "Azure Active Directory Domain Services: Administrera DNS på hanterade domäner | Microsoft Docs"
description: "Administrera DNS på Azure Active Directory Domain Services hanterade domäner"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 55b6368d55b5d0ad50d066a4963e74d8c44a2049
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrera DNS på en Azure AD Domain Services-hanterad domän
Azure Active Directory Domain Services innehåller en DNS (Domain Name Resolution)-server som tillhandahåller DNS-matchning för den hanterade domänen. Ibland kan behöva du konfigurera DNS på den hanterade domänen. Du kan behöva skapa DNS-poster för datorer som inte är anslutna till domänen, konfigurera den virtuella IP-adresser för belastningsutjämnare eller konfigurera externa DNS-vidarebefordrare. Därför kan beviljas användare som tillhör gruppen AAD DC-administratörer DNS administratörsbehörighet för den hanterade domänen.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. En **domänanslutna virtuella** där du administrera den hanterade domänen med Azure AD Domain Services. Om du inte har sådan en virtuell dator, följer du de uppgifter som beskrivs i artikel med titeln [Anslut en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste autentiseringsuppgifterna för en **användarkontot som hör till gruppen ”AAD DC-administratörer”** i katalogen för att administrera DNS för din hanterade domän.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Uppgift 1 – etablera en virtuell dator domänanslutna för att fjärradministrera DNS för den hanterade domänen
Azure AD Domain Services-hanterade domäner kan hanteras från en fjärrdator med hjälp av välbekanta Active Directory administrativa verktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. På liknande sätt DNS för den hanterade domänen kan du administrera med administrationsverktygen för DNS-Server.

Administratörer i din Azure AD-katalog har inte behörighet att ansluta till domänkontrollanter på den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen AAD DC-administratörer kan administrera DNS för hanterade domäner med DNS-Server-verktyg från en Windows Server/klientdator som är ansluten till den hanterade domänen. Verktyg för DNS-Server kan installeras som en del av Remote verktyg för fjärrserveradministration (RSAT) valfri funktion i Windows Server och klientdatorer som är anslutna till den hanterade domänen.

Den första uppgiften är att etablera en virtuell dator med Windows Server som är ansluten till den hanterade domänen. Instruktioner finns i artikeln [ansluta en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Uppgift 2 – installera DNS-Server tools på den virtuella datorn
Utför följande steg för att installera administrationsverktygen för DNS på den virtuella datorn ansluten till en domän. Mer information om [installera och använda Administrationsverktyg för fjärrserver](https://technet.microsoft.com/library/hh831501.aspx), finns på Technet.

1. Gå till Azure-portalen. Klicka på **alla resurser** i den vänstra rutan. Leta upp och klicka på den virtuella datorn som du skapade i uppgift 1.
2. Klicka på den **Anslut** på fliken Översikt. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. Inloggningsskärm, Använd autentiseringsuppgifter för en användare som tillhör gruppen AAD DC-administratörer. Exempelvis kan vi använda 'bob@domainservicespreview.onmicrosoft.com' i vårt fall. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att fortsätta med anslutningen.

4. Från startskärmen öppnar **Serverhanteraren**. Klicka på **Lägg till roller och funktioner** i fönstret i mitten av Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På den **innan du börjar** sida av den **guiden Lägg till roller och funktioner**, klickar du på **nästa**.

    ![Innan du börjar sida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På den **installationstyp** lämnar den **rollbaserad eller funktionsbaserad installation** alternativet som är markerat och klicka på **nästa**.

    ![Installationstyp](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På den **Serverval** , Välj den aktuella virtuella datorn från serverpoolen och klicka på **nästa**.

    ![Sidan för val av Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På den **serverroller** klickar du på **nästa**. Vi hoppar över den här sidan eftersom vi inte installerar några roller på servern.
9. På den **funktioner** sidan, klicka på och expandera den **Remote Server Administration Tools** noden och sedan klicka på och expandera den **Rolladministrationsverktyg** nod. Välj **DNS-serververktyg** funktionen från listan över Rolladministrationsverktyg.

    ![Sidan funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. På den **bekräftelse** klickar du på **installera** du installerar verktygsfunktionen för DNS-Server på den virtuella datorn. När för funktionsinstallationen är klar klickar du på **Stäng** att avsluta den **Lägg till roller och funktioner** guiden.

    ![Bekräftelsesida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Uppgift 3 – Starta DNS-hanteringskonsolen för att administrera DNS
Funktionen Verktyg för DNS-Server är installerat på domänanslutna virtuell dator, vi kan använda DNS-verktyg för att administrera DNS på den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen AAD DC-administratörer för att administrera DNS på den hanterade domänen.
>
>

1. Klicka på startskärmen **Administrationsverktyg**. Du bör se den **DNS** konsolen har installerats på den virtuella datorn.

    ![Administrativa verktyg - DNS-konsolen](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Klicka på **DNS** att starta DNS-hanteringskonsolen.
3. I den **Anslut till DNS-Server** dialogrutan klickar du på alternativet **följande dator**, och ange DNS-domännamnet för den hanterade domänen (till exempel ”contoso100.com”).

    ![DNS-konsolen - Anslut till domän](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS-konsolen ansluter till den hanterade domänen.

    ![DNS-konsolen – administrera domän](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Du kan nu använda DNS-konsolen för att lägga till DNS-poster för datorer i det virtuella nätverket som du har aktiverat AAD Domain Services.

> [!WARNING]
> Var försiktig när du administrerar DNS för den hanterade domänen med hjälp av Administrationsverktyg för DNS. Se till att du **inte ta bort eller ändra de inbyggda DNS-poster som används av Domain Services i domänen**. Inbyggda DNS-poster är DNS-poster för domänen, namnserverposter och andra poster som används för DC-plats. Om du ändrar dessa poster upplöst domäntjänster på det virtuella nätverket.
>
>

Finns det [DNS-verktyg artikel på Technet](https://technet.microsoft.com/library/cc753579.aspx) för mer information om hur du hanterar DNS.

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Anslut en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Administrationsverktyg för DNS](https://technet.microsoft.com/library/cc753579.aspx)
