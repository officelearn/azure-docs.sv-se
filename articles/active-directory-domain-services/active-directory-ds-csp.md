---
title: Azure Active Directory Domain Services för Azure Cloud Solution Providers | Microsoft Docs
description: Azure Active Directory Domain Services för Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 8d87312591f44bac5fd9a4ff63eccc19333a870c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055785"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services för Azure Cloud Solution Providers (CSP)
Den här artikeln förklarar hur du kan använda Azure AD Domain Services i en Azure CSP-prenumeration.

## <a name="overview-of-azure-csp"></a>Översikt över Azure CSP
Azure CSP är ett program för Microsoft-Partners och tillhandahåller en kanal som licens för olika Microsoft-molntjänster. Azure CSP kan partner hantera försäljning, äga, ge teknisk support och faktureringssupport och vara kundens enda kontaktpunkt. Dessutom tillhandahåller Azure CSP en komplett uppsättning verktyg, bland annat en självbetjäningsportal och medföljande API: er. Dessa verktyg aktivera CSP-partner att enkelt etablera och hantera Azure-resurser och tillhandahållande av fakturering för kunder och prenumerationer.

Den [Partner Center-portalen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungerar som en startpunkt för alla Azure CSP-partner. Det ger funktioner för hantering av spännande och automatisk behandling. Azure CSP-partner kan använda Partner Center-funktioner med hjälp av ett webbaserat gränssnitt eller med hjälp av PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på hög nivå. Contoso har en Azure AD Active Directory. De har ett samarbete med en CSP som distribuerar och hanterar resurser i Azure CSP-prenumerationen. Contoso kan också ha regelbundna (direkt) Azure-prenumerationer, som faktureras direkt till Contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partner-klienten har tre grupper för särskild agent - administratör agenter, supportavdelningen agenter och agenter för försäljning. Administratörsgrupp för agenter har tilldelats klientadministratörsroll i Contosos Azure AD-katalog. En användare som hör till CSP-partner administratörsgrupp för agenter har därför klient admin-behörighet i Contosos Azure AD-katalog. När den CSP-partner tillhandahåller en Azure CSP-prenumeration för Contoso, deras agenter administratörsgrupp tilldelas rollen ägare för den aktuella prenumerationen. Därför kan har CSP-partner admin agenter de behörigheter som krävs för att etablera Azure-resurser som virtuella datorer, virtuella nätverk och Azure AD Domain Services för Contoso.

Mer information finns i den [Azure CSP-översikt](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Fördelarna med att använda Azure AD Domain Services i en Azure CSP-prenumeration
Azure AD Domain Services tillhandahåller Windows Server AD-kompatibla tjänster i Azure, till exempel LDAP, Kerberos/NTLM-autentisering, domänanslutning, Grupprincip och DNS. Under årtiondena har många program skapats för att arbeta mot AD med hjälp av dessa funktioner. Många oberoende programvaruleverantörer (ISV) har skapats och distribuerade program i sina kunders lokaler. Dessa program är betungande för eftersom som ofta kräver åtkomst till olika miljöer där programmen distribueras. Med Azure CSP-prenumerationer har du ett enklare alternativ med skala och flexibilitet i Azure.

Azure AD Domain Services har nu stöd för Azure CSP-prenumerationer. Du kan nu distribuera ditt program i en Azure CSP-prenumeration som är kopplad till din kunds Azure AD-katalog. Dina anställda (supportpersonalen) kan därför hantera, administrera och de virtuella datorerna som ditt program har distribuerats med din organisations företagsuppgifter-tjänsten. Dessutom kan du etablera en Azure AD Domain Services-hanterad domän för din kunds Azure AD-katalog. Ditt program är ansluten till din kunds hanterade domänen. Därför kan de funktioner i ditt program som förlitar sig på Kerberos/NTLM, LDAP, eller [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) arbets sömlöst mot din kunds katalog. Dina slutkunder dra avsevärt använder ditt program som en tjänst, utan att behöva bekymra dig om att underhålla infrastrukturen som programmet har distribuerats på.

Alla fakturering för Azure-resurser du förbrukar i den prenumerationen, inklusive Azure AD Domain Services debiteras till dig. Du kan ha fullständig kontroll över relationen med kunden när det gäller försäljning, fakturering, teknisk support osv. Med flexibilitet i Azure CSP-plattformen, en liten grupp agenter kan underhålla många kunder som har instanser av ditt program distribuerats.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP-distributionsmodeller för Azure AD Domain services
Det finns två sätt som du kan använda Azure AD Domain Services med en Azure CSP-prenumeration. Välj det rätt baserat på de överväganden för säkerhet och enkelhet som dina kunder har.

### <a name="direct-deployment-model"></a>Direkt-distributionsmodellen
I denna distributionsmodell Azure AD Domain Services aktiveras inom ett virtuellt nätverk som hör till Azure CSP-prenumeration. CSP-partner admin agenter har följande behörigheter:
* Behörigheter som global administratör i kundens Azure AD-katalog.
* Prenumeration ägarprivilegier för Azure CSP-prenumerationen.

![Direkt-distributionsmodellen](./media/csp/csp_direct_deployment_model.png)

I denna distributionsmodell administrera CSP-provider admin agenter identiteter för kunden. Agenterna administratören har möjlighet att etablera nya användare, grupper, lägga till program i Azure AD-katalog för kundens osv. Denna distributionsmodell kan vara lämpligt för mindre organisationer som inte har en dedikerad identity-administratör eller föredrar för CSP-partner att administrera identiteter för deras räkning.


### <a name="peered-deployment-model"></a>Peer-kopplade distributionsmodell
I denna distributionsmodell Azure AD Domain Services aktiveras inom ett virtuellt nätverk som tillhör kunden – det vill säga en direkt Azure-prenumeration som betalas av kunden. CSP-partner kan sedan distribuera program i ett virtuellt nätverk som tillhör kundens CSP-prenumeration. De virtuella nätverken kan sedan kopplas med hjälp av Azure vnet-peering. Därför kan kan arbetsbelastningar/program som distribuerats av CSP-partner i Azure CSP-prenumeration ansluta till kundens hanterade domänen tillhandahålls i kundens direkt Azure-prenumeration.

![Peer-kopplade distributionsmodell](./media/csp/csp_peered_deployment_model.png)

Denna distributionsmodell ger en åtskillnad mellan privilegier och gör det möjligt för CSP-partner supportavdelningen agenter att administrera Azure-prenumeration och distribuera och hantera resurser i den. CSP-partner supportavdelningen agenter behöver dock inte har behörigheter som global administratör för kundens Azure AD-katalog. Kundens identitetsadministratörer kan fortsätta att hantera identiteter för organisationen.

Denna distributionsmodell kan vara bra för scenarier där en ISV (oberoende programvaruleverantör) tillhandahåller en värdbaserad version av sina lokala program som måste också ansluta till kunden är AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrera Azure AD Domain Services hanterade domäner i CSP-prenumerationer
Följande viktiga överväganden gäller när du administrerar en hanterad domän i en Azure CSP-prenumeration:

* **Agenter för CSP-administratör kan etablera en hanterad domän med sina autentiseringsuppgifter:** Azure AD Domain Services har stöd för Azure CSP-prenumerationer. Användare som tillhör en CSP-partner administratörsgrupp för agenter kan därför etablera en ny Azure AD Domain Services-hanterad domän.

* **CSP: er kan skript skapandet av nya hanterade domäner för sina kunder med hjälp av PowerShell:** Se [hur att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md) mer information.

* **Agenter för CSP-administratör kan inte utföra pågående hanteringsuppgifter på den hanterade domänen med sina autentiseringsuppgifter:** CSP-administratörer kan inte utföra vanliga hanteringsuppgifter i den hanterade domänen med sina autentiseringsuppgifter. Dessa användare är externa för kundens Azure AD-katalog och sina autentiseringsuppgifter är inte tillgängliga i kundens Azure AD-katalog. Azure AD Domain Services har därför inte åtkomst till Kerberos- och NTLM-lösenordshashvärden för dessa användare. Därför kan kan dessa användare inte autentiseras på Azure AD Domain Services hanterade domäner.

  > [!WARNING]
  > **Du måste skapa ett användarkonto i kundens directory att utföra löpande administrationsuppgifter i den hanterade domänen.**
  > Du kan inte logga in till den hanterade domänen med hjälp av en administratörsanvändare för CSP-autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett användarkonto som tillhör kundens Azure AD-katalog för att göra detta. Du behöver dessa autentiseringsuppgifter för uppgifter som att ansluta virtuella datorer till den hanterade domänen, administrera DNS, administrera grupp princip osv.
  >

* **Det användarkonto som har skapats för löpande administrativa måste läggas till gruppen ”AAD DC-administratörer”:** gruppen ”AAD DC-administratörer” har behörighet att utföra vissa uppgifter för delegerad administration i den hanterade domänen. Dessa omfattar att konfigurera DNS, skapa organisationsenheter, administrera Grupprincip osv. För CSP-partner att utföra uppgifter på en hanterad domän, behöver ett konto skapas i kundens Azure AD-katalog. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partner admin-agenter. Det här användarkontot måste också läggas till gruppen ”AAD DC-administratörer” för att aktivera konfigurationsåtgärder i den hanterade domänen som ska utföras med hjälp av det här användarkontot.


## <a name="next-steps"></a>Nästa steg
* [Registrera dig i Azure CSP-programmet](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) och börja skapa verksamhet med Azure CSP.
* Granska listan över [Azure-tjänster i Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Aktivera Azure AD Domain Services med PowerShell](active-directory-ds-enable-using-powershell.md)
* [Kom igång med Azure AD Domain Services](active-directory-ds-getting-started.md)
