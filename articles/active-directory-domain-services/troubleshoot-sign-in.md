---
title: Felsöka inloggnings problem i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du felsöker vanliga användar inloggnings problem och fel i Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827055"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Felsöka konto inloggnings problem med en Azure AD Domain Services hanterad domän

De vanligaste orsakerna till ett användar konto som inte kan logga in på en hanterad Azure AD DS-domän är följande scenarier:

* [Kontot är inte synkroniserat med Azure AD DS ännu.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS har inte lösen ordets hash-värden för att tillåta kontot att logga in.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Kontot är utelåst.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan inte synkronisera i autentiseringsuppgifter för konton som är externa för Azure AD-klienten. Externa användare kan inte logga in på den hanterade domänen i Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Kontot är inte synkroniserat med Azure AD DS ännu

Beroende på katalogens storlek kan det ta en stund innan användar konton och inloggnings-hashar är tillgängliga i Azure AD DS. För stora kataloger kan den här inledande enkelriktade synkroniseringen från Azure AD ta några timmar och upp till en dag eller två. Se till att du väntar tillräckligt länge innan du försöker autentisera igen.

Se till att du kör den senaste versionen av Azure AD Connect och att du har [konfigurerat Azure AD Connect för att utföra en fullständig synkronisering när du har aktiverat Azure för Hybrid miljöer som användaren Azure AD Connect att synkronisera lokala katalog data till Azure AD AD DS][azure-ad-connect-phs]. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa de här stegen igen.

Om du fortsätter att ha problem med konton som inte synkroniseras via Azure AD Connect startar du om Azure AD Sync tjänsten. Från datorn med Azure AD Connect installerat öppnar du ett kommando tolks fönster och kör följande kommandon:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS har inte lösen ordets hash-värden

Azure AD genererar eller lagrar inte lösen ordets hash-värden i det format som krävs för NTLM-eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybrid miljöer med lokal synkronisering

För Hybrid miljöer som använder Azure AD Connect för att synkronisera från en lokal AD DS-miljö kan du lokalt generera och synkronisera de obligatoriska NTLM-eller Kerberos-hashvärden i Azure AD. När du har skapat din Azure AD DS-hanterade domän [aktiverar du Azure Active Directory Domain Services med hash-synkronisering av lösen ord][azure-ad-connect-phs]. Det går inte att logga in på ett konto med Azure AD DS utan att slutföra det här steget för synkronisering av lösen ord. Om du inaktiverar Azure AD DS och sedan aktiverar igen måste du följa dessa steg igen.

Mer information finns i [så här fungerar hash-synkronisering av lösen ord för Azure AD DS][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Miljöer med enbart moln utan lokal synkronisering

Azure AD DS-hanterade domäner utan lokal synkronisering, det är bara konton i Azure AD som måste generera nödvändiga NTLM-eller Kerberos-hashvärden. Om ett moln konto inte kan logga in, har en process för lösen ords ändring slutförts för kontot när Azure AD DS har Aktiver ATS?

* **Nej, lösen ordet har inte ändrats.**
    * [Ändra lösen ordet för kontot][enable-user-accounts] för att generera nödvändiga lösen ords-hashar och vänta sedan i 15 minuter innan du försöker logga in igen.
    * Om du inaktiverar Azure AD DS och sedan aktiverar igen måste varje konto följa stegen igen för att ändra lösen ordet och generera nödvändiga lösen ords-hashar.
* **Ja, lösen ordet har ändrats.**
    * Försök att logga in med *UPN* -formatet, till exempel `driley@contoso.com`, i stället för *sAMAccountName* -formatet som `CONTOSO\deeriley`.
    * *SAMAccountName* kan genereras automatiskt för användare vars UPN-prefix är över långt eller samma som en annan användare på den hanterade domänen. *UPN* -formatet garanterar att det är unikt i en Azure AD-klient.

## <a name="the-account-is-locked-out"></a>Kontot är utelåst

Ett användar konto i Azure AD DS är utelåst när ett definierat tröskelvärde för misslyckade inloggnings försök har uppfyllts. Detta konto utelåsning är utformat för att skydda dig från upprepade återkrävda inloggnings försök som kan tyda på ett automatiskt digitalt angrepp.

Om det finns fem dåliga lösen ords försök på 2 minuter låses kontot som standard i 30 minuter.

Mer information och hur du löser problem med konto utelåsning finns i [Felsöka problem med konto utelåsning i Azure AD DS][troubleshoot-account-lockout].

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem med att ansluta den virtuella datorn till den hanterade Azure AD DS-domänen kan du [hitta hjälp och öppna ett support ärende för Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
