---
title: Översikt över Azure Active Directory Domain Services | Microsoft Docs
description: I den här översikten får du veta vad Azure Active Directory Domain Services tillhandahåller och hur du använder det i din organisation för att tillhandahålla identitets tjänster till program och tjänster i molnet.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 3d4ac9ed9b8d5162f820f7e981d3380b95cf41e4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172822"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Vad är Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering som är helt kompatibel med Windows Server Active Katalogen. Du använder dessa domän tjänster utan att behöva distribuera, hantera och korrigera domänkontrollanter i molnet. Azure AD DS integreras med din befintliga Azure AD-klient, vilket gör det möjligt för användarna att logga in med sina befintliga autentiseringsuppgifter. Du kan också använda befintliga grupper och användar konton för att skydda åtkomsten till resurser, vilket ger en smidigare ökning och växling av lokala resurser till Azure.

Azure AD DS replikerar identitets information från Azure AD, så att den fungerar med Azure AD-klienter som endast är enbart i molnet eller som synkroniseras med en lokal Active Directory Domain Services (AD DS)-miljö. Samma uppsättning Azure AD DS-funktioner finns i båda miljöerna.

* Om du har en befintlig lokal AD DS-miljö kan du synkronisera användar konto information för att ge användarna en enhetlig identitet.
* I miljöer med enbart moln behöver du inte en traditionell lokal AD DS-miljö för att använda de centraliserade identitets tjänsterna för Azure AD DS.

Följande videoklipp ger en översikt över hur Azure AD DS integreras med dina program och arbets belastningar för att tillhandahålla identitets tjänster i molnet:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Vanliga sätt att tillhandahålla identitets lösningar i molnet

När du migrerar befintliga arbets belastningar till molnet kan katalog medveten program använda LDAP för Läs-eller skriv åtkomst till en lokal AD DS-katalog. Program som körs på Windows Server distribueras vanligt vis på domänanslutna virtuella datorer (VM) så att de kan hanteras på ett säkert sätt med hjälp av grupprincip. För att användarna ska kunna autentisera slutanvändare kan programmen också vara beroende av Windows-integrerad autentisering, t. ex. Kerberos eller NTLM-autentisering.

IT-administratörer använder ofta en av följande lösningar för att tillhandahålla en identitets tjänst till program som körs i Azure:

* Konfigurera en plats-till-plats-VPN-anslutning mellan arbets belastningar som körs i Azure och den lokala AD DS-miljön.
* Skapa replik-domänkontrollanter med Azure Virtual Machines (VM) för att utöka AD DS-domänen/-skogen.
* Distribuera en fristående AD DS-miljö i Azure med hjälp av domänkontrollanter som körs på virtuella Azure-datorer.

Med dessa metoder kan VPN-anslutningar till den lokala katalogen göra program sårbara för tillfälliga nätverks fel eller avbrott. Om du distribuerar domänkontrollanter med virtuella datorer i Azure måste de virtuella datorerna hantera, säkra, korrigera, övervaka, säkerhetskopiera och felsöka dem.

Azure AD DS innehåller alternativ för behovet av att skapa VPN-anslutningar tillbaka till en lokal AD DS-miljö eller köra och hantera virtuella datorer i Azure för att tillhandahålla identitets tjänster. Som en hanterad tjänst minskar Azure AD DS komplexiteten för att skapa en integrerad identitets lösning för både hybrid miljöer och moln miljöer.

## <a name="azure-ad-ds-features-and-benefits"></a>Funktioner och fördelar i Azure AD DS

För att tillhandahålla identitets tjänster för program och virtuella datorer i molnet är Azure AD DS helt kompatibelt med en traditionell AD DS-miljö för åtgärder som domän anslutning, säker LDAP (LDAPs), grupprincip och DNS-hantering samt LDAP-bindning och Läs stöd. Stöd för LDAP-skrivning är tillgängligt för objekt som skapats i den hanterade Azure AD DS-domänen, men inte resurser som synkroniseras från Azure AD. Följande funktioner i Azure AD DS fören klar distributions-och hanterings åtgärder:

* **Förenklad distributions upplevelse:** Azure AD DS har Aktiver ATS för din Azure AD-klient med hjälp av en enda guide i Azure Portal.
* **Integrerat med Azure AD:** Användar konton, grupp medlemskap och autentiseringsuppgifter är automatiskt tillgängliga från Azure AD-klienten. Nya användare, grupper eller ändringar av attribut från din Azure AD-klient eller din lokala AD DS-miljö synkroniseras automatiskt till Azure AD DS.
    * Konton i externa kataloger som är länkade till Azure AD är inte tillgängliga i Azure AD DS. Autentiseringsuppgifterna är inte tillgängliga för dessa externa kataloger och kan därför inte synkroniseras till en hanterad Azure AD DS-domän.
* **Använd företagets autentiseringsuppgifter/lösen ord:** Lösen ord för användare i Azure AD-klienten är desamma i Azure AD DS. Användarna kan använda sina företags uppgifter för att ansluta till domän datorer, logga in interaktivt eller via fjärr skrivbord och autentisera mot den hanterade domänen i Azure AD DS.
* **NTLM-och Kerberos-autentisering:** Med stöd för NTLM-och Kerberos-autentisering kan du distribuera program som förlitar sig på Windows-integrerad autentisering.
* **Hög tillgänglighet:** Azure AD DS innehåller flera domänkontrollanter som ger hög tillgänglighet för din hanterade domän. Den här hög tillgängligheten garanterar tjänste drift tid och återhämtning till problem.
    * I regioner som stöder [Azure-tillgänglighetszoner][availability-zones]distribueras även dessa domänkontrollanter i zoner för ytterligare återhämtning. 

Några viktiga aspekter av en hanterad Azure AD DS-domän är följande:

* Den hanterade domänen i Azure AD DS är en fristående domän. Den är inte en utökning av en lokal domän.
* IT-teamet behöver inte hantera, korrigera eller övervaka domänkontrollanter för den här hanterade domänen i Azure AD DS.

För Hybrid miljöer som kör AD DS lokalt behöver du inte hantera AD-replikering till den hanterade Azure AD DS-domänen. Användar konton, grupp medlemskap och autentiseringsuppgifter från din lokala katalog synkroniseras med Azure AD via [Azure AD Connect][azure-ad-connect]. Dessa användar konton, grupp medlemskap och autentiseringsuppgifter är automatiskt tillgängliga i den hanterade domänen i Azure AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Hur fungerar Azure AD DS?

För att tillhandahålla identitets tjänster skapar Azure en AD DS-instans i ett virtuellt nätverk som du väljer. I bakgrunden, och utan att du behöver hantera, skydda eller uppdatera, tillhandahålls redundans genom ett par Windows Server-domänkontrollanter.

Den hanterade domänen i Azure AD DS har kon figurer ATS för att utföra en enkelriktad synkronisering från Azure AD för att ge åtkomst till en central uppsättning användare, grupper och autentiseringsuppgifter. Du kan skapa resurser direkt i den hanterade domänen i Azure AD DS, men de synkroniseras inte tillbaka till Azure AD. Program, tjänster och virtuella datorer i Azure som ansluter till det här virtuella nätverket kan sedan använda vanliga AD DS-funktioner som domän anslutning, grup princip, LDAP och Kerberos/NTLM-autentisering.

I en hybrid miljö med en lokal AD DS-miljö synkroniserar [Azure AD Connect][azure-ad-connect] identitets information med Azure AD.

![Synkronisering i Azure AD Domain Services med Azure AD och lokala Active Directory Domain Services med AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Låt oss titta på några exempel för att se hur Azure AD DS fungerar:

* [Azure AD DS för Hybrid organisationer](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS för endast moln organisationer](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS för Hybrid organisationer

Många organisationer kör en hybrid infrastruktur som omfattar både moln-och lokala program arbets belastningar. Äldre program som migrerats till Azure som en del av en hiss och Shift-strategi kan använda traditionella LDAP-anslutningar för att tillhandahålla identitets information. För att stödja den här hybrid infrastrukturen kan identitets information från en lokal AD DS-miljö synkroniseras med en Azure AD-klient. Azure AD DS tillhandahåller sedan dessa äldre program i Azure med en identitets källa, utan att du behöver konfigurera och hantera program anslutningen tillbaka till lokala katalog tjänster.

Nu ska vi titta på ett exempel för Litware ' Corporation, en hybrid organisation som kör både lokala och Azure-resurser:

![Azure Active Directory Domain Services för en hybrid organisation som innehåller lokal synkronisering](./media/overview/synced-tenant.png)

* Program-och Server arbets belastningar som kräver domän tjänster distribueras i ett virtuellt nätverk i Azure.
    * Detta kan inkludera äldre program som migrerats till Azure som en del av en hiss-och Shift-strategi.
* För att synkronisera identitets information från sin lokala katalog till sin Azure AD-klient distribuerar Litware ' Corporation [Azure AD Connect][azure-ad-connect].
    * Identitets information som synkroniseras innehåller användar konton och grupp medlemskap.
* Litware ' IT-teamet möjliggör Azure AD DS för sin Azure AD-klient i detta eller ett peer-kopplat virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domän anslutning, LDAP Read, LDAP-bindning, NTLM-och Kerberos-autentisering och grupprincip.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS för endast moln organisationer

En molnbaserad Azure AD-klient har ingen lokal identitets källa. Användar konton och grupp medlemskap skapas till exempel och hanteras direkt i Azure AD.

Nu ska vi titta på ett exempel för contoso, en moln organisation som bara använder Azure AD för identitet. Alla användar identiteter, deras autentiseringsuppgifter och grupp medlemskap skapas och hanteras i Azure AD. Det finns ingen ytterligare konfiguration av Azure AD Connect för att synkronisera identitets information från en lokal katalog.

![Azure Active Directory Domain Services för en organisation som endast är i molnet utan lokal synkronisering](./media/overview/cloud-only-tenant.png)

* Program-och Server arbets belastningar som kräver domän tjänster distribueras i ett virtuellt nätverk i Azure.
* Contosos IT-team möjliggör Azure AD DS för sin Azure AD-klient i detta eller ett peer-kopplat virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domän anslutning, LDAP Read, LDAP-bindning, NTLM-och Kerberos-autentisering och grupprincip.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD DS jämför med andra identitets lösningar och hur synkronisering fungerar finns i följande artiklar:

* [Jämför Azure AD DS med Azure AD, Active Directory Domain Services på virtuella Azure-datorer och Active Directory Domain Services lokalt][compare]
* [Lär dig hur Azure AD Domain Services synkroniseras med din Azure AD-katalog][synchronization]

Kom igång genom att [skapa en Azure AD DS-hanterad domän med hjälp av Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
