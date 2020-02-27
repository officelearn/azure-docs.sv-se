---
title: Ta bort Azure Active Directory Domain Services | Microsoft Docs
description: Lär dig hur du inaktiverar eller tar bort en Azure Active Directory Domain Services hanterad domän med hjälp av Azure Portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614032"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Ta bort en Azure Active Directory Domain Services hanterad domän med hjälp av Azure Portal

Om du inte längre behöver en hanterad domän kan du ta bort en Azure Active Directory Domain Services (Azure AD DS)-instans. Det finns inget alternativ för att stänga av eller tillfälligt inaktivera en hanterad Azure AD DS-domän. Det går inte att ta bort den hanterade Azure AD DS-domänen eller på annat sätt påverka Azure AD-klienten. Den här artikeln visar hur du använder Azure Portal för att ta bort en Azure AD DS-hanterad domän.

> [!WARNING]
> **Borttagning är permanent och kan inte ångras.**
> När du tar bort en Azure AD DS-hanterad domän inträffar följande steg:
>   * Domänkontrollanter för den hanterade domänen är avetablerade och tas bort från det virtuella nätverket.
>   * Data på den hanterade domänen tas bort permanent. Dessa data omfattar anpassade organisationsenheter, grup princip objekt, anpassade DNS-poster, tjänstens huvud namn, GMSAs osv. som du har skapat.
>   * Datorer som är anslutna till den hanterade domänen förlorar sin förtroende relation med domänen och måste vara frånkopplad från domänen.
>       * Du kan inte logga in på de här datorerna med Corporate AD-autentiseringsuppgifter. I stället måste du använda autentiseringsuppgifterna för lokal administratör för datorn.

## <a name="delete-the-managed-domain"></a>Ta bort den hanterade domänen

Utför följande steg för att ta bort en Azure AD DS-hanterad domän:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**.
1. Välj namnet på din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **Ta bort** på sidan **Översikt**. Bekräfta borttagningen genom att skriva domän namnet för den hanterade domänen igen och sedan välja **ta bort**.

Det kan ta 15-20 minuter eller mer att ta bort den hanterade Azure AD DS-domänen.

## <a name="next-steps"></a>Nästa steg

Överväg att [dela feedback][feedback] för de funktioner som du vill se i Azure AD DS.

Om du vill komma igång med Azure AD DS igen, se [skapa och konfigurera en Azure Active Directory Domain Services instans][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
