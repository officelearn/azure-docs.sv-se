---
title: 'Azure Active Directory Domain Services: Administrationsguide | Microsoft Docs'
description: Skapa en organisationsenhet (OU) i Azure AD Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 49b915242ca0a4775236da3314eed816fde7ea6b
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856272"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Skapa en organisationsenhet (OU) i en Azure AD Domain Services-hanterad domän
Azure AD Domain Services-hanterade domäner omfattar två inbyggda behållarna som heter ”AADDC-datorer” och ”AADDC-användare” respektive. Behållaren AADDC-datorer har datorobjekt för alla datorer som är anslutna till den hanterade domänen. ”AADDC-användare”-behållaren innehåller användare och grupper i Azure AD-klient. Ibland kan vara det nödvändigt att skapa tjänstkonton i den hanterade domänen att distribuera arbetsbelastningar. Du kan skapa en anpassad organisationsenhet (OU) i den hanterade domänen och skapa tjänstkonton i denna Organisationsenhet för detta ändamål. Den här artikeln visar hur du skapar en Organisationsenhet i den hanterade domänen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Innan du börjar
Om du vill utföra åtgärderna i den här artikeln behöver du:

1. En giltig **Azure-prenumeration**.
2. En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
3. **Azure AD Domain Services** måste aktiveras för Azure AD-katalog. Om du inte gjort det, följer du alla uppgifter som beskrivs i den [komma igång-guiden](active-directory-ds-getting-started.md).
4. En domänansluten virtuell dator där du administrera Azure AD Domain Services-hanterade domän. Om du inte har sådan en virtuell dator, följer du alla uppgifter som beskrivs i artikeln [ansluta en Windows-dator till en hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md).
5. Du måste ha autentiseringsuppgifter för en **användarkonto som hör till gruppen ”AAD DC-administratörer”** i katalogen, skapa en anpassad Organisationsenhet på den hanterade domänen.

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installera AD verktyg för fjärrserveradministration på en virtuell dator ingår i domänen för fjärradministration
Azure AD Domain Services-hanterade domäner kan hanteras via en fjärranslutning med hjälp av välbekanta Active Directory-administrationsverktyg, till exempel Active Directory administrativa Center (ADAC) eller AD PowerShell. Innehavaradministratörer har inte behörighet för att ansluta till domänkontrollanter i den hanterade domänen via fjärrskrivbord. För att administrera den hanterade domänen kan du installera hanteringsverktygen för AD-administration på en virtuell dator som är anslutna till den hanterade domänen. Referera till artikeln [administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md) anvisningar.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Skapa en organisationsenhet i den hanterade domänen
Nu när AD Administrationsverktyg är installerade på domänanslutna virtuella datorer, vi kan använda dessa verktyg för att skapa en organisationsenhet i den hanterade domänen. Utför följande steg:

> [!NOTE]
> Endast medlemmar i gruppen ”AAD DC-administratörer” har rätt behörighet för att skapa en anpassad Organisationsenhet. Se till att du utför följande steg som en användare som tillhör den här gruppen.
>
>

1. På startsidan klickar du på **Administrationsverktyg**. Du bör se AD administrativa verktygen som installeras på den virtuella datorn.

    ![Administrativa verktyg som installerats på servern](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klicka på **Active Directory Administrationscenter**.

    ![Active Directory Administrationscenter](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Om du vill visa domänen, klickar du på domännamnet i den vänstra rutan (till exempel ”contoso100.com”).

    ![ADAC - Visa domän](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. På höger sida **uppgifter** fönstret klickar du på **New** under noden domän namn. I det här exemplet vi klickar du på **New** under noden ”contoso100(local)” till höger **uppgifter** fönstret.

    ![ADAC - ny Organisationsenhet](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. Du bör se alternativet för att skapa en organisationsenhet. Klicka på **organisationsenhet** att starta den **skapa organisationsenhet** dialogrutan.
6. I den **skapa organisationsenhet** dialogrutan, ange en **namn** för den nya Organisationsenheten. Ange en kort beskrivning för Organisationsenheten. Du kan också ange den **hanterad av** för Organisationsenheten. Klicka för att skapa anpassade Organisationsenheten **OK**.

    ![ADAC - OU dialogrutan Skapa](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. Den nyligen skapade Organisationsenheten bör nu visas i AD Administrative Center (ADAC).

    ![ADAC - Organisationsenhet som har skapats](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>Behörigheter/säkerhet för nya organisationsenheter
Som standard beviljas användaren (medlem i gruppen ”AAD DC-administratörer”) som skapade anpassade Organisationsenheten administrativa privilegier (fullständig behörighet) över Organisationsenheten. Användaren kan sedan gå vidare och ge behörigheter till andra användare eller till gruppen ”AAD DC-administratörer” efter behov. Som visas i skärmbilden, användaren 'bob@domainservicespreview.onmicrosoft.com”vem som skapade den nya 'MyCustomOU' organisationsenheten beviljas fullständig kontroll över den.

 ![ADAC - nya OU-säkerhet](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>Information om hur du administrerar anpassade organisationsenheter
Nu när du har skapat en anpassad Organisationsenhet kan du gå vidare och skapa användare, grupper, datorer och tjänstkonton i den här Organisationsenheten. Du kan inte flytta användare eller grupper från Organisationsenheten AADDC användare till anpassade organisationsenheter.

> [!WARNING]
> Användarkonton, grupper, service-konton och datorobjekt som du skapar med anpassade organisationsenheter är inte tillgängliga i Azure AD-klienten. Dessa objekt visas med andra ord inte upp med hjälp av Azure AD Graph-API eller i Användargränssnittet för Azure AD. Dessa objekt är endast tillgängliga i din Azure AD Domain Services-hanterad domän.
>
>

## <a name="related-content"></a>Relaterat innehåll
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Konfigurera en Grupprincip på en hanterad domän](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory Administrationscenter: Komma igång](https://technet.microsoft.com/library/dd560651.aspx)
* [Stegvisa instruktioner för tjänstkonton](https://technet.microsoft.com/library/dd548356.aspx)
