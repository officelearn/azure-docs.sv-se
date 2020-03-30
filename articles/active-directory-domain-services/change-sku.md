---
title: Ändra SKU för en Azure AD-domäntjänster | Microsoft-dokument
description: Lär dig hur du använder SKU-nivån för en hanterad Azure AD Domain Services-domän om dina affärskrav ändras
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: b65310569e95173b88dd0aa0dfe1dbacd86cc8fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126700"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Ändra SKU för en befintlig Azure AD Domain Services-hanterad domän

I Azure Active Directory Domain Services (Azure AD DS) baseras de tillgängliga prestanda och funktioner på SKU-typen. Dessa funktionsskillnader inkluderar säkerhetskopieringsfrekvensen eller maximalt antal envägs utgående skogsförtroende (för närvarande i förhandsversion). Du väljer en SKU när du skapar den hanterade domänen och du kan växla SKU:er uppåt eller nedåt när ditt företag behöver ändras när den hanterade domänen har distribuerats. Ändringar i affärskraven kan omfatta behovet av mer frekventa säkerhetskopieringar eller för att skapa ytterligare skogsförtroende. Mer information om begränsningar och priser för de olika SKU:erna finns i [Azure AD DS SKU-koncept][concepts-sku] och [Azure AD DS-prissidor.][pricing]

Den här artikeln visar hur du ändrar SKU för en befintlig Azure AD DS-hanterad domän med Azure-portalen.

## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs slutför du självstudien för att [skapa och konfigurera en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Begränsningar för SKU-ändring

Du kan ändra SKU:er upp eller ned när den Azure AD DS-hanterade domänen har distribuerats. Men om du använder en resursskog (för närvarande i förhandsversion) och har skapat envägs utgående skogsförtroende från Azure AD DS till en lokal AD DS-miljö, finns det vissa begränsningar för SKU-ändringsåtgärden. *Premium-* och Enterprise-SKU:erna definierar en gräns för hur många förtroenden du kan skapa. *Enterprise* Du kan inte ändra till en SKU med en lägre maxgräns än vad du för närvarande har konfigurerat.

Ett exempel:

* Om du har skapat två skogsförtroende på *Premium* SKU kan du inte ändra till *Standard* SKU. *Standard-SKU* stöder inte skogsförtroende.
* Om du har skapat sju förtroenden på *Premium* SKU kan du inte ändra till *Enterprise* SKU. *Enterprise* SKU stöder högst fem förtroenden.

Mer information om dessa begränsningar finns i [Azure AD DS SKU-funktioner och begränsningar][concepts-sku].

## <a name="select-a-new-sku"></a>Välj en ny SKU

Så här ändrar du SKU för en Azure AD DS-hanterad domän med Azure-portalen:

1. Sök efter och välj Azure AD Domain Services högst upp i **Azure-portalen**. Välj den hanterade domänen i listan, till exempel *aaddscontoso.com*.
1. Välj **Inställningar > SKU till**vänster på sidan Azure AD DS .

    ![Välj SKU-menyalternativet för din Azure AD DS-hanterade domän i Azure-portalen](media/change-sku/overview-change-sku.png)

1. Välj den SKU som du vill ha för din Azure AD DS-hanterade domän på den nedrullningsbara menyn. Om du har en resursskog kan du inte välja *Standard* SKU eftersom skogsförtroende bara är tillgängliga på *Enterprise* SKU eller högre.

    Välj den SKU du vill använda på den nedrullningsbara menyn och välj sedan **Spara**.

    ![Välj den SKU som krävs på den nedrullningsliga menyn i Azure-portalen](media/change-sku/change-sku-selection.png)

Det kan ta en minut eller två att ändra SKU-typen.

## <a name="next-steps"></a>Nästa steg

Om du har en resursskog och vill skapa ytterligare förtroenden efter SKU-ändringen läser du [Skapa ett utgående skogsförtroende till en lokal domän i Azure AD DS (förhandsversion).][create-trust]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
