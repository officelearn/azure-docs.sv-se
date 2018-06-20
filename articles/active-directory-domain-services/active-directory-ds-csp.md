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
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 362d7226434733ffeaa5be6f988afa4016a7c827
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213880"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services för Azure Cloud Solution Providers (CSP)
Den här artikeln förklarar hur du kan använda Azure AD Domain Services i en CSP för Azure-prenumeration.

## <a name="overview-of-azure-csp"></a>Översikt över Azure CSP
Azure CSP är ett program för Microsoft-Partners som ger en licens kanal för olika Microsoft-molntjänster. Azure CSP gör det möjligt för partner att hantera försäljning, äger faktureringsrelation, teknik och fakturering stödja och vara kundens enda kontaktpunkt. Azure CSP innehåller också en fullständig uppsättning av verktyg, inklusive en självbetjäningsportal och tillhörande API: er. Dessa verktyg aktivera CSP-partners att enkelt konfigurera och hantera Azure-resurser och ange fakturering för kunder och deras prenumerationer.

Den [Partnercenter portal](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungerar som en startpunkt för alla Azure CSP-partner. Det ger funktioner för hantering av omfattande kund och automatisk behandling. Azure CSP-partners kan använda Partnercenter funktioner med hjälp av ett webbaserat gränssnitt eller med hjälp av PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på hög nivå. Contoso har en Azure AD Active Directory. De har ett samarbete med en CSP som distribuerar och hanterar resurser i Azure CSP prenumerationen. Contoso kan också ha regelbundna (direkt) Azure-prenumerationer som debiteras direkt till Contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partnerns innehavare har tre grupper för särskild agent - Admin agenter, supportavdelning agenter och agenter för försäljning. Administratörsgrupp för agenter har tilldelats klientadministratörsroll i Contosos Azure AD-katalog. En användare som hör till CSP-partnerns administratörsgrupp agenter har därför klient administratörsrättigheter i Contosos Azure AD-katalog. När den CSP partner tillhandahåller en Azure CSP-prenumeration för Contoso, deras agenter administratörsgrupp tilldelas rollen ägare för den prenumerationen. Därför har CSP-partnerns admin agenter de behörigheter som krävs för att etablera Azure-resurser som virtuella datorer, virtuella nätverk och Azure AD Domain Services för Contoso.

Mer information finns i [Azure CSP-översikt](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Fördelarna med att använda Azure AD Domain Services i en CSP för Azure-prenumeration
Azure AD Domain Services ger kompatibla Windows Server AD-tjänster i Azure, till exempel LDAP, Kerberos/NTLM-autentisering, domänanslutning, Grupprincip och DNS. Under åren, har många program byggts om du vill arbeta mot AD med hjälp av dessa funktioner. Många oberoende programvaruleverantörer (ISV) har skapats och distribuerade program i sina kunder lokaler. Dessa program är förlustbringande att stödja eftersom som kräver ofta åtkomst till olika miljöer där programmen har distribuerats. Med Azure CSP prenumerationer har ett enklare alternativ med skala och flexibilitet i Azure.

Azure AD Domain Services stöder nu CSP för Azure-prenumerationer. Du kan nu distribuera ditt program i en CSP för Azure-prenumeration knutna till kundens Azure AD-katalog. Dina anställda (supportpersonal) kan därför hantera, administrera och tjänsten virtuella datorer som programmet har distribuerats med företagets autentiseringsuppgifter för din organisation. Dessutom kan du etablera en Azure AD Domain Services-hanterad domän för kundens Azure AD-katalog. Ditt program är anslutet till kundens hanterad domän. Därför funktioner i ditt program som förlitar sig på Kerberos/NTLM, LDAP, eller [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) fungerar sömlöst mot kundens katalog. Kunderna slutet nytta avsevärt från att konsumera ditt program som en tjänst utan att behöva bekymra dig om att underhålla infrastrukturen som programmet har distribuerats på.

Alla fakturering för Azure-resurser du använder i den prenumeration, inklusive Azure AD Domain Services debiteras tillbaka till dig. Du kan underhålla fullständig kontroll över relationen med kunden när det gäller försäljning, fakturering, teknisk support osv. Med flexibilitet i Azure CSP-plattformen, en liten grupp med stöd för agenter kan betjäna många kunder som har instanser av programmet distribueras.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP distributionsmodeller för Azure AD Domain services
Det finns två sätt som du kan använda Azure AD Domain Services med en CSP för Azure-prenumeration. Välj det högra baserat på de överväganden för säkerhet och enkelhet som kunder har.

### <a name="direct-deployment-model"></a>Direkt distributionsmodell
I denna distributionsmodell Azure AD Domain Services aktiverat inom ett virtuellt nätverk som hör till CSP: N för Azure-prenumeration. CSP-partnerns admin agenter har följande behörigheter:
* Global administratörsbehörighet i kundens Azure AD-katalog.
* Prenumerationen ägare privilegier på Azure CSP-prenumeration.

![Direkt distributionsmodell](./media/csp/csp_direct_deployment_model.png)

I denna distributionsmodell administrera CSP-leverantörens admin agenter identiteter för kunden. Dessa admin-agenter har möjlighet att etablera nya användare, grupper, lägga till program i Azure AD-katalog för kundens osv. Denna distributionsmodell kan vara lämpligt för mindre organisationer som inte har en dedikerad identitetsadministratör eller föredrar för CSP-partner att administrera identiteter åt.


### <a name="peered-deployment-model"></a>Peerkoppla distributionsmodell
I denna distributionsmodell Azure AD Domain Services aktiverat inom ett virtuellt nätverk som hör till kund - som är en direkt Azure-prenumeration betalat för kunden. CSP-partner kan sedan distribuera program inom ett virtuellt nätverk som hör till kundens CSP-prenumeration. Virtuella nätverk kan sedan kopplas med hjälp av virtuella Azure-nätverket peering. Därför kan arbetsbelastningar/program som har distribuerats av CSP-partner i Azure CSP-prenumeration ansluta till kundens hanterad domän som etablerats i kundens direkt Azure-prenumeration.

![Peerkoppla distributionsmodell](./media/csp/csp_peered_deployment_model.png)

Denna distributionsmodell tillhandahåller en avgränsning av privilegier och möjliggör CSP-partnerns supportavdelningen agenter för att administrera Azure-prenumerationen, distribuera och hantera resurser i den. Dock behöver inte CSP-partnerns supportavdelningen agenter har globala administratörsbehörigheter på kundens Azure AD-katalog. Kundens identity-administratörer fortsätta att hantera identiteter för organisationen.

Denna distributionsmodell kan vara lämpliga i scenarier där en ISV (oberoende programvaruleverantör) tillhandahåller en värdbaserad version av programmet lokalt, som också behöver för att ansluta till kunden har AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrera Azure AD Domain Services hanterade domäner i CSP-prenumerationer
Följande viktiga gäller när du administrerar en hanterad domän i en prenumeration på Azure-CSP:

* **Agenter för CSP-administratör kan etablera en hanterad domän med hjälp av autentiseringsuppgifterna:** CSP för Azure-prenumerationer har stöd för Azure AD Domain Services. Därför kan användare som tillhör en CSP partner agenter administratörsgrupp etablera en ny Azure AD Domain Services-hanterad domän.

* **CSP: er kan skapa nya hanterade domäner för sina kunder som använder PowerShell skript:** finns [så att aktivera Azure AD Domain Services med hjälp av PowerShell](active-directory-ds-enable-using-powershell.md) mer information.

* **CSP admin agenter kan inte utföra pågående hanteringsuppgifter på hanterade domänen med sina autentiseringsuppgifter:** CSP administrativa användare kan inte utföra vanliga hanteringsuppgifter i den hanterade domänen med sina autentiseringsuppgifter. Dessa användare är externa för kundens Azure AD-katalog och autentiseringsuppgifterna är inte tillgängliga i kundens Azure AD-katalog. Azure AD Domain Services har därför inte åtkomst till lösenordshashvärden Kerberos och NTLM för dessa användare. Därför kan kan dessa användare inte autentiseras på Azure AD Domain Services hanterade domäner.

  > [!WARNING]
  > **Du måste skapa ett användarkonto i kundens katalog att utföra löpande administrationsuppgifter på den hanterade domänen.**
  > Du kan inte logga in på den hanterade domänen med hjälp av en administratörsanvändare för CSP-autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett konto som hör till Azure AD-katalog för kundens gör. Du behöver dessa autentiseringsuppgifter för aktiviteter, till exempel ansluta virtuella datorer till den hanterade domänen, administrera DNS, administrera grupp princip osv.
  >

* **Det användarkonto som har skapats för löpande administrativa måste läggas till gruppen AAD DC-administratörer:** gruppen AAD DC-administratörer har behörighet att utföra vissa uppgifter för delegerad administration på den hanterade domänen. Dessa omfattar att konfigurera DNS, skapa organisationsenheter, administrera grupprinciper osv. Ett användarkonto måste skapas i kundens Azure AD-katalog för en CSP partner att utföra dessa uppgifter på en hanterad domän. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partnerns admin agenter. Kontot måste också läggas till gruppen AAD DC-administratörer att aktivera konfigurationsuppgifter på den hanterade domänen som ska utföras med det här användarkontot.


## <a name="next-steps"></a>Nästa steg
* [Registrering i Azure CSP program](https://partnercenter.microsoft.com/partner/programs) och börja skapa företag genom Azure CSP.
* Granska listan över [Azure-tjänster i Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Aktivera Azure AD Domain Services med PowerShell](active-directory-ds-enable-using-powershell.md)
* [Komma igång med Azure AD Domain Services](active-directory-ds-getting-started.md)
