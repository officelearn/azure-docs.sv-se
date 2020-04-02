---
title: Azure AD-domäntjänster för molnlösningsleverantörer | Microsoft-dokument
description: Lär dig hur du aktiverar och hanterar hanterade Azure Active Directory Domain Services-domäner för Azure Cloud Solution Providers
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519092"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Distribution och hantering av Azure Active Directory Domain Services för Azure Cloud Solution Providers

Azure Cloud Solution Providers (CSP) är ett program för Microsoft Partners och tillhandahåller en licenskanal för olika Microsoft-molntjänster. Azure CSP gör det möjligt för partner att hantera försäljning, äga faktureringsrelationen, tillhandahålla teknisk support och faktureringssupport och vara kundens enda kontaktpunkt. Dessutom innehåller Azure CSP en fullständig uppsättning verktyg, inklusive en självbetjäningsportal och medföljande API:er. Dessa verktyg gör det möjligt för CSP-partner att enkelt etablera och hantera Azure-resurser och tillhandahålla fakturering för kunder och deras prenumerationer.

[Partner center-portalen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) är startpunkten för alla Azure CSP-partner och ger omfattande kundhanteringsfunktioner, automatiserad bearbetning med mera. Azure CSP-partner kan använda Partner Center-funktioner med hjälp av ett webbaserat användargränssnitt eller genom att använda PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på en hög nivå. Här har Contoso en Azure Active Directory -klientorganisation (Azure AD). De har ett partnerskap med en CSP som distribuerar och hanterar resurser i sin Azure CSP-prenumeration. Contoso kan också ha vanliga (direkta) Azure-prenumerationer, som faktureras direkt till Contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partnerns klient har tre specialagentgrupper - *administratörsagenter,* *Helpdesk-agenter* och *försäljningsagenter.*

Gruppen *Administratörsagenter* tilldelas rollen klientadministratör i Contosos Azure AD-klientorganisation. Därför har en användare som tillhör CSP-partnerns administratörsagentgrupp behörigheter för klientadministratörer i Contosos Azure AD-klientorganisation.

När CSP-partnern etablerar en Azure CSP-prenumeration för Contoso tilldelas deras administratörsagentgrupp ägarrollen för den prenumerationen. Därför har CSP-partnerns administratörsagenter de behörigheter som krävs för att etablera Azure-resurser som virtuella datorer, virtuella nätverk och Azure AD Domain Services på uppdrag av Contoso.

Mer information finns i [Översikt över Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Fördelar med att använda Azure AD DS i en Azure CSP-prenumeration

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Windows Server Active Directory Domain Services. Under årtiondena har många program byggts för att fungera mot AD med hjälp av dessa funktioner. Många oberoende programvaruleverantörer (ISV) har byggt och distribuerat program i sina kunders lokaler. Dessa program är svåra att stödja eftersom du ofta behöver åtkomst till de olika miljöer där programmen distribueras. Med Azure CSP-prenumerationer har du ett enklare alternativ med Azures skala och flexibilitet.

Azure AD DS stöder Azure CSP-prenumerationer. Du kan distribuera ditt program i en Azure CSP-prenumeration som är knuten till kundens Azure AD-klientorganisation. Därför kan dina anställda (supportpersonal) hantera, administrera och betjäna de virtuella datorer som programmet distribueras på med hjälp av organisationens företagsautentiseringsuppgifter.

Du kan också distribuera en Azure AD DS-hanterad domän i kundens Azure AD-klientorganisation. Ditt program är sedan anslutet till kundens hanterade domän. Funktioner i ditt program som är beroende av Kerberos / NTLM, LDAP eller [System.DirectoryServices API](/dotnet/api/system.directoryservices) fungerar sömlöst mot kundens domän. Slutkunder drar nytta av att konsumera ditt program som en tjänst, utan att behöva oroa sig för att underhålla infrastrukturen som programmet distribueras på.

All fakturering för Azure-resurser som du använder i den prenumerationen, inklusive Azure AD DS, debiteras tillbaka till dig. Du behåller full kontroll över relationen med kunden när det gäller försäljning, fakturering, teknisk support etc. Med flexibiliteten i Azure CSP-plattformen kan ett litet team av supportagenter serva många sådana kunder som har instanser av ditt program distribuerade.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>CSP-distributionsmodeller för Azure AD DS

Det finns två sätt att använda Azure AD DS med en Azure CSP-prenumeration. Välj den rätta baserat på de säkerhets- och enkelhetsöverväganden dina kunder har.

### <a name="direct-deployment-model"></a>Direkt distributionsmodell

I den här distributionsmodellen är Azure AD DS aktiverat i ett virtuellt nätverk som tillhör Azure CSP-prenumerationen. CSP-partnerns administratörsagenter har följande behörighet:

* *Globala* administratörsbehörighet i kundens Azure AD-klientorganisation.
* *Prenumerationsägarebehörighet* för Azure CSP-prenumerationen.

![Direkt distributionsmodell](./media/csp/csp_direct_deployment_model.png)

I den här distributionsmodellen kan CSP-leverantörens administratörsagenter administrera identiteter för kunden. Dessa administratörsagenter kan utföra uppgifter som att etablera nya användare eller grupper, eller lägga till program i kundens Azure AD-klientorganisation.

Den här distributionsmodellen kan vara lämplig för mindre organisationer som inte har en dedikerad identitetsadministratör eller föredrar för CSP-partnern att administrera identiteter för deras räkning.

### <a name="peered-deployment-model"></a>Peered distributionsmodell

I den här distributionsmodellen är Azure AD DS aktiverat i ett virtuellt nätverk som tillhör kunden - en direkt Azure-prenumeration som betalas av kunden. CSP-partnern kan distribuera program inom ett virtuellt nätverk som tillhör kundens CSP-prenumeration. De virtuella nätverken kan sedan anslutas med Azure-virtual network peering.

Med den här distributionen kan arbetsbelastningar eller program som distribueras av CSP-partnern i Azure CSP-prenumerationen ansluta till kundens hanterade domän som etablerats i kundens direkta Azure-prenumeration.

![Peered distributionsmodell](./media/csp/csp_peered_deployment_model.png)

Den här distributionsmodellen ger en separation av privilegier och gör det möjligt för CSP-partnerns helpdesk-agenter att administrera Azure-prenumerationen och distribuera och hantera resurser inom den. CSP-partnerns helpdesk-agenter behöver dock inte ha globala administratörsbehörighet i kundens Azure AD-katalog. Kundens identitetsadministratörer kan fortsätta att hantera identiteter för sin organisation.

Den här distributionsmodellen kan vara anpassad till scenarier där en ISV tillhandahåller en värdversion av deras lokala program, som också måste ansluta till kundens Azure AD.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrera Azure AD DS i CSP-prenumerationer

Följande viktiga överväganden gäller vid administrering av en hanterad domän i en Azure CSP-prenumeration:

* **CSP-administratörsagenter kan etablera en hanterad domän med hjälp av sina autentiseringsuppgifter:** Azure AD DS stöder Azure CSP-prenumerationer. Användare som tillhör en CSP-partners administratörsagentgrupp kan etablera en ny Azure AD DS-hanterad domän.

* **CSP:er kan skapa nya hanterade domäner för sina kunder med PowerShell:** Mer [information finns i hur du aktiverar Azure AD DS med PowerShell.](powershell-create-instance.md)

* **CSP-administratörsagenter kan inte utföra pågående hanteringsuppgifter på den hanterade domänen med hjälp av sina autentiseringsuppgifter:** CSP-administratörsanvändare kan inte utföra rutinhanteringsuppgifter inom den hanterade domänen med hjälp av sina autentiseringsuppgifter. Dessa användare är externa för kundens Azure AD-klientorganisation och deras autentiseringsuppgifter är inte tillgängliga i kundens Azure AD-klientorganisation. Azure AD DS har inte åtkomst till Kerberos- och NTLM-lösenordshasna för dessa användare, så användare kan inte autentiseras på Azure AD DS-hanterade domäner.

  > [!WARNING]
  > Du måste skapa ett användarkonto i kundens katalog för att kunna utföra pågående administrationsuppgifter på den hanterade domänen.
  >
  > Du kan inte logga in på den hanterade domänen med hjälp av en CSP-administratörsanvändares autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett användarkonto som tillhör kundens Azure AD-klient för att göra det. Du behöver dessa autentiseringsuppgifter för uppgifter som att ansluta virtuella datorer till den hanterade domänen, administrera DNS eller administrera grupprincip.

* **Användarkontot som skapas för pågående administration måste läggas till i gruppen *AAD DC-administratörer:* ** Gruppen *AAD DC-administratörer* har behörighet att utföra vissa delegerade administrationsuppgifter på den hanterade domänen. Dessa uppgifter omfattar att konfigurera DNS, skapa organisationsenheter och administrera grupprincip.
    
    För att en CSP-partner ska kunna utföra dessa uppgifter på en hanterad domän måste ett användarkonto skapas inom kundens Azure AD-klientorganisation. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partnerns administratörsagenter. Det här användarkontot måste också läggas till i gruppen *AAD DC-administratörer* för att aktivera konfigurationsuppgifter på den hanterade domänen som ska utföras med det här användarkontot.

## <a name="next-steps"></a>Nästa steg

För att komma [igång, registrera dig i Azure CSP-programmet](/partner-center/enrolling-in-the-csp-program). Du kan sedan aktivera Azure AD Domain Services med [Azure-portalen](tutorial-create-instance.md) eller [Azure PowerShell](powershell-create-instance.md).
