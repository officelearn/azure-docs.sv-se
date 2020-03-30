---
title: Aktivera SharePoint-tjänst för användarprofil med Azure AD DS | Microsoft-dokument
description: Lär dig hur du konfigurerar en hanterad Azure Active Directory Domain Services-domän för att stödja profilsynkronisering för SharePoint Server
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 9d983015927d2635f69a327a9c5b168056542519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613864"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>Konfigurera Azure Active Directory Domain Services så att de stöder synkronisering av användarprofiler för SharePoint Server

SharePoint Server innehåller en tjänst för att synkronisera användarprofiler. Med den här funktionen kan användarprofiler lagras på en central plats och vara tillgängliga på flera SharePoint-webbplatser och -grupper. Om du vill konfigurera tjänsten SharePoint Server-användarprofil måste lämpliga behörigheter beviljas i en Azure Active Directory Domain Services (Azure AD DS) hanterad domän. Mer information finns [i synkronisering av användarprofiler i SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx).

I den här artikeln beskrivs hur du konfigurerar Azure AD DS så att synkroniseringstjänsten för SharePoint Server-användarprofilen tillåts.

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
* Ett SharePoint-tjänstkonto för synkroniseringstjänsten för användarprofiler.
    * Om det behövs läser du [Planera för administrativa konton och tjänstkonton i SharePoint Server][sharepoint-service-account].

## <a name="service-accounts-overview"></a>Översikt över tjänstkonton

I en Azure AD DS-hanterad domän finns en säkerhetsgrupp med namnet **AAD DC Service Accounts** som en del av *organisationsenheten för användare* (OU). Medlemmar i den här säkerhetsgruppen delegeras följande behörigheter:

- **Behörigheten Replikera katalogändringar** på roten DSE.
- **Behörigheten Replikera katalogändringar** i *konfigurationsnamnningskontexten* (behållare).`cn=configuration`

Säkerhetsgruppen **AAD DC-tjänstkonton** är också medlem i den inbyggda gruppen **Pre-Windows 2000 Compatible Access**.

När tjänsten för synkroniseringstjänsten för SharePoint Server-användarprofil läggs till i den här säkerhetsgruppen beviljas tjänsten för synkroniseringstjänsten för SharePoint Server de privilegier som krävs för att fungera korrekt.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>Aktivera stöd för synkronisering av användarprofiler för SharePoint Server

Tjänstkontot för SharePoint Server behöver tillräcklig behörighet för att replikera ändringar i katalogen och låta SharePoint Server-användarprofilsynkronisering fungera korrekt. Om du vill ange dessa privilegier lägger du till det tjänstkonto som används för Synkronisering av SharePoint-användarprofil i gruppen **AAD DC Service Accounts.**

Gör så här från den virtuella virtuella datorn för Azure AD DS-hantering:

> [!NOTE]
> Om du vill redigera gruppmedlemskap i en Azure AD DS-hanterad domän måste du vara inloggad på ett användarkonto som är medlem i gruppen *AAD DC-administratörer.*

1. Välj **Administrationsverktyg**på Startskärmen . En lista över tillgängliga hanteringsverktyg visas som installerades i självstudien för att [skapa en hantering VM][tutorial-create-management-vm].
1. Om du vill hantera gruppmedlemskap väljer du **Active Directory Administrationscenter** i listan över administrativa verktyg.
1. I den vänstra rutan väljer du din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*. En lista över befintliga företags och resurser visas.
1. Välj **organisationsenheten Användare** och välj sedan säkerhetsgruppen *AAD DC Service Accounts.*
1. Välj **Medlemmar**och välj sedan **Lägg till...**.
1. Ange namnet på SharePoint-tjänstkontot och välj sedan **OK**. I följande exempel heter SharePoint-tjänstkontot *spadmin:*

    ![Lägga till SharePoint-tjänstkontot i säkerhetsgruppen AAD DC Service Accounts](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Bevilja Active Directory Domain Services-behörigheter för profilsynkronisering i SharePoint Server](https://technet.microsoft.com/library/hh296982.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts
