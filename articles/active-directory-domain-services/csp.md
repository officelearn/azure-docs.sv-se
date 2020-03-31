---
title: Azure AD-domäntjänster för molnlösningsleverantörer | Microsoft-dokument
description: Azure Active Directory Domain Services för Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754430"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domäntjänster för CSP (Azure Active Directory)
I den här artikeln beskrivs hur du kan använda Azure AD Domain Services i en Azure CSP-prenumeration.

## <a name="overview-of-azure-csp"></a>Översikt över Azure CSP
Azure CSP är ett program för Microsoft Partners och tillhandahåller en licenskanal för olika Microsoft-molntjänster. Azure CSP gör det möjligt för partner att hantera försäljning, äga faktureringsrelationen, tillhandahålla teknisk support och faktureringssupport och vara kundens enda kontaktpunkt. Dessutom innehåller Azure CSP en fullständig uppsättning verktyg, inklusive en självbetjäningsportal och medföljande API:er. Dessa verktyg gör det möjligt för CSP-partner att enkelt etablera och hantera Azure-resurser och tillhandahålla fakturering för kunder och deras prenumerationer.

[Partnercenterportalen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungerar som en startpunkt för alla Azure CSP-partner. Det ger omfattande kundhanteringsfunktioner, automatiserad bearbetning med mera. Azure CSP-partner kan använda Partner Center-funktioner med hjälp av ett webbaserat användargränssnitt eller genom att använda PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på en hög nivå. Contoso har en Azure AD Active Directory. De har ett partnerskap med en CSP som distribuerar och hanterar resurser i sin Azure CSP-prenumeration. Contoso kan också ha vanliga (direkta) Azure-prenumerationer, som faktureras direkt till Contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partnerns klient har tre specialagentgrupper - administratörsagenter, Helpdesk-agenter och försäljningsagenter. Gruppen Administratörsagenter tilldelas rollen klientadministratör i Contosos Azure AD-katalog. Därför har en användare som tillhör CSP-partnerns administratörsagentgrupp behörigheter för klientadministratörer i Contosos Azure AD-katalog. När CSP-partnern etablerar en Azure CSP-prenumeration för Contoso tilldelas deras administratörsagentgrupp ägarrollen för den prenumerationen. Därför har CSP-partnerns administratörsagenter de behörigheter som krävs för att etablera Azure-resurser som virtuella datorer, virtuella nätverk och Azure AD Domain Services på uppdrag av Contoso.

Mer information finns i [Översikt över Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Fördelar med att använda Azure AD Domain Services i en Azure CSP-prenumeration
Azure AD Domain Services tillhandahåller Windows Server AD-kompatibla tjänster i Azure, till exempel LDAP, Kerberos/NTLM-autentisering, domänanslutning, grupprincip och DNS. Under årtiondena har många program byggts för att fungera mot AD med hjälp av dessa funktioner. Många oberoende programvaruleverantörer (ISV) har byggt och distribuerat program i sina kunders lokaler. Dessa program är betungande att stödja eftersom det ofta kräver åtkomst till de olika miljöer där dessa program distribueras. Med Azure CSP-prenumerationer har du ett enklare alternativ med Azures skala och flexibilitet.

Azure AD Domain Services stöder nu Azure CSP-prenumerationer. Du kan nu distribuera ditt program i en Azure CSP-prenumeration som är knuten till kundens Azure AD-katalog. Därför kan dina anställda (supportpersonal) hantera, administrera och betjäna de virtuella datorer som programmet distribueras på med hjälp av organisationens företagsautentiseringsuppgifter. Vidare kan du etablera en Hanterad Azure AD-domän för hanterade domäner för kundens Azure AD-katalog. Ditt program är anslutet till kundens hanterade domän. Därför fungerar funktioner i ditt program som är beroende av Kerberos/NTLM, LDAP eller [System.DirectoryServices API](/dotnet/api/system.directoryservices) sömlöst mot kundens katalog. Dina slutkunder drar stor nytta av att konsumera ditt program som en tjänst, utan att behöva oroa sig för att underhålla den infrastruktur som programmet distribueras på.

All fakturering för Azure-resurser som du använder i den prenumerationen, inklusive Azure AD Domain Services, debiteras tillbaka till dig. Du behåller full kontroll över relationen med kunden när det gäller försäljning, fakturering, teknisk support etc. Med flexibiliteten i Azure CSP-plattformen kan ett litet team av supportagenter serva många sådana kunder som har instanser av ditt program distribuerade.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP-distributionsmodeller för Azure AD-domäntjänster
Det finns två sätt att använda Azure AD Domain Services med en Azure CSP-prenumeration. Välj den rätta baserat på de säkerhets- och enkelhetsöverväganden dina kunder har.

### <a name="direct-deployment-model"></a>Direkt distributionsmodell
I den här distributionsmodellen är Azure AD Domain Services aktiverat i ett virtuellt nätverk som tillhör Azure CSP-prenumerationen. CSP-partnerns administratörsagenter har följande behörighet:
* Globala administratörsbehörighet i kundens Azure AD-katalog.
* Prenumerationsägarebehörighet för Azure CSP-prenumerationen.

![Direkt distributionsmodell](./media/csp/csp_direct_deployment_model.png)

I den här distributionsmodellen kan CSP-leverantörens administratörsagenter administrera identiteter för kunden. Dessa administratörsagenter har möjlighet att etablera nya användare, grupper, lägga till program i kundens Azure AD-katalog etc. Den här distributionsmodellen kan vara lämplig för mindre organisationer som inte har en dedikerad identitetsadministratör eller föredrar för CSP-partnern att administrera identiteter för deras räkning.


### <a name="peered-deployment-model"></a>Peered distributionsmodell
I den här distributionsmodellen är Azure AD Domain Services aktiverat i ett virtuellt nätverk som tillhör kunden - det vill ha en direkt Azure-prenumeration som betalas av kunden. CSP-partnern kan sedan distribuera program inom ett virtuellt nätverk som tillhör kundens CSP-prenumeration. De virtuella nätverken kan sedan anslutas med Azure-virtual network peering. Därför kan arbetsbelastningar/program som distribueras av CSP-partnern i Azure CSP-prenumerationen ansluta till kundens hanterade domän som etablerats i kundens direkta Azure-prenumeration.

![Peered distributionsmodell](./media/csp/csp_peered_deployment_model.png)

Den här distributionsmodellen ger en separation av privilegier och gör det möjligt för CSP-partnerns helpdesk-agenter att administrera Azure-prenumerationen och distribuera och hantera resurser inom den. CSP-partnerns helpdesk-agenter behöver dock inte ha globala administratörsbehörighet i kundens Azure AD-katalog. Kundens identitetsadministratörer kan fortsätta att hantera identiteter för sin organisation.

Den här distributionsmodellen kan vara anpassad till scenarier där en ISV (oberoende programvaruleverantör) tillhandahåller en värdversion av deras lokala program, som också måste ansluta till kundens AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrera Azure AD Domain Services hanterade domäner i CSP-prenumerationer
Följande viktiga överväganden gäller vid administrering av en hanterad domän i en Azure CSP-prenumeration:

* **CSP-administratörsagenter kan etablera en hanterad domän med hjälp av sina autentiseringsuppgifter:** Azure AD Domain Services stöder Azure CSP-prenumerationer. Därför kan användare som tillhör en CSP-partners administratörsagentgrupp etablera en ny Azure AD Domain Services-hanterad domän.

* **CSP:er kan skapa nya hanterade domäner för sina kunder med PowerShell:** Mer [information finns i hur du aktiverar Azure AD Domain Services med PowerShell.](powershell-create-instance.md)

* **CSP-administratörsagenter kan inte utföra pågående hanteringsuppgifter på den hanterade domänen med hjälp av sina autentiseringsuppgifter:** CSP-administratörsanvändare kan inte utföra rutinhanteringsuppgifter inom den hanterade domänen med hjälp av sina autentiseringsuppgifter. Dessa användare är externa till kundens Azure AD-katalog och deras autentiseringsuppgifter är inte tillgängliga i kundens Azure AD-katalog. Azure AD Domain Services har därför inte åtkomst till Kerberos- och NTLM-lösenordshasna för dessa användare. Därför kan sådana användare inte autentiseras på Azure AD Domain Services-hanterade domäner.

  > [!WARNING]
  > **Du måste skapa ett användarkonto i kundens katalog för att kunna utföra pågående administrationsuppgifter på den hanterade domänen.**
  > Du kan inte logga in på den hanterade domänen med hjälp av en CSP-administratörsanvändares autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett användarkonto som tillhör kundens Azure AD-katalog för att göra det. Du behöver dessa autentiseringsuppgifter för uppgifter som att ansluta virtuella datorer till den hanterade domänen, administrera DNS, administrera grupprincip etc.
  >

* **Användarkontot som skapats för pågående administration måste läggas till i gruppen AAD DC-administratörer:** Gruppen AAD DC-administratörer har behörighet att utföra vissa delegerade administrationsuppgifter på den hanterade domänen. Dessa uppgifter inkluderar att konfigurera DNS, skapa organisationsenheter, administrera grupprincip etc. För att en CSP-partner ska kunna utföra sådana uppgifter på en hanterad domän måste ett användarkonto skapas i kundens Azure AD-katalog. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partnerns administratörsagenter. Det här användarkontot måste också läggas till i gruppen AAD DC-administratörer för att aktivera konfigurationsuppgifter på den hanterade domänen som ska utföras med det här användarkontot.


## <a name="next-steps"></a>Nästa steg
* [Registrera dig i Azure CSP-programmet](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) och börja skapa företag via Azure CSP.
* Granska listan över [Azure-tjänster som är tillgängliga i Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Aktivera Azure AD Domain Services med PowerShell](powershell-create-instance.md)
* [Kom igång med Azure AD Domain Services](tutorial-create-instance.md)
