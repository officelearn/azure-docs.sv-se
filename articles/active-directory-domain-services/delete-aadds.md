---
title: Ta bort Azure Active Directory Domain Services | Microsoft-dokument
description: Lär dig hur du inaktiverar eller tar bort en hanterad Azure Active Directory Domain Services-domän med Azure-portalen
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655470"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Ta bort en hanterad Azure Active Directory Domain Services-domän med Azure-portalen

Om du inte längre behöver en hanterad domän kan du ta bort en Azure Active Directory Domain Services -instans (Azure AD DS). Det finns inget alternativ för att stänga av eller tillfälligt inaktivera en Azure AD DS-hanterad domän. Om du tar bort den Hanterade Azure AD DS-domänen tas inte azure AD-klienten bort eller på annat sätt negativt. Den här artikeln visar hur du använder Azure-portalen för att ta bort en Azure AD DS-hanterad domän.

> [!WARNING]
> **Borttagningen är permanent och kan inte återföras.**
> När du tar bort en Azure AD DS-hanterad domän inträffar följande:
>   * Domänkontrollanter för den hanterade domänen avetablerars och tas bort från det virtuella nätverket.
>   * Data på den hanterade domänen tas bort permanent. Dessa data omfattar anpassade företagsenheter, gpu:er, anpassade DNS-poster, tjänstobjekt, GMSAs, etc. som du har skapat.
>   * Datorer som är anslutna till den hanterade domänen förlorar sin förtroenderelation med domänen och måste tas bort från domänen.
>       * Du kan inte logga in på dessa datorer med företagets AD-autentiseringsuppgifter. I stället måste du använda datorns lokala administratörsbehörighet.

## <a name="delete-the-managed-domain"></a>Ta bort den hanterade domänen

Så här tar du bort en Hanterad Azure AD DS-domän:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen .
1. Välj namnet på din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **Ta bort** på sidan **Översikt**. Om du vill bekräfta borttagningen skriver du domännamnet för den hanterade domänen igen och väljer sedan **Ta bort**.

Det kan ta 15-20 minuter eller mer att ta bort azure AD DS-hanterad domän.

## <a name="next-steps"></a>Nästa steg

Överväg att [dela feedback][feedback] för de funktioner som du vill se i Azure AD DS.

Om du vill komma igång med Azure AD DS igen läser du [Skapa och konfigurera en Azure Active Directory Domain Services-instans][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
