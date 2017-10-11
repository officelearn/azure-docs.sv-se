---
title: 'Azure Active Directory Domain Services: Administrationsguide | Microsoft Docs'
description: "Skapa en organisationsenhet (OU) i Azure AD Domain Services hanterade domäner"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 017a8cabe81743af4c0cbb694098df799a904468
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Skapa en organisationsenhet (OU) på en Azure AD Domain Services-hanterad domän
Azure AD Domain Services-hanterade domäner omfatta två inbyggda behållare som kallas för respektive AADDC-datorer och AADDC-användare. Behållaren AADDC-datorer har datorobjekt för alla datorer som är anslutna till den hanterade domänen. ' AADDC' i användarbehållaren innehåller användare och grupper i Azure AD-klient. Ibland kan vara det nödvändigt att skapa tjänstkonton på den hanterade domänen att distribuera arbetsbelastningar. Du kan skapa en egen organisationsenhet (OU) på den hanterade domänen och skapa tjänstkonton i denna Organisationsenhet för detta ändamål. Den här artikeln visar hur du skapar en Organisationsenhet i din hanterade domän.

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste vara aktiverat för Azure AD-katalog. Om du inte gjort det, följer du de uppgifter som beskrivs i den [Kom igång-guiden](active-directory-ds-getting-started.md).
4. En domänansluten virtuell dator där du administrera Azure AD Domain Services hanterade domän. Om du inte har sådan en virtuell dator, följer du de uppgifter som beskrivs i artikel med titeln [Anslut en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste autentiseringsuppgifterna för en **användarkontot som hör till gruppen ”AAD DC-administratörer”** i katalogen för att skapa en anpassad Organisationsenhet på din hanterade domän.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installera AD Administrationsverktyg på en domänansluten dator för fjärradministration
Azure AD Domain Services-hanterade domäner kan hanteras från en fjärrdator med hjälp av välbekanta Active Directory administrativa verktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. Innehavaradministratörer har inte behörighet att ansluta till domänkontrollanter på den hanterade domänen via fjärrskrivbord. Installera funktionen AD administration tools på en virtuell dator som är ansluten till den hanterade domänen för att administrera den hanterade domänen. Finns i artikeln [administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md) anvisningar.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Skapa en organisationsenhet i den hanterade domänen
Nu när AD Administrationsverktyg är installerade på domänanslutna virtuell dator, vi kan använda dessa verktyg för att skapa en organisationsenhet i den hanterade domänen. Utför följande steg:

> [!NOTE]
> Endast medlemmar i gruppen AAD DC-administratörer har de behörigheter som krävs för att skapa en anpassad Organisationsenhet. Se till att du utför följande steg som en användare som tillhör den här gruppen.
>
>

1. Klicka på startskärmen **Administrationsverktyg**. Du bör se AD administrativa verktyg som installerats på den virtuella datorn.

    ![Administrativa verktyg som installerats på servern](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klicka på **Active Directory Administrationscenter**.

    ![Active Directory Administrationscenter](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Om du vill visa domänen, klickar du på domännamnet i den vänstra rutan (till exempel ”contoso100.com”).

    ![ADAC - Visa domän](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. På höger sida **uppgifter** rutan klickar du på **ny** under noden domänens namn. I det här exemplet vi klickar du på **ny** under noden 'contoso100(local)' på höger sida **uppgifter** fönstret.

    ![ADAC - ny Organisationsenhet](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Du bör se alternativet för att skapa en organisationsenhet. Klicka på **organisationsenhet** att starta den **skapa organisationsenhet** dialogrutan.
6. I den **skapa organisationsenhet** dialogrutan, ange en **namn** för den nya Organisationsenheten. Ange en kort beskrivning för OU: N. Du kan också ange den **hanterad av** för Organisationsenheten. Klicka för att skapa anpassade Organisationsenheten **OK**.

    ![ADAC - OU dialogrutan Skapa](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Nyligen skapade OU: N bör nu visas i AD administrativa Center (ADAC).

    ![ADAC - Organisationsenhet som har skapats](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Behörigheter/säkerhet för nyskapade organisationsenheter
Som standard beviljas användaren (medlem i gruppen AAD DC-administratörer) som skapade den anpassa Organisationsenheten administratörsbehörighet (fullständig behörighet) över Organisationsenheten. Användaren kan sedan gå vidare och ge behörighet till andra användare eller AAD DC-administratörer grupp som du vill. Som det visas i följande skärmbild användaren 'bob@domainservicespreview.onmicrosoft.com' vem som skapade den nya 'MyCustomOU' organisationsenheten beviljas fullständig kontroll över den.

 ![ADAC - säkerhet för ny Organisationsenhet](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Information om hur du administrerar anpassade organisationsenheter
Nu när du har skapat en anpassad Organisationsenhet kan du gå vidare och skapa användare, grupper, datorer och tjänstkonton i Organisationsenheten. Du kan inte flytta användare eller grupper från Organisationsenheten AADDC användare till anpassade organisationsenheterna.

> [!WARNING]
> Användarkonton, grupper, tjänstkonton och datorobjekt som du skapar under anpassade organisationsenheter är inte tillgängliga i Azure AD-klienten. Dessa objekt visas med andra ord inte upp med hjälp av Azure AD Graph API eller i Azure AD-Gränssnittet. Objekten är bara tillgängliga i din Azure AD Domain Services-hanterad domän.
>
>

## <a name="related-content"></a>Relaterat innehåll
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Konfigurera en Grupprincip på en hanterad domän](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory Administrationscenter: Komma igång](https://technet.microsoft.com/library/dd560651.aspx)
* [Stegvisa instruktioner för tjänstkonton](https://technet.microsoft.com/library/dd548356.aspx)
