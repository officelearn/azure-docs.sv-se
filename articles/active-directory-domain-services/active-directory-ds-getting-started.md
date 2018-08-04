---
title: 'Azure Active Directory Domain Services: Komma igång | Microsoft Docs'
description: Aktivera Azure Active Directory Domain Services med Azure portal
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: b6651c038a2b3abd15b8b0587e6a0e95832401b1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502319"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure portal
Den här artikeln visar hur du aktiverar Azure Active Directory Domain Services (Azure AD DS) med Azure portal.


## <a name="before-you-begin"></a>Innan du börjar
Du behöver följande för att slutföra de uppgifter som anges i den här artikeln:

* En giltig **Azure-prenumeration**.
* En **Azure AD-katalog** -antingen synkroniseras med en lokal katalog eller en molnbaserad katalog.
* Den **Azure-prenumeration måste vara associerad med Azure AD-katalogen**.
* Du behöver **global administratör** behörigheter i din Azure AD-katalog för att aktivera Azure AD Domain Services.


## <a name="enable-azure-ad-domain-services"></a>Aktivera Azure AD Domain Services

Att starta den **aktivera Azure AD Domain Services** guiden gör du följande:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. I den vänstra rutan klickar du på **Skapa en resurs**.
3. I den **New** anger **Domain Services** i sökfältet.

    ![Sök efter domain services](./media/getting-started/search-domain-services.png)

4. Klickar du på **Azure AD Domain Services** från listan över sökförslag. På den **Azure AD Domain Services** klickar du på den **skapa** knappen.

    ![Domain services-vy](./media/getting-started/domain-services-blade.png)

5. Den **aktivera Azure AD Domain Services** starta guiden.


## <a name="task-1-configure-basic-settings"></a>Uppgift 1: Konfigurera grundläggande inställningar
I den **grunderna** sidan i guiden anger du DNS-domännamnet för den hanterade domänen. Du kan också välja resursgruppen och Azure-plats som den hanterade domänen ska distribueras.

![Konfigurera grundläggande inställningar](./media/getting-started/domain-services-blade-basics.png)

1. Välj den **DNS-domännamn** för din hanterade domän.

   > [!NOTE]
   > **Riktlinjer för att välja ett DNS-domännamn**
   > * **Inbyggda domännamn:** som standard i guiden anger standard/inbyggda-i domännamnet för katalogen (med en **. onmicrosoft.com** suffix) åt dig. Om du väljer att aktivera åtkomst med säkert LDAP till den hanterade domänen via internet, räkna med problem med att skapa en offentlig DNS-post eller skaffa en säker LDAP-certifikat från en offentlig Certifikatutfärdare för det här domännamnet. Microsoft äger den *. onmicrosoft.com* domän och certifikatutfärdare utfärdar inte certifikat som ansvarar för den här domänen.
   * **Anpassade domännamn:** du kan också skriva i ett anpassat domännamn. I det här exemplet är det anpassade domännamnet *contoso100.com*.
   * **Icke-dirigerbara domänsuffix:** i allmänhet rekommenderar vi att undvika ett icke-dirigerbara domän namnsuffix. Exempelvis är det bättre att undvika att skapa en domän med DNS-domän namn ”contoso.local”. DNS-suffixet ”Local” är inte dirigerbart och kan orsaka problem med DNS-matchning.
   * **Domänbegränsningar för prefix:** prefixet för det angivna domännamnet (till exempel *contoso100* i den *contoso100.com* domännamn) får innehålla högst 15 tecken. Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken.
   * **Nätverks-namnet står i konflikt:** Kontrollera att DNS-domännamnet som du har valt för den hanterade domänen inte redan finns i det virtuella nätverket. Kontrollera särskilt om:
       * Du har redan en Active Directory-domän med samma DNS-domännamnet i det virtuella nätverket.
       * Det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning med det lokala nätverket. I det här scenariot, se till att du inte har en domän med samma DNS-domännamnet i ditt lokala nätverk.
       * Du har en befintlig molntjänst med det namnet i det virtuella nätverket.
    >

2. Välj Azure **prenumeration** i som du vill skapa den hanterade domänen.

3. Välj den **resursgrupp** till den hanterade domänen ska tillhöra. Välj antingen den **Skapa nytt** eller **Använd befintlig** alternativ att välja resursgruppen.

4. Välj Azure **plats** i som den hanterade domänen ska skapas. På den **nätverk** sidan i guiden visas bara virtuella nätverk som hör till den plats som du har valt.

5. Klicka på **OK** att gå vidare till den **nätverk** i guiden.


## <a name="next-step"></a>Nästa steg
[Uppgift 2: Konfigurera nätverksinställningar](active-directory-ds-getting-started-network.md)
