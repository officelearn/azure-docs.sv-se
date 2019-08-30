---
title: Inaktivera Azure Active Directory Domain Services | Microsoft Docs
description: Inaktivera Azure Active Directory Domain Services med hjälp av Azure Portal
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171975"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Inaktivera Azure Active Directory Domain Services med hjälp av Azure Portal
Den här artikeln visar hur du använder Azure Portal för att inaktivera Azure Active Directory (AD) domän tjänster för Azure AD-katalogen.

> [!WARNING]
> **Borttagning är permanent och kan inte ångras.**
> Fortsätt med varning! När du tar bort den hanterade domänen:
>   * Domänkontrollanter för den hanterade domänen är avetablerade och tas bort från det virtuella nätverket.
>   * Data på den hanterade domänen tas bort permanent. Detta inkluderar anpassade organisationsenheter, grup princip objekt, anpassade DNS-poster, tjänstens huvud namn, GMSAs osv. som du har skapat på den hanterade domänen.
>   * Datorer som är anslutna till den hanterade domänen förlorar sin förtroende relation med domänen och måste vara frånkopplad från domänen.
>   * Du kan inte logga in på de här datorerna med Corporate AD-autentiseringsuppgifter. Använd den lokala administratörens autentiseringsuppgifter för datorn i stället.
> Om du tar bort den hanterade domänen tas inte Azure AD-katalogen bort, eller så påverkas inte katalogen på annat sätt.

Utför följande steg för att ta bort din Azure AD Domain Services hanterade domän:
1. Navigera till [Azure AD Domain Services tillägget](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) i Azure Portal.
2. Klicka på namnet på den hanterade domänen.

    ![Välj domän att ta bort](./media/getting-started/domain-services-delete-select-domain.png)

3. På sidan **Översikt** klickar du på knappen **ta bort** .

    ![Ta bort domän](./media/getting-started/domain-services-delete-domain.png)

4. Bekräfta borttagningen genom att ange DNS-domännamnet för den hanterade domänen. Klicka på knappen **ta bort** när du är färdig.

    ![Ta bort domän bekräftelse](./media/getting-started/domain-services-delete-domain-confirm.png)

Det kan ta 15-20 minuter eller mer att ta bort den hanterade domänen.

Överväg att [dela feedback](contact-us.md) för att hjälpa oss att förstå vilka funktioner som kan hjälpa dig att välja Azure AD Domain Services i framtiden. Den här feedback hjälper oss att utveckla tjänsten så att den bättre passar dina distributions behov och användnings fall.
