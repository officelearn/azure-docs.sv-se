---
title: 'Azure Active Directory Domain Services: Funktioner | Microsoft Docs'
description: Funktioner i Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: f1ecf7aabaaa4f89d5f572595e32fdc9812e33dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588070"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funktioner
Följande funktioner är tillgängliga i Azure AD Domain Services hanterade domäner.

* **Enkel distributionsupplevelse:** du kan aktivera Azure AD Domain Services för din Azure AD-katalog med bara några klickningar. Din hanterade domän innehåller endast molnbaserad användarkonton och konton synkroniseras från en lokal katalog.
* **Stöd för domänanslutning:** kan du enkelt domänanslutning datorer i virtuella Azure-nätverket din hanterade domän finns i. Anslut till domän-upplevelse på Windows-klient och Server-operativsystem fungerar sömlöst mot domäner som underhålls av Azure AD Domain Services. Du kan också använda automatisk domänanslutning tooling mot dessa domäner.
* **En domän instans per Azure AD-katalog:** du kan skapa en enda Active Directory-domän för varje Azure AD-katalog.
* **Skapa domäner med egna namn:** kan du skapa domäner med anpassade namn (till exempel ”contoso100.com”) med hjälp av Azure AD Domain Services. Du kan använda antingen verifierade och overifierade domännamn. Du kan alternativt kan du också skapa en domän med det inbyggda domänsuffixet (det vill säga ' *. onmicrosoft.com') erbjuds av Azure AD-katalogen.
* **Integrerade med Azure AD:** du behöver inte konfigurera eller hantera replikering till Azure AD Domain Services. Användarkonton, gruppmedlemskap och autentiseringsuppgifter (lösenord) från Azure AD-katalogen är automatiskt tillgängliga i Azure AD Domain Services. Nya användare, grupper eller ändringar i attribut från Azure AD-klienten eller din lokala katalog synkroniseras automatiskt till Azure AD Domain Services.
* **NTLM och Kerberos-autentisering:** med stöd för NTLM och Kerberos-autentisering kan du distribuera program som förlitar sig på Windows-Integrated autentisering.
* **Använd företagets autentiseringsuppgifter/lösenorden:** lösenord för användare i din Azure AD-klient fungerar med Azure AD Domain Services. Användare kan använda sina företagsuppgifter för domänanslutning datorer, logga in interaktivt eller via fjärrskrivbord och autentiseras mot den hanterade domänen.
* **LDAP-bindning & LDAP läsa stöd:** du kan använda program som förlitar sig på LDAP-bindningar för att autentisera användare i domäner som underhålls av Azure AD Domain Services. Program som använder LDAP läsåtgärder att fråga användaren eller datorn attribut från katalogen kan dessutom också arbeta mot Azure AD Domain Services.
* **Säkert LDAP (LDAPS):** du kan aktivera åtkomst till katalogen över säker LDAP (LDAPS). Säkert är LDAP tillgängliga i det virtuella nätverket som standard. Du kan också aktivera säker LDAP-åtkomst via internet.
* **Grupprincip:** du kan använda ett inbyggt GPO varje för användare och datorer som behållare för att tvinga kompatibilitet med krävs säkerhetsprinciper för användarkonton och domänanslutna datorer. Du kan också skapa egna anpassade grupprincipobjekt och tilldela dem till anpassade organisationsenheter [hanterar Grupprincip](active-directory-ds-admin-guide-administer-group-policy.md).
* **Hantera DNS:** medlemmar i gruppen AAD DC-administratörer kan hantera DNS för din hanterade domän med hjälp av välbekanta DNS Administrationsverktyg, till exempel DNS-Administration MMC-snapin-modulen.
* **Skapa anpassade organisationsenheter (OU):** medlemmar i gruppen AAD DC-administratörer kan skapa anpassade organisationsenheter i den hanterade domänen. Dessa användare beviljas fullständig administratörsbehörighet över anpassade organisationsenheter, så att de kan lägga till eller ta bort tjänstkonton, datorer, grupper osv. i dessa anpassade organisationsenheter.
* **I många Azure globala regioner:** finns i [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) att veta vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
* **Hög tillgänglighet:** Azure AD Domain Services ger hög tillgänglighet för din domän. Den här funktionen ger för bättre service driftstid och återhämtning för fel. Inbyggda hälsoövervakning erbjudanden automatisk reparation från fel av snurrande in nya instanser kan ersätta misslyckade instanser och tillhandahålla tjänster för din domän.
* **AD-kontot kontoutelåsning skydd:** användarkonton är utelåst under 30 minuter om fem ogiltigt lösenord används inom 2 minuter. Konton låses automatiskt efter 30 minuter.
* **Använda välbekanta verktyg:** du kan använda välbekanta Windows Server Active Directory-hanteringsverktyg, till exempel Active Directory Administrationscenter eller Active Directory PowerShell för att administrera hanterade domäner.
