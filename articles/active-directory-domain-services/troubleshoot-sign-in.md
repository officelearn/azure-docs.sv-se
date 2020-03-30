---
title: Felsöka inloggningsproblem i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du felsöker vanliga användarloggningsproblem och fel i Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612744"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Felsöka inloggningsproblem för konto med en hanterad Azure AD Domain Services-domän

De vanligaste orsakerna till ett användarkonto som inte kan logga in på en Azure AD DS-hanterad domän är följande scenarier:

* [Kontot synkroniseras inte med Azure AD DS ännu.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS har inte lösenordet hashar för att låta kontot logga in.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Kontot är utelåst.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan inte synkroniseras i autentiseringsuppgifter för konton som är externa till Azure AD-klienten. Externa användare kan inte logga in på azure AD DS-hanterade domänen.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Kontot synkroniseras inte i Azure AD DS ännu

Beroende på storleken på din katalog kan det ta ett tag innan användarkonton och autentiseringsuppgifterna är tillgängliga i Azure AD DS. För stora kataloger kan den här inledande enkelriktad synkroniseringen från Azure AD ta några timmar och upp till en dag eller två. Se till att du väntar tillräckligt länge innan du försöker igen autentisering.

För hybridmiljöer som användaren Azure AD Connect för att synkronisera lokala katalogdata till Azure AD, se till att du kör den senaste versionen av Azure AD Connect och har [konfigurerat Azure AD Connect för att utföra en fullständig synkronisering efter att ha aktiverat Azure AD DS][azure-ad-connect-phs]. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa dessa steg igen.

Om du fortsätter att ha problem med konton som inte synkroniseras via Azure AD Connect startar du om Azure AD Sync Service. Öppna ett kommandotolksfönster från datorn med Azure AD Connect installerat och kör följande kommandon:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS har inte lösenordshas

Azure AD genererar inte eller lagrar lösenordsh hashar i det format som krävs för NTLM- eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhetsskäl lagrar Azure AD inte heller några lösenordsuppgifter i klartextform. Därför kan Azure AD inte automatiskt generera dessa NTLM- eller Kerberos-lösenordshashar baserat på användarnas befintliga autentiseringsuppgifter.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybridmiljöer med lokal synkronisering

För hybridmiljöer som använder Azure AD Connect för att synkronisera från en lokal AD DS-miljö kan du generera och synkronisera de ntlm- eller Kerberos-lösenordshökar som krävs i Azure AD. När du har skapat din Azure AD DS-hanterade domän [aktiverar du synkronisering av lösenordshÃ¤nde till Azure Active Directory Domain Services][azure-ad-connect-phs]. Utan att slutföra det här steget för synkronisering av lösenord hash-synkronisering kan du inte logga in på ett konto med Azure AD DS. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa dessa steg igen.

Mer information finns i Så här [fungerar synkronisering av lösenord hash för Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Molnmiljöer utan lokal synkronisering

Azure AD DS-hanterade domäner utan lokal synkronisering, endast konton i Azure AD, behöver också generera de nödvändiga NTLM- eller Kerberos-lösenordshasna. Om ett konto endast för molnet inte kan logga in, har en lösenordsändringsprocess slutförts för kontot efter att Azure AD DS har aktiverats?

* **Nej, lösenordet har inte ändrats.**
    * [Ändra lösenordet för kontot för][enable-user-accounts] att generera det nödvändiga lösenordet hashar, vänta sedan i 15 minuter innan du försöker logga in igen.
    * Om du inaktiverar Azure AD DS och sedan aktiverar om, måste varje konto följa stegen igen för att ändra sitt lösenord och generera de nödvändiga lösenordshÃ¤1arna.
* **Ja, lösenordet har ändrats.**
    * Försök att logga in med *UPN-formatet,* till exempel `driley@aaddscontoso.com`, i stället för *SAMAccountName-formatet* som `AADDSCONTOSO\deeriley`.
    * *SAMAccountName* kan genereras automatiskt för användare vars UPN-prefix är alltför långt eller är samma som en annan användare på den hanterade domänen. *UPN-formatet* är garanterat unikt i en Azure AD-klientorganisation.

## <a name="the-account-is-locked-out"></a>Kontot är utelåst

Ett användarkonto i Azure AD DS är utelåst när ett definierat tröskelvärde för misslyckade inloggningsförsök har uppfyllts. Det här kontoutelåsningsbeteendet är utformat för att skydda dig från upprepade brute force-inloggningsförsök som kan tyda på en automatiserad digital attack.

Om det finns 5 felaktiga lösenordsförsök på två minuter är kontot som standard utelåst i 30 minuter.

Mer information och hur du löser problem med kontoutelåsning finns [i Felsöka problem med kontoutelåsning i Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem med att ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen [kan du hitta hjälp och öppna en supportbiljett för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
