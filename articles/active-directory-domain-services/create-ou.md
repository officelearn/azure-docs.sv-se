---
title: Skapa en organisationsenhet (OU) i Azure AD Domain Services | Microsoft Dokument"
description: Lär dig hur du skapar och hanterar en anpassad organisationsenhet (OU) i en hanterad Azure AD-domäntjänst.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 4b95a3e32bc2b8df3d02453e42fa9bbc3719134b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519163"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Skapa en organisationsenhet (OU) i en hanterad Azure AD-domäntjänst

Med organisationsenheter i AD DS (Active Directory Domain Services) kan du logiskt gruppera objekt som användarkonton, tjänstkonton eller datorkonton. Du kan sedan tilldela administratörer till specifika ru:er och tillämpa grupprincip för att framtvinga riktade konfigurationsinställningar.

Azure AD DS-hanterade domäner innehåller följande två inbyggda AU:

* *AADDC-datorer* - innehåller datorobjekt för alla datorer som är anslutna till den hanterade domänen.
* *AADDC-användare* - innehåller användare och grupper synkroniserade från Azure AD-klienten.

När du skapar och kör arbetsbelastningar som använder Azure AD DS kan du behöva skapa tjänstkonton för program för att autentisera sig själva. Om du vill ordna dessa tjänstkonton skapar du ofta en anpassad organisationsenhet i den Hanterade Azure AD DS-domänen och skapar sedan tjänstkonton inom den organisationsenhetsenheten.

I en hybridmiljö synkroniseras inte OUs som skapats i en lokal AD DS-miljö till Azure AD DS. Azure AD DS-hanterade domäner använder en platt organisationsenhetsstruktur. Alla användarkonton och grupper lagras i behållaren *AADDC-användare,* trots att de synkroniseras från olika lokala domäner eller skogar, även om du har konfigurerat en hierarkisk organisationsenhetsstruktur där.

Den här artikeln visar hur du skapar en organisationsenhet i din Azure AD DS-hanterade domän.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* En virtuell windows serverhantering som är ansluten till den Hanterade Azure AD DS-domänen.
    * Om det behövs slutför du självstudien för att [skapa en vm-hantering][tutorial-create-management-vm].
* Ett användarkonto som är medlem i azure *AD DC-administratörsgruppen* i din Azure AD-klientorganisation.

## <a name="custom-ou-considerations-and-limitations"></a>Anpassade överväganden och begränsningar för organisation av organisationsenhet

När du skapar anpassade företagsenheter i en Azure AD DS-hanterad domän får du ytterligare hanteringsflexibilitet för användarhantering och tillämpa grupprinciper. Jämfört med en lokal AD DS-miljö finns det vissa begränsningar och överväganden när du skapar och hanterar en anpassad organisationsenhetsstruktur i Azure AD DS:

* Om du vill skapa anpassade företagsenheter måste användarna vara medlemmar i gruppen *AAD DC-administratörer.*
* En användare som skapar en anpassad organisationsenhet beviljas administratörsbehörighet (fullständig kontroll) över organisationsenheten och är resursägare.
    * Som standard har gruppen *AAD DC-administratörer* också full kontroll över den anpassade organisationsenheten.
* En standardorganisation för *AADDC-användare* skapas som innehåller alla synkroniserade användarkonton från din Azure AD-klientorganisation.
    * Du kan inte flytta användare eller grupper från organisationsenheten för användare av *AADDC* till anpassade organisationsenheter som du skapar. Endast användarkonton eller resurser som skapats i Azure AD DS-hanterad domän kan flyttas till anpassade räs.
* Användarkonton, grupper, tjänstkonton och datorobjekt som du skapar under anpassade företagsenheter är inte tillgängliga i din Azure AD-klientorganisation.
    * Dessa objekt visas inte med Microsoft Graph API eller i Azure AD-användargränssnittet. De är bara tillgängliga i din Azure AD DS-hanterade domän.

## <a name="create-a-custom-ou"></a>Skapa en anpassad organisationsenhet

Om du vill skapa en anpassad organisationsenhet använder du Administrationsverktygen för Active Directory från en domänansluten virtuell dator. Med Active Directory Administrationscenter kan du visa, redigera och skapa resurser i en Azure AD DS-hanterad domän, inklusive ru: er.

> [!NOTE]
> Om du vill skapa en anpassad organisationsenhet i en Azure AD DS-hanterad domän måste du vara inloggad på ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Logga in på den virtuella datorn för hantering. Steg om hur du ansluter med Azure-portalen finns i [Ansluta till en Virtuell Windows Server][connect-windows-server-vm].
1. Välj **Administrationsverktyg**på Startskärmen . En lista över tillgängliga hanteringsverktyg visas som installerades i självstudien för att [skapa en hantering VM][tutorial-create-management-vm].
1. Om du vill skapa och hantera ru:er väljer du **Administrationscenter** för Active Directory i listan över administrativa verktyg.
1. I den vänstra rutan väljer du din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*. En lista över befintliga företags och resurser visas:

    ![Välj din Azure AD DS-hanterade domän i Active Directory Administrationscenter](./media/create-ou/create-ou-adac-overview.png)

1. Fönstret **Aktiviteter** visas till höger i Administrationscenter för Active Directory. Välj **Ny > organisationsenhet**under domänen, till exempel *aaddscontoso.com.*

    ![Välj alternativet för att skapa en ny organisationsenhet i Active Directory Administrationscenter](./media/create-ou/create-ou-adac-new-ou.png)

1. I dialogrutan **Skapa organisationsenhet** anger du ett **namn** för den nya organisationsenheten, till exempel *MyCustomOu*. Ange en kort beskrivning av organisationsenheten, till exempel *Anpassad organisationsenhet för tjänstkonton*. Om du vill kan du också ange fältet **Hanterad av** för organisationsenheten. Om du vill skapa den anpassade organisationsenheten väljer du **OK**.

    ![Skapa en anpassad organisationsenhet från Administrationscenter för Active Directory](./media/create-ou/create-ou-dialog.png)

1. Tillbaka i Active Directory Administrationscenter visas nu den anpassade organisationsenheten och är tillgänglig för användning:

    ![Anpassad organisationsenhet som är tillgänglig för användning i Administrationscenter för Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder administrationsverktygen eller skapa och använda tjänstkonton finns i följande artiklar:

* [Administrationscenter för Active Directory: Komma igång](https://technet.microsoft.com/library/dd560651.aspx)
* [Stegvisa instruktioner för tjänstkonton](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
