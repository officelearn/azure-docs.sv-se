---
title: Azure AD Domain Services för leverantörer av moln lösningar | Microsoft Docs
description: Azure Active Directory Domain Services för Azure Cloud solution providers.
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
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754430"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services för Azure Cloud solution providers (CSP)
I den här artikeln förklaras hur du kan använda Azure AD Domain Services i en Azure CSP-prenumeration.

## <a name="overview-of-azure-csp"></a>Översikt över Azure CSP
Azure CSP är ett program för Microsoft-partner och tillhandahåller en licens kanal för olika moln tjänster från Microsoft. Azure CSP gör det möjligt för partner att hantera försäljning, äga fakturerings relationen, ge teknisk support och fakturerings support och vara kundens enda kontakt punkt. Dessutom tillhandahåller Azure CSP en fullständig uppsättning verktyg, inklusive en självbetjänings Portal och medföljande API: er. Med dessa verktyg kan CSP-partner enkelt etablera och hantera Azure-resurser och tillhandahålla fakturering för kunder och deras prenumerationer.

[Partner Center-portalen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungerar som en start punkt för alla Azure CSP-partner. Den ger omfattande funktioner för kund hantering, automatisk bearbetning med mera. Azure CSP-partner kan använda Partner Center-funktioner med hjälp av ett webbaserat användar gränssnitt eller med hjälp av PowerShell och olika API-anrop.

Följande diagram illustrerar hur CSP-modellen fungerar på en hög nivå. Contoso har en Azure AD-Active Directory. De har ett partnerskap med en CSP, som distribuerar och hanterar resurser i sin Azure CSP-prenumeration. Contoso kan också ha vanliga (direkta) Azure-prenumerationer som faktureras direkt till contoso.

![Översikt över CSP-modellen](./media/csp/csp_model_overview.png)

CSP-partnerns klient organisation har tre särskilda agent grupper – administratörs agenter, support agenter och försäljnings agenter. Gruppen administratörs agenter tilldelas rollen klient organisations administratör i Contosos Azure AD-katalog. Därför har en användare som tillhör CSP-partnerns administratörs agenter gruppen klient organisations administratörs behörighet i Contosos Azure AD-katalog. När CSP-partnern etablerar en Azure CSP-prenumeration för Contoso tilldelas gruppen administratörs agenter rollen som ägare för den prenumerationen. Därför har CSP-partnerns administratörs agenter de behörigheter som krävs för att etablera Azure-resurser, till exempel virtuella datorer, virtuella nätverk och Azure AD Domain Services å uppdrag av contoso.

Mer information finns i [Översikt över Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Fördelar med att använda Azure AD Domain Services i en Azure CSP-prenumeration
Azure AD Domain Services tillhandahåller Windows Server AD-kompatibla tjänster i Azure, till exempel LDAP, Kerberos/NTLM-autentisering, domän anslutning, grup princip och DNS. Under årtionden har många program skapats för att fungera mot AD med hjälp av dessa funktioner. Många oberoende program varu leverantörer (ISV: er) har skapat och distribuerat program till sina kunders lokaler. Dessa program är betungande för att stödja eftersom det ofta krävs åtkomst till de olika miljöer där dessa program distribueras. Med Azure CSP-prenumerationer har du ett enklare alternativ med skalbarhet och flexibilitet i Azure.

Azure AD Domain Services stöder nu Azure CSP-prenumerationer. Du kan nu distribuera ditt program i en Azure CSP-prenumeration som är kopplad till kundens Azure AD-katalog. Det innebär att dina anställda (support personal) kan hantera, administrera och underhålla de virtuella datorer där ditt program distribueras med företagets företags uppgifter. Ytterligare kan du etablera en Azure AD Domain Services hanterad domän för kundens Azure AD-katalog. Ditt program är anslutet till kundens hanterade domän. Därför fungerar funktioner i ditt program som är beroende av Kerberos/NTLM, LDAP eller [system. DirectoryServices-API: et](/dotnet/api/system.directoryservices) sömlöst mot kundens katalog. Dina slutanvändare har nytta av att förbruka ditt program som en tjänst, utan att behöva oroa sig för att underhålla infrastrukturen som programmet distribueras på.

All fakturering för Azure-resurser som du förbrukar i den prenumerationen, inklusive Azure AD Domain Services, debiteras till dig. Du behåller full kontroll över relationen med kunden när den kommer till försäljning, fakturering, teknisk support osv. Med flexibiliteten i Azure CSP-plattformen kan en liten grupp support agenter betjäna många sådana kunder som har instanser av ditt program distribuerade.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP-distributions modeller för Azure AD Domain Services
Det finns två sätt att använda Azure AD Domain Services med en Azure CSP-prenumeration. Välj rätt baserat på säkerhets-och förenklings överväganden som dina kunder har.

### <a name="direct-deployment-model"></a>Direkt distributions modell
I den här distributions modellen är Azure AD Domain Services aktive rad i ett virtuellt nätverk som tillhör Azure CSP-prenumerationen. CSP-partnerns administratörs agenter har följande behörigheter:
* Global administratörs behörighet i kundens Azure AD-katalog.
* Prenumerations ägarens behörigheter för Azure CSP-prenumerationen.

![Direkt distributions modell](./media/csp/csp_direct_deployment_model.png)

I den här distributions modellen kan CSP-providerns administratörs agenter administrera identiteter för kunden. Dessa administratörs agenter kan etablera nya användare, grupper, lägga till program i kundens Azure AD-katalog osv. Den här distributions modellen kan vara lämplig för mindre organisationer som inte har en särskild identitets administratör eller som föredras för CSP-partnern för att administrera identiteter för deras räkning.


### <a name="peered-deployment-model"></a>Modell för peer-distribution
I den här distributions modellen är Azure AD Domain Services aktive rad i ett virtuellt nätverk som tillhör kunden, d.v.s. en direkt Azure-prenumeration som betalas av kunden. CSP-partnern kan sedan distribuera program i ett virtuellt nätverk som tillhör kundens CSP-prenumeration. De virtuella nätverken kan sedan anslutas med hjälp av Azure Virtual Network-peering. Det innebär att arbets belastningar/program som distribueras av CSP-partnern i Azure CSP-prenumerationen kan ansluta till kundens hanterade domän etablerade i kundens direkta Azure-prenumeration.

![Modell för peer-distribution](./media/csp/csp_peered_deployment_model.png)

Den här distributions modellen ger en separation av privilegier och gör det möjligt för CSP-partnerns helpdesk-agenter att administrera Azure-prenumerationen och distribuera och hantera resurser i den. CSP-partnerns helpdesk-agenter behöver dock inte ha global administratörs behörighet för kundens Azure AD-katalog. Kundens identitets administratörer kan fortsätta att hantera identiteter för organisationen.

Den här distributions modellen kan vara lämplig för scenarier där en ISV (oberoende program varu leverantör) tillhandahåller en värdbaserad version av sitt lokala program, som också måste ansluta till kundens AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrera Azure AD Domain Services hanterade domäner i CSP-prenumerationer
Följande viktiga överväganden gäller när du administrerar en hanterad domän i en Azure CSP-prenumeration:

* **CSP admin-agenter kan etablera en hanterad domän med sina autentiseringsuppgifter:** Azure AD Domain Services stöder Azure CSP-prenumerationer. Därför kan användare som tillhör en CSP-partners grupp med administratörs agenter etablera en ny Azure AD Domain Services hanterad domän.

* **Kryptografiproviders kan skapa skript för nya hanterade domäner för sina kunder med hjälp av PowerShell:** Mer information finns i [så här aktiverar du Azure AD Domain Services med hjälp av PowerShell](powershell-create-instance.md) .

* **CSP admin-agenter kan inte utföra pågående hanterings uppgifter på den hanterade domänen med sina autentiseringsuppgifter:** Användare av CSP-administratörer kan inte utföra rutin hanterings uppgifter i den hanterade domänen med sina autentiseringsuppgifter. Dessa användare är externa i kundens Azure AD-katalog och deras autentiseringsuppgifter är inte tillgängliga i kundens Azure AD-katalog. Därför har Azure AD Domain Services inte åtkomst till Kerberos-och NTLM-hashvärden för dessa användare. Därför kan sådana användare inte autentiseras på Azure AD Domain Services hanterade domäner.

  > [!WARNING]
  > **Du måste skapa ett användar konto i kundens katalog för att utföra pågående administrations uppgifter på den hanterade domänen.**
  > Du kan inte logga in på den hanterade domänen med hjälp av en CSP admin-användares autentiseringsuppgifter. Använd autentiseringsuppgifterna för ett användar konto som tillhör kundens Azure AD-katalog. Du behöver dessa autentiseringsuppgifter för uppgifter som att ansluta virtuella datorer till den hanterade domänen, administrera DNS, administrera grupprincip osv.
  >

* **Användar kontot som skapas för pågående administration måste läggas till i gruppen "AAD DC-administratörer":** Gruppen AAD DC-administratörer har behörighet att utföra vissa delegerade administrations uppgifter på den hanterade domänen. De här uppgifterna omfattar konfigurering av DNS, skapande av organisationsenheter, administration av grup princip osv. För att en CSP-partner ska kunna utföra sådana uppgifter på en hanterad domän måste du skapa ett användar konto i kundens Azure AD-katalog. Autentiseringsuppgifterna för det här kontot måste delas med CSP-partnerns administratörs agenter. Dessutom måste det här användar kontot läggas till i gruppen "AAD DC-administratörer" för att aktivera konfigurations åtgärder på den hanterade domänen som ska utföras med det här användar kontot.


## <a name="next-steps"></a>Nästa steg
* [Registrera dig för Azure CSP-programmet](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) och börja skapa företag via Azure CSP.
* Granska listan över [Azure-tjänster som är tillgängliga i Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Aktivera Azure AD Domain Services med PowerShell](powershell-create-instance.md)
* [Kom igång med Azure AD Domain Services](tutorial-create-instance.md)
