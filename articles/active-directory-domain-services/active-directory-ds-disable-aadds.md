---
title: Inaktivera Azure Active Directory Domain Services | Microsoft Docs
description: Inaktivera Azure Active Directory Domain Services med Azure-portalen
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: 32db916b319c531816a935fcfe3bc4fe82997fdd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Inaktivera Azure Active Directory Domain Services med Azure-portalen
Den här artikeln visar hur du använder Azure-portalen för att inaktivera Azure Active Directory (AD) Domain Services för din Azure AD-katalog.

> [!WARNING]
> **Borttagning är permanent och kan inte ångras.**
> Fortsätt med varning! När du tar bort den hanterade domänen:
  * Domänkontrollanter för den hanterade domänen tas bort och tas bort från det virtuella nätverket.
  * Data på den hanterade domänen tas bort permanent. Detta inkluderar anpassade organisationsenheter, grupprincipobjekt, anpassade DNS-poster, tjänstens huvudnamn, GMSAs etc. som du har skapat på den hanterade domänen.
  * Datorer som är anslutna till den hanterade domänen förlora sina förtroenderelation med domänen och behöver inte frånkopplas från domänen.
  * Du kan inte logga in på dessa datorer med AD företagsuppgifter. Använd autentiseringsuppgifter för lokal administratör för datorn i stället.
Ta bort den hanterade domänen inte ta bort Azure AD-katalogen eller annars negativt påverka katalogen.
>

Utför följande steg för att ta bort din Azure AD Domain Services-hanterad domän:
1. Navigera till den [Azure AD Domain Services-tillägget](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure-portalen.
2. Klicka på namnet på din hanterade domän.

    ![Välj domän att ta bort](./media/getting-started/domain-services-delete-select-domain.png)

3. På den **översikt** klickar du på den **ta bort** knappen.

    ![Ta bort domänen](./media/getting-started/domain-services-delete-domain.png)

4. Ange DNS-domännamnet för den hanterade domänen för att bekräfta borttagningen. Klicka på den **ta bort** knappen när du är klar.

    ![Ta bort domänen bekräftelse](./media/getting-started/domain-services-delete-domain-confirm.png)

Den hanterade domänen tas bort i ungefär 15-20 minuter.

Överväg att [delning feedback](active-directory-ds-contact-us.md) som hjälper oss att förstå vad funktioner hjälper du har valt Azure AD Domain Services i framtiden. Denna feedback hjälper oss att utveckla tjänsten så att den passar dina behov och användningsfall.
