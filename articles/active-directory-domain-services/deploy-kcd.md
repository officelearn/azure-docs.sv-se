---
title: 'Azure Active Directory Domain Services: Aktivera Kerberos-begränsad delegering | Microsoft Docs'
description: Aktivera Kerberos-begränsad delegering på Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612940"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurera Kerberos-begränsad delegering (KCD) på en hanterad domän
Många program behöver ha åtkomst till resurser i användarens kontext. Active Directory stöder en mekanism som kallas Kerberos-delegering, vilket möjliggör det här användnings fallet. Ytterligare kan du begränsa delegeringen så att endast vissa resurser kan nås i användarens kontext. Azure AD Domain Services hanterade domäner skiljer sig från traditionella Active Directory domäner eftersom de är säkrare att låsa upp.

Den här artikeln visar hur du konfigurerar Kerberos-begränsad delegering på en Azure AD Domain Services hanterad domän.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos-begränsad delegering (KCD)
Med Kerberos-delegering kan ett konto personifiera ett annat säkerhets objekt (till exempel en användare) för att få åtkomst till resurser. Överväg ett webb program som har åtkomst till ett Server dels webb-API i kontexten för en användare. I det här exemplet personifierar webb programmet (som körs i kontexten för ett tjänst konto eller ett dator-/dator konto) användaren vid åtkomst till resursen (Server dels webb-API). Kerberos-delegering är insäker eftersom den inte begränsar de resurser som kontot personifiera kan komma åt i användarens kontext.

Kerberos-begränsad delegering (KCD) begränsar de tjänster/resurser som den angivna servern kan agera för på uppdrag av en användare. Traditionella KCD kräver domän administratörs behörighet för att konfigurera ett domän konto för en tjänst och det begränsar kontot till en enda domän.

Traditionella KCD har också några problem kopplade till sig. Om domän administratören har konfigurerat kontobaserade KCD för tjänsten i tidigare operativ system, hade tjänst administratören inte något användbart sätt att veta vilka frontend-tjänster som delegerats till de resurs tjänster som de äger. Alla frontend-tjänster som kan delegera till en resurs tjänst motsvarade en potentiell angrepps punkt. Om en server som är värd för en frontend-tjänst har komprometterats och den kon figurer ATS att delegera till resurs tjänster, kan resurs tjänsterna också komprometteras.

> [!NOTE]
> I en Azure AD Domain Services hanterad domän har du inte domän administratörs behörighet. Därför **kan inte traditionella kontobaserade KCD konfigureras på en hanterad domän**. Använd Resource based KCD enligt beskrivningen i den här artikeln. Den här mekanismen är också säkrare.
>
>

## <a name="resource-based-kcd"></a>Resursbaserade KCD
Från och med Windows Server 2012 får tjänst administratörer möjlighet att konfigurera begränsad delegering för tjänsten. I den här modellen kan Server administratören för Server delen tillåta eller neka särskilda klient dels tjänster från att använda KCD. Den här modellen kallas för **resursbaserade KCD**.

Resource-baserad KCD har kon figurer ATS med PowerShell. Du använder `Set-ADComputer` cmdletarna `Set-ADUser` eller, beroende på om det Personifierande kontot är ett dator konto eller ett användar konto/tjänst konto.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurera resursbaserade KCD för ett dator konto i en hanterad domän
Anta att du har en webbapp som körs på datorn ' contoso-webapp.contoso.com '. Den behöver åtkomst till resursen (ett webb-API som körs på ' contoso-api.contoso.com ') i kontexten för domän användare. Så här konfigurerar du resursbaserade KCD för det här scenariot:

1. [Skapa en anpassad Organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade ORGANISATIONSENHETen till användare i den hanterade domänen.
2. Anslut båda virtuella datorerna (den som kör webbappen och den som kör webb-API: et) till den hanterade domänen. Skapa dessa dator konton inom den anpassade ORGANISATIONSENHETen.
3. Konfigurera sedan resursbaserade KCD med följande PowerShell-kommando:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Dator kontona för webbappen och webb-API: et måste finnas i en anpassad ORGANISATIONSENHET där du har behörighet att konfigurera resursbaserade KCD. Det går inte att konfigurera resursbaserade KCD för ett dator konto i den inbyggda behållaren AAD DC-datorer.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurera resursbaserade KCD för ett användar konto på en hanterad domän
Anta att du har en webbapp som körs som ett tjänst konto ' appsvc ' och att den behöver åtkomst till resursen (ett webb-API som körs som ett tjänst konto-' backendsvc ') i kontexten för domän användare. Så här konfigurerar du Resource based KCD för det här scenariot.

1. [Skapa en anpassad Organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade ORGANISATIONSENHETen till användare i den hanterade domänen.
2. Anslut den virtuella datorn som kör Server dels webb-API: n/resursen till den hanterade domänen. Skapa sitt dator konto inom den anpassade ORGANISATIONSENHETen.
3. Skapa tjänst kontot (till exempel ' appsvc ') som används för att köra webbappen i den anpassade ORGANISATIONSENHETen.
4. Konfigurera sedan resursbaserade KCD med följande PowerShell-kommando:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Både dator kontot för webb-API för Server delen och tjänst kontot måste finnas i en anpassad ORGANISATIONSENHET där du har behörighet att konfigurera resursbaserade KCD. Du kan inte konfigurera resursbaserade KCD för ett dator konto i den inbyggda behållaren "AAD DC-datorer" eller för användar konton i den inbyggda behållaren "AAD DC Users". Det innebär att du inte kan använda användar konton som synkroniserats från Azure AD för att konfigurera resursbaserade KCD.
>

## <a name="related-content"></a>Relaterat innehåll
* [Azure AD Domain Services-Komma igång guide](tutorial-create-instance.md)
* [Översikt över Kerberos-begränsad delegering](https://technet.microsoft.com/library/jj553400.aspx)
