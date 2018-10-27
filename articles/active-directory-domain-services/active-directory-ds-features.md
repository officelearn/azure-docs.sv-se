---
title: 'Azure Active Directory Domain Services: Funktioner | Microsoft Docs'
description: Funktioner i Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: cd4fdb1e59f5b85fdfd8eb7ca17b77d02dcdac05
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157045"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funktioner
Följande funktioner är tillgängliga i Azure AD Domain Services hanterade domäner.

* **Enkel distributionsupplevelse:** du kan aktivera Azure AD Domain Services för Azure AD-katalogen med bara några klick. Den hanterade domänen innehåller endast molnbaserade användarkonton och användarkonton som synkroniseras från en lokal katalog.
* **Stöd för domänanslutning:** du kan enkelt domänanslutning datorer i Azure-nätverk som din hanterade domän finns i. Domänanslutning upplevelse på Windows-klienten och servern operativsystem fungerar sömlöst mot domäner som underhålls av Azure AD Domain Services. Du kan också använda automatisk domänanslutning verktyg mot sådana domäner.
* **En domän instans per Azure AD-katalog:** du kan skapa en enda Active Directory-domän för varje Azure AD-katalog.
* **Skapa domäner med anpassade namn:** kan du skapa domäner med anpassade namn (till exempel ”contoso100.com”) med hjälp av Azure AD Domain Services. Du kan använda antingen verifierade och overifierade domännamn. Du kan eventuellt också skapa en domän med inbyggda domänsuffix (det vill säga ' *. onmicrosoft.com') erbjuds av Azure AD-katalogen.
* **Integrerad med Azure AD:** du behöver inte konfigurera eller hantera replikering till Azure AD Domain Services. Användarkonton, gruppmedlemskap och autentiseringsuppgifter (lösenord) från Azure AD-katalogen är automatiskt tillgängliga i Azure AD Domain Services. Nya användare, grupper eller ändringar av attribut från Azure AD-klienten eller en lokal katalog synkroniseras automatiskt till Azure AD Domain Services.
* **NTLM och Kerberos-autentisering:** med stöd för NTLM och Kerberos-autentisering kan du distribuera program som förlitar sig på Windows-Integrated autentisering.
* **Använd dina företagets autentiseringsuppgifter/lösenord:** lösenorden för användare i din Azure AD-klient fungerar med Azure AD Domain Services. Användare kan använda sina inloggningsuppgifter till domänanslutning datorer, logga in interaktivt eller via fjärrskrivbord och autentisera mot den hanterade domänen.
* **LDAP-bindning & LDAP läsa support:** du kan använda program som förlitar sig på LDAP-bindningar för att autentisera användare i domäner som underhålls av Azure AD Domain Services. Program som använder LDAP läsåtgärder att fråga användaren eller datorn attribut från katalogen kan dessutom också fungerar mot Azure AD Domain Services.
* **Säkert LDAP (LDAPS):** du kan aktivera åtkomst till katalogen över säker LDAP (LDAPS). Åtkomst med säkert LDAP är tillgänglig i det virtuella nätverket som standard. Du kan även aktivera åtkomst med säkert LDAP via internet.
* **Grupprincip:** du kan använda en enda inbyggda Grupprincipobjektet varje för användare och datorer behållare för att tvinga kompatibilitet med krävs säkerhetsprinciper för användarkonton och domänanslutna datorer. Du kan också skapa dina egna anpassade grupprincipobjekt och tilldela dem till organisationsenheter som är anpassade till [hanterar Grupprincip](active-directory-ds-admin-guide-administer-group-policy.md).
* **Hantera DNS:** medlemmar i gruppen ”AAD DC-administratörer” hantera DNS för din hanterade domän via välbekanta DNS-Administrationsverktyg, till exempel DNS-Administration MMC-snapin-modulen.
* **Skapa anpassade organisationsenheter (OU):** medlemmar i gruppen AAD DC-administratörer kan skapa anpassade organisationsenheter i den hanterade domänen. Dessa användare beviljas fullständig administratörsbehörighet över anpassade organisationsenheter, så att de kan lägga till/ta bort tjänstkonton, datorer, grupper osv inom dessa anpassade organisationsenheter.
* **Tillgängliga i många globala Azure-regioner:** finns i den [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) att Azure-regioner där Azure AD Domain Services är tillgängligt.
* **Hög tillgänglighet:** Azure AD Domain Services ger hög tillgänglighet för din domän. Den här funktionen ger garanti för högre tjänstens drifttid och återhämtning vid fel. Inbyggda hälsoövervakning erbjudanden automatisk reparation från fel genom att skapa nya instanser att ersätta misslyckade förekomster och tillhandahålla tjänster för din domän.
* **AD-kontots utelåsningsskydd:** användarkonton är utelåst under 30 minuter om fem ogiltigt lösenord används inom två minuter. Konton är automatiskt bort efter 30 minuter.
* **Använd välbekanta verktyg:** du kan använda välbekanta Windows Server Active Directory-hanteringsverktyg, till exempel Active Directory Administrationscenter eller Active Directory PowerShell för att administrera hanterade domäner.
