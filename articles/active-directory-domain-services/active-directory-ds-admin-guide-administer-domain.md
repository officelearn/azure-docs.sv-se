---
title: 'Azure Active Directory Domain Services: Administrera en hanterad domän | Microsoft Docs'
description: Administrera Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 2ee5250147a82199057a3bf6f043627616e7443d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333694"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrera en Azure Active Directory Domain Services-hanterad domän
Den här artikeln visar hur du administrerar en Azure Active Directory (AD) Domain Services-hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Du behöver följande för att slutföra aktiviteterna i den här artikeln:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. En **domänanslutna virtuella** där du administrera den hanterade domänen med Azure AD Domain Services. Om du inte har sådan en virtuell dator, följer du de uppgifter som beskrivs i artikel med titeln [Anslut en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste autentiseringsuppgifterna för en **användarkontot som hör till gruppen ”AAD DC-administratörer”** i katalogen för att administrera din hanterade domän.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Administrativa uppgifter som du kan utföra på en hanterad domän
Medlemmar i gruppen AAD DC-administratörer beviljas behörighet för den hanterade domänen som gör det möjligt för dem att utföra uppgifter som:

* Ansluta datorer till den hanterade domänen.
* Konfigurera det inbyggda grupprincipobjektet för behållarna ”AADDC-datorer” och ”AADDC-användare” i den hanterade domänen.
* Administrera DNS i den hanterade domänen.
* Skapa och administrera anpassade organisationsenheter (OU) på den hanterade domänen.
* Få administrativ åtkomst till datorer som är anslutna till den hanterade domänen.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Administratörsbehörighet behöver du inte på en hanterad domän
Domänen hanteras av Microsoft, inklusive aktiviteter, till exempel korrigering, övervakning och säkerhetskopiering. Domänen är låst och du har inte behörighet att utföra vissa administrativa uppgifter i domänen. Det är några exempel på uppgifter som du inte kan göra nedan.

* Du har inte behörighet för domänadministratör eller företagsadministratör för den hanterade domänen.
* Du kan inte utöka schemat för den hanterade domänen.
* Du kan inte ansluta till domänkontrollanterna för den hanterade domänen med hjälp av fjärrskrivbord.
* Du kan inte lägga till domänkontrollanter i den hanterade domänen.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Uppgift 1 – skapa en domänansluten Windows Server-datorn för att fjärradministrera den hanterade domänen
Azure AD Domain Services-hanterade domäner kan hanteras med hjälp av välbekanta Active Directory administrativa verktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. Innehavaradministratörer har inte behörighet att ansluta till domänkontrollanter på den hanterade domänen via fjärrskrivbord. Medlemmar i gruppen AAD DC-administratörer kan administrera hanterade domäner med Administrationsverktyg för AD från en Windows Server/klientdator som är ansluten till den hanterade domänen. Administrationsverktyg för AD kan installeras som en del av Remote verktyg för fjärrserveradministration (RSAT) valfri funktion i Windows Server och klientdatorer som är anslutna till den hanterade domänen.

Det första steget är att konfigurera en virtuell dator för Windows Server som är ansluten till den hanterade domänen. Instruktioner finns i artikeln [ansluta en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Fjärradministrera den hanterade domänen från en klientdator (till exempel Windows 10)
Instruktionerna i den här artikeln används en virtuell dator med Windows Server för att administrera AAD-DS hanterade domän. Du kan också välja att använda en virtuell dator för Windows-klienter (till exempel Windows 10) gör.

Du kan [installera fjärråtkomst verktyg för fjärrserveradministration (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) på en Windows-klient virtuell dator genom att följa anvisningarna på TechNet.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Uppgift 2 – Installera Active Directory-Administrationsverktyg på den virtuella datorn
Utför följande steg för att installera administrationsverktygen för Active Directory på den virtuella datorn ansluten till en domän. Mer information finns på Technet [information om hur du installerar och använder Remote Server Administration Tools](https://technet.microsoft.com/library/hh831501.aspx).

1. Gå till Azure-portalen. Klicka på **alla resurser** i den vänstra rutan. Leta upp och klicka på den virtuella datorn som du skapade i uppgift 1.
2. Klicka på den **Anslut** på fliken Översikt. En Remote Desktop Protocol (RDP)-fil skapas och hämtas.

    ![Ansluta till Windows-dator](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om du uppmanas till detta klickar du på **Anslut**. Använd autentiseringsuppgifter för en användare som tillhör gruppen AAD DC-administratörer. Till exempel 'bob@domainservicespreview.onmicrosoft.com'. Du kan få en certifikatvarning under inloggningen. Klicka på Ja eller fortsätta att fortsätta med anslutningen.
4. Från startskärmen öppnar **Serverhanteraren**. Klicka på **Lägg till roller och funktioner** i fönstret i mitten av Serverhanteraren.

    ![Starta Serverhanteraren på den virtuella datorn](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. På den **innan du börjar** sida av den **guiden Lägg till roller och funktioner**, klickar du på **nästa**.

    ![Innan du börjar sida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. På den **installationstyp** lämnar den **rollbaserad eller funktionsbaserad installation** alternativet som är markerat och klicka på **nästa**.

    ![Installationstyp](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. På den **Serverval** , Välj den aktuella virtuella datorn från serverpoolen och klicka på **nästa**.

    ![Sidan för val av Server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. På den **serverroller** klickar du på **nästa**.
9. På den **funktioner** sidan, klicka på och expandera den **Remote Server Administration Tools** noden och sedan klicka på och expandera den **Rolladministrationsverktyg** nod. Välj **AD DS och AD LDS-verktyg** funktionen från listan över Rolladministrationsverktyg.

    ![Sidan funktioner](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. På den **bekräftelse** klickar du på **installera** att installera AD och funktionen för AD LDS-verktyg på den virtuella datorn. När för funktionsinstallationen är klar klickar du på **Stäng** att avsluta den **Lägg till roller och funktioner** guiden.

    ![Bekräftelsesida](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Uppgift 3 – ansluta till och utforska den hanterade domänen
Du kan nu använda Administrationsverktyg för Windows Server AD att utforska och administrera den hanterade domänen.

> [!NOTE]
> Du måste vara medlem i gruppen AAD DC-administratörer för att administrera hanterade domän.
>
>

1. Klicka på startskärmen **Administrationsverktyg**. Du bör se AD administrativa verktyg som installerats på den virtuella datorn.

    ![Administrativa verktyg som installerats på servern](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klicka på **Active Directory Administrationscenter**.

    ![Active Directory Administrationscenter](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Om du vill utforska domänen, klickar du på domännamnet i den vänstra rutan (till exempel ”contoso100.com”). Meddelande två behållare kallas AADDC-datorer och AADDC-användare.

    ![ADAC - Visa domän](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Klicka på den behållare som kallas **AADDC användare** visa alla användare och grupper som tillhör den hanterade domänen. Du bör se användarkonton och grupper från din Azure AD-klient visa upp i den här behållaren. I det här exemplet, ett användarkonto för användare som kallas ”bob” och en grupp med namnet AAD DC-administratörer är tillgängliga i den här behållaren.

    ![ADAC - domänanvändare](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Klicka på den behållare som kallas **AADDC datorer** vill se datorer som är anslutna till den här hanterade domänen. Du bör se en post för den aktuella virtuella datorn som är ansluten till domänen. Datorkontona för alla datorer som är anslutna till Azure AD Domain Services-hanterad domän som lagras i den här behållaren AADDC-datorer.

    ![ADAC - domänanslutna datorer](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Anslut en virtuell dator med Windows Server till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Distribuera fjärradministrationsverktyg för Server](https://technet.microsoft.com/library/hh831501.aspx)
