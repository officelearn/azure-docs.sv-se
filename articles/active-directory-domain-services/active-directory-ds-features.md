---
title: 'Azure Active Directory Domain Services: Funktioner | Microsoft Docs'
description: Funktioner i Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 9e4ab7aa4f61921d8b327404a266694349d78164
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60417273"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funktioner
Följande funktioner är tillgängliga i Azure AD Domain Services hanterade domäner.

* **Enkel distributionsupplevelse:** Du kan aktivera Azure AD Domain Services för Azure AD-katalogen med bara några klick. Den hanterade domänen innehåller endast molnbaserade användarkonton och användarkonton som synkroniseras från en lokal katalog.
* **Stöd för domänanslutning:** Du kan enkelt domänanslutning datorer i Azure-nätverk som din hanterade domän finns i. Domänanslutning upplevelse på Windows-klienten och servern operativsystem fungerar sömlöst mot domäner som underhålls av Azure AD Domain Services. Du kan också använda automatisk domänanslutning verktyg mot sådana domäner.
* **En domän instans per Azure AD-katalog:** Du kan skapa en enda Active Directory-domän för varje Azure AD-katalog.
* **Skapa domäner med anpassade namn:** Du kan skapa domäner med anpassade namn (till exempel ”contoso100.com”) med hjälp av Azure AD Domain Services. Du kan använda antingen verifierade och overifierade domännamn. Du kan eventuellt också skapa en domän med inbyggda domänsuffix (det vill säga ' *. onmicrosoft.com') erbjuds av Azure AD-katalogen.
* **Integrerad med Azure AD:** Du behöver inte konfigurera eller hantera replikering till Azure AD Domain Services. Användarkonton, gruppmedlemskap och autentiseringsuppgifter (lösenord) från Azure AD-katalogen är automatiskt tillgängliga i Azure AD Domain Services. Nya användare, grupper eller ändringar av attribut från Azure AD-klienten eller en lokal katalog synkroniseras automatiskt till Azure AD Domain Services.
* **NTLM och Kerberos-autentisering:** Du kan distribuera program som förlitar sig på Windows-Integrated autentisering med stöd för NTLM och Kerberos-autentisering.
* **Använd dina företagets autentiseringsuppgifter/lösenord:** Lösenord för användare i Azure AD-klienten fungerar med Azure AD Domain Services. Användare kan använda sina inloggningsuppgifter till domänanslutning datorer, logga in interaktivt eller via fjärrskrivbord och autentisera mot den hanterade domänen.
* **LDAP-bindning & LDAP Läs support:** Du kan använda program som förlitar sig på LDAP-bindningar för att autentisera användare i domäner som underhålls av Azure AD Domain Services. Program som använder LDAP läsåtgärder att fråga användaren eller datorn attribut från katalogen kan dessutom också fungerar mot Azure AD Domain Services.
* **Säkert LDAP (LDAPS):** Du kan aktivera åtkomst till katalogen över säker LDAP (LDAPS). Åtkomst med säkert LDAP är tillgänglig i det virtuella nätverket som standard. Du kan även aktivera åtkomst med säkert LDAP via internet.
* **Grupprincip:** Du kan använda en enda inbyggda Grupprincipobjektet varje för användare och datorer behållare för att tvinga kompatibilitet med krävs säkerhetsprinciper för användarkonton och domänanslutna datorer. Du kan också skapa dina egna anpassade grupprincipobjekt och tilldela dem till organisationsenheter som är anpassade till [hanterar Grupprincip](active-directory-ds-admin-guide-administer-group-policy.md).
* **Hantera DNS:** Medlemmar i gruppen ”AAD DC-administratörer” hantera DNS för din hanterade domän via välbekanta DNS-Administrationsverktyg, till exempel DNS-Administration MMC-snapin-modulen.
* **Skapa anpassade organisationsenheter (OU):** Medlemmar i gruppen AAD DC-administratörer kan skapa anpassade organisationsenheter i den hanterade domänen. Dessa användare beviljas fullständig administratörsbehörighet över anpassade organisationsenheter, så att de kan lägga till/ta bort tjänstkonton, datorer, grupper osv inom dessa anpassade organisationsenheter.
* **Tillgänglig i många globala Azure-regioner:** På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
* **Hög tillgänglighet:** Azure AD Domain Services ger hög tillgänglighet för din domän. Den här funktionen ger garanti för högre tjänstens drifttid och återhämtning vid fel. Inbyggda hälsoövervakning erbjudanden automatisk reparation från fel genom att skapa nya instanser att ersätta misslyckade förekomster och tillhandahålla tjänster för din domän.
* **AD-kontots utelåsningsskydd:** Användarkonton är utelåst under 30 minuter om fem ogiltigt lösenord används inom två minuter. Konton är automatiskt bort efter 30 minuter.
* **Använd välbekanta verktyg:** Du kan använda välbekanta Windows Server Active Directory-hanteringsverktyg, till exempel Active Directory Administrationscenter eller Active Directory PowerShell för att administrera hanterade domäner.
