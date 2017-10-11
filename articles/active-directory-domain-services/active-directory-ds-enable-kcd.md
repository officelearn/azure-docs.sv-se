---
title: "Azure Active Directory Domain Services: Aktivera kerberos-begränsad delegering | Microsoft Docs"
description: "Aktivera kerberos-begränsad delegering på Azure Active Directory Domain Services hanterade domäner"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: f36f16a7bb00ace9fd5164eb38ba77f015f22f5c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurera kerberos-begränsad delegering (KCD) på en hanterad domän
Många program behöver åtkomst till resurser i kontexten för användaren. Active Directory stöder en mekanism som kallas Kerberos-delegering, vilket gör den här användningsfall. Dessutom kan du begränsa delegering så att bara vissa resurser kan användas i kontexten för användaren. Azure AD Domain Services-hanterade domäner skiljer sig från traditionella Active Directory-domäner eftersom de säkrare är låsta.

Den här artikeln visar hur du konfigurerar kerberos-begränsad delegering på en hanterad Azure AD DS-domän.

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos-begränsad delegering (KCD)
Kerberos-delegering kan ett konto att personifiera en annan säkerhetsobjekt (till exempel en användare) för att komma åt resurser. Överväg att ett webbprogram som har åtkomst till en backend-webb-API i kontexten för en användare. I det här exemplet personifierar webbprogrammet (som körs i kontexten för ett tjänstkonto eller ett dator-/ datorkonto) användaren få åtkomst till resursen (backend-webb-API). Kerberos-delegering är osäker eftersom den inte begränsar resurser imiterande kontot kan komma åt i kontexten för användaren.

Kerberos-begränsad delegering (KCD) begränsar de resurser eller det tjänster som den angivna servern kan fungera på uppdrag av en användare. Traditionell KCD kräver domänadministratörsbehörighet att konfigurera ett domänkonto för en tjänst och det begränsar kontot till en enda domän.

Traditionell KCD har även några problem som är kopplade till den. I tidigare operativsystem där domänadministratören konfigurerade tjänsten, hade tjänsteadministratören inget användbart sätt att veta vilka frontend-tjänster delegerat till resurstjänsterna de ägde. Och alla frontend-tjänster som kunde delegera till en resurstjänst representerade en potentiell angreppspunkt. Om en server som är värd för en frontend-tjänst blev komprometterad och konfigurerades för att delegera till resurstjänster, kunde resurstjänsterna också komprometteras.

> [!NOTE]
> På en hanterad domän i Azure AD Domain Services har inte administratörsbehörighet för domänen. Därför **traditionella KCD kan inte konfigureras på en hanterad domän**. Använd resursbaserade KCD som beskrivs i den här artikeln. Den här mekanismen är också säkrare.
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>Resursbaserade kerberos-begränsad delegering
I Windows Server 2012 R2 och Windows Server 2012 har möjlighet att konfigurera begränsad delegering för tjänsten överförts från domänadministratören till tjänsteadministratören. På så sätt kan kan administratören för backend-tjänst tillåta eller neka frontend-tjänster. Den här modellen kallas **resursbaserade kerberos-begränsad delegering**.

Resursbaserad KCD har konfigurerats med hjälp av PowerShell. Du kan använda Set-ADComputer eller Set-ADUser cmdlets, beroende på om imiterande kontot är ett datorkonto eller ett användarkonto för konto-tjänsten.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurera resursbaserade KCD för ett datorkonto på en hanterad domän
Anta att du har ett webbprogram som körs på datorn ”contoso100-webapp.contoso100.com'. Behöver åtkomst till resursen (en webb-API som körs på ”contoso100-api.contoso100.com”) i samband med domänanvändare. Här är hur du konfigurerar resursbaserade KCD för det här scenariot.

```
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurera resursbaserade KCD för ett användarkonto på en hanterad domän
Anta att du har ett webbprogram som körs som ett tjänstkonto 'appsvc' och behöver åtkomst till resursen (ett web API som körs som ett tjänstkonto - 'backendsvc') i samband med domänanvändare. Här är hur du konfigurerar resursbaserade KCD för det här scenariot.

```
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services - komma igång-guide](active-directory-ds-getting-started.md)
* [Kerberos-begränsad delegering: översikt](https://technet.microsoft.com/library/jj553400.aspx)
