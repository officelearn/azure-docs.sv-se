---
title: "Azure Active Directory Domain Services: Komma igång | Microsoft Docs"
description: Aktivera Azure Active Directory Domain Services med Azure-portalen
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 97803d62ee42d777336dc87c34a16eff426d24d0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivera Azure Active Directory Domain Services med Azure-portalen
Den här artikeln visar hur du aktiverar Azure Active Directory Domain Services (Azure AD DS) med Azure-portalen.

Att starta den **aktivera Azure AD Domain Services** guiden gör du följande:

1. Gå till [Azure-portalen](https://portal.azure.com).
2. I den vänstra rutan klickar du på **skapar du en resurs**.
3. I den **ny** anger **Domain Services** i sökfältet.

    ![Sök efter domäntjänster](./media/getting-started/search-domain-services.png)

4. Välj **Azure AD Domain Services** från listan över sökförslag. På den **Azure AD Domain Services** klickar du på den **skapa** knappen.

    ![Domain services vy](./media/getting-started/domain-services-blade.png)

5. Den **aktivera Azure AD Domain Services** starta guiden.


## <a name="task-1-configure-basic-settings"></a>Uppgift 1: Konfigurera grundläggande inställningar
I den **grunderna** sidan i guiden kan du ange DNS-domännamnet för den hanterade domänen. Du kan också välja resursgruppen och Azure-plats som den hanterade domänen ska distribueras.

![Konfigurera grunderna](./media/getting-started/domain-services-blade-basics.png)

1. Välj den **DNS-domännamn** för din hanterade domän.

   > [!NOTE]
   > **Riktlinjer för att välja ett DNS-domännamn**
   > * **Inbyggda domännamn:** som standard i guiden anger standard/inbyggda-i domännamnet för katalogen (med en **. onmicrosoft.com** suffix) för dig. Om du väljer att aktivera säker LDAP-åtkomst till den hanterade domänen via internet, räkna med problem med att skapa en offentlig DNS-post eller hämta ett säkert LDAP-certifikat från en offentlig Certifikatutfärdare för det här domännamnet. Microsoft äger den *. onmicrosoft.com* domän och certifikatutfärdare utfärdar inte certifikat som ansvarar för den här domänen.
   * **Anpassade domännamn:** du kan också skriva i ett anpassat domännamn. I det här exemplet är det anpassade domännamnet *contoso100.com*.
   * **Icke-dirigerbara domänsuffix:** vanligtvis rekommenderar vi att undvika en icke-dirigerbara domänens namnsuffix. Exempelvis är det bättre att undvika att skapa en domän med DNS-domänen namnet 'contoso.local'. DNS-suffixet ”Local' är inte dirigerbart och kan orsaka problem med DNS-matchning.
   * **Domänbegränsningar prefix:** prefixet för ett angivet domännamn (till exempel *contoso100* i den *contoso100.com* domännamn) får innehålla högst 15 tecken. Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken.
   * **Nätverket namnet står i konflikt:** Kontrollera att DNS-domännamnet som du har valt för den hanterade domänen inte redan finns i det virtuella nätverket. I synnerhet kontrollera om:
       * Du har redan en Active Directory-domän med samma DNS-domännamnet på det virtuella nätverket.
       * Det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning med ditt lokala nätverk. I det här scenariot, se till att du inte har en domän med samma DNS-domännamnet i ditt lokala nätverk.
       * Du har en befintlig molntjänst med det namnet i det virtuella nätverket.
    >

2. Välj Azure **prenumeration** i som du vill skapa den hanterade domänen.

3. Välj den **resursgruppen** till den hanterade domänen ska tillhöra. Du kan välja antingen den **Skapa nytt** eller **använda befintliga** alternativ för att välja en resursgrupp.

4. Välj Azure **plats** i som den hanterade domänen ska skapas. På den **nätverk** sidan i guiden visas bara virtuella nätverk som hör till den plats som du har valt.

5. När du är klar klickar du på **OK** att gå vidare till den **nätverk** sidan i guiden.


## <a name="next-step"></a>Nästa steg
[Uppgift 2: Konfigurera nätverksinställningar](active-directory-ds-getting-started-network.md)
