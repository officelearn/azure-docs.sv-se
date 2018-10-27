---
title: 'Azure Active Directory Domain Services: Aktivera kerberos-begränsad delegering | Microsoft Docs'
description: Aktivera kerberos-begränsad delegering i Azure Active Directory Domain Services-hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: da7909853d47f680726464d6d646ca87028fd311
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158790"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurera Kerberos-begränsad delegering (KCD) på en hanterad domän
Många program behöver åtkomst till resurser i kontexten för användaren. Active Directory stöder en mekanism som heter Kerberos-delegering, vilket gör att det här användningsfallet. Dessutom kan du begränsa delegering så att endast specifika resurser kan användas i kontexten för användaren. Azure AD Domain Services-hanterade domäner skiljer sig från traditionella Active Directory-domäner, eftersom de säkrare är låsta.

Den här artikeln visar hur du konfigurerar Kerberos-begränsad delegering på en Azure AD Domain Services-hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos-begränsad delegering (KCD)
Kerberos-delegering kan ett konto för att personifiera en annan säkerhetsobjekt (till exempel en användare) att komma åt resurser. Överväg att ett webbprogram som har åtkomst till en backend-webb API i kontexten för en användare. I det här exemplet personifierar webbprogrammet (som körs i kontexten för ett tjänstkonto eller ett dator-/ datorkonto) användaren vid åtkomst till resursen (backend-webb-API). Kerberos-delegering är osäkert eftersom det inte begränsar de resurser som kontot imiterande kan komma åt i kontexten för användaren.

Kerberos-begränsad delegering (KCD) begränsar de tjänster/resurser som den angivna servern kan fungera på uppdrag av en användare. Traditionella KCD kräver domänadministratörsbehörighet att konfigurera ett domänkonto för en tjänst och det begränsar kontot till en enda domän.

Traditionella KCD har även några problem som är kopplade till den. I tidigare operativsystem, om domänadministratören konfigurerade baserade KCD för tjänsten, hade tjänsteadministratören inget användbart sätt att veta vilka frontend-tjänster delegerat till resurstjänsterna de ägde. Och alla frontend-tjänster som kunde delegera till en resurstjänst representerar en potentiell angreppspunkt. Om en server som värd för en frontend-tjänst har komprometterats och konfigurerades för att delegera till resurstjänster, kunde resurstjänsterna också komprometteras.

> [!NOTE]
> På en hanterad domän i Azure AD Domain Services har inte administratörsbehörighet för domänen. Därför **traditionella baserade KCD kan inte konfigureras på en hanterad domän**. Använd Resursbaserad KCD enligt beskrivningen i den här artikeln. Den här mekanismen är också säkrare.
>
>

## <a name="resource-based-kcd"></a>Resursbaserad KCD
Från och med Windows Server 2012 får administratörer möjligheten att konfigurera begränsad delegering för sina tjänster. I den här modellen kan backend-tjänst-administratören tillåta eller neka specifika frontend-tjänster från att använda KCD. Den här modellen kallas **Resursbaserad KCD**.

Resursbaserad KCD har konfigurerats med hjälp av PowerShell. Du använder den `Set-ADComputer` eller `Set-ADUser` cmdlet: ar, beroende på om imiterande kontot är ett datorkonto eller ett användarkonto för tjänsten och kontot.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurera Resursbaserad KCD för ett datorkonto i en hanterad domän
Anta att du har en webbapp som körs på datorn ”contoso100-webapp.contoso100.com”. Den behöver åtkomst till resursen (ett webb-API som körs på ”contoso100-api.contoso100.com”) i samband med domänanvändare. Här är hur du skapar Resursbaserad KCD för det här scenariot:

1. [Skapa en anpassad OU](active-directory-ds-admin-guide-create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade Organisationsenheten för användare i den hanterade domänen.
2. Anslut båda de virtuella datorerna (det som kör webbappen och den som körs webb-API) till den hanterade domänen. Skapa dessa konton i den anpassa Organisationsenheten.
3. Nu kan konfigurera Resursbaserad KCD med hjälp av följande PowerShell-kommando:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Datorkontona för webbappen och webb-API måste vara i en anpassad Organisationsenhet där du har behörighet att konfigurera Resursbaserad KCD. Du kan inte konfigurera Resursbaserad KCD för ett datorkonto i den inbyggda ”AAD DC datorer”-behållaren.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurera Resursbaserad KCD för ett användarkonto på en hanterad domän
Anta att du har en webbapp som körs som ett tjänstkonto 'appsvc' och den behöver åtkomst till resursen (ett webb-API som körs som ett tjänstkonto - 'backendsvc ”) i samband med domänanvändare. Här är hur du skapar Resursbaserad KCD för det här scenariot.

1. [Skapa en anpassad OU](active-directory-ds-admin-guide-create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade Organisationsenheten för användare i den hanterade domänen.
2. Ansluta till den virtuella datorn körs serverdels-webb-API-resursen till den hanterade domänen. Skapa datorkontot i den anpassa Organisationsenheten.
3. Skapa kontot (till exempel appsvc) används för att köra webbappen i den anpassa Organisationsenheten.
4. Nu kan konfigurera Resursbaserad KCD med hjälp av följande PowerShell-kommando:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Både datorkontot för backend-webb-API och tjänsten konto måste vara i en anpassad Organisationsenhet där du har behörighet att konfigurera Resursbaserad KCD. Du kan inte konfigurera Resursbaserad KCD för ett datorkonto i den inbyggda ”AAD DC datorer”-behållaren eller för användarkonton i den inbyggda ”AAD DC-användare”-behållaren. Du kan inte därför använda användarkonton som synkroniseras från Azure AD för att ställa in Resursbaserad KCD.
>

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services – komma igång-guiden](active-directory-ds-getting-started.md)
* [Kerberos-begränsad delegering översikt](https://technet.microsoft.com/library/jj553400.aspx)
