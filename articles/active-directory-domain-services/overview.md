---
title: Översikt över Azure Active Directory Domain Services | Microsoft-dokument
description: I den här översikten kan du läsa vad Azure Active Directory Domain Services tillhandahåller och hur du använder dem i organisationen för att tillhandahålla identitetstjänster till program och tjänster i molnet.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: ea0fa0e9d4e475a8496d1ee52b4cdfea11a13d8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544127"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Vad är Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP (Lightweight Directory Access Protocol) och Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Du använder dessa domäntjänster utan att behöva distribuera, hantera och korrigera domänkontrollanter i molnet. Azure AD DS integreras med din befintliga Azure AD-klientorganisation, vilket gör det möjligt för användare att logga in med sina befintliga autentiseringsuppgifter. Du kan också använda befintliga grupper och användarkonton för att skydda åtkomst till resurser, vilket ger en smidigare lyft-och-förskjutning av lokala resurser till Azure.

Azure AD DS replikerar identitetsinformation från Azure AD, så fungerar med Azure AD-klienter som är molnfria eller synkroniserade med en lokal AD DS-miljö (Active Directory Domain Services). Samma uppsättning Azure AD DS-funktioner finns för båda miljöerna.

* Om du har en befintlig lokal AD DS-miljö kan du synkronisera användarkontoinformation för att ge användarna en konsekvent identitet.
* För molnmiljöer behöver du inte en traditionell lokal AD DS-miljö för att använda de centraliserade identitetstjänsterna i Azure AD DS.

Följande video ger en översikt över hur Azure AD DS integreras med dina program och arbetsbelastningar för att tillhandahålla identitetstjänster i molnet:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Vanliga sätt att tillhandahålla identitetslösningar i molnet

När du migrerar befintliga arbetsbelastningar till molnet kan katalogmedvetna program använda LDAP för läs- eller skrivåtkomst till en lokal AD DS-katalog. Program som körs på Windows Server distribueras vanligtvis på domänanslutna virtuella datorer så att de kan hanteras på ett säkert sätt med hjälp av grupprinciper. Om du vill autentisera slutanvändare kan programmen också förlita sig på Windows-integrerad autentisering, till exempel Kerberos- eller NTLM-autentisering.

IT-administratörer använder ofta någon av följande lösningar för att tillhandahålla en identitetstjänst till program som körs i Azure:

* Konfigurera en plats-till-plats-VPN-anslutning mellan arbetsbelastningar som körs i Azure och en lokal AD DS-miljö.
    * De lokala domänkontrollanterna tillhandahåller sedan autentisering via VPN-anslutningen.
* Skapa replikdomänkontrollanter med hjälp av virtuella Azure-datorer (VMs) för att utöka AD DS-domänen/skogen från lokala.
    * Domänkontrollanterna som körs på virtuella Azure-datorer tillhandahåller autentisering och replikerar kataloginformation mellan den lokala AD DS-miljön.
* Distribuera en fristående AD DS-miljö i Azure med domänkontrollanter som körs på virtuella Azure-datorer.
    * Domänkontrollanterna som körs på virtuella Azure-datorer tillhandahåller autentisering, men det finns ingen kataloginformation som replikeras från en lokal AD DS-miljö.

Med dessa metoder gör VPN-anslutningar till den lokala katalogen program sårbara för tillfälliga nätverksfel eller avbrott. Om du distribuerar domänkontrollanter med virtuella datorer i Azure måste IT-teamet hantera de virtuella datorerna och sedan skydda, korrigera, övervaka, säkerhetskopiera och felsöka dem.

Azure AD DS erbjuder alternativ till behovet av att skapa VPN-anslutningar tillbaka till en lokal AD DS-miljö eller köra och hantera virtuella datorer i Azure för att tillhandahålla identitetstjänster. Som en hanterad tjänst minskar Azure AD DS komplexiteten för att skapa en integrerad identitetslösning för både hybrid- och molnmiljöer.

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS-funktioner och fördelar

För att tillhandahålla identitetstjänster till program och virtuella datorer i molnet är Azure AD DS fullt kompatibel med en traditionell AD DS-miljö för åtgärder som domänanslutning, säker LDAP (LDAPS), grupprincip- och DNS-hantering och LDAP-bindning och lässupport. LDAP-skrivstöd är tillgängligt för objekt som skapats i Azure AD DS-hanterad domän, men inte resurser som synkroniseras från Azure AD. Följande funktioner i Azure AD DS förenklar distributions- och hanteringsåtgärder:

* **Förenklad distributionsupplevelse:** Azure AD DS är aktiverat för din Azure AD-klient med hjälp av en enda guide i Azure-portalen.
* **Integrerad med Azure AD:** Användarkonton, gruppmedlemskap och autentiseringsuppgifter är automatiskt tillgängliga från din Azure AD-klientorganisation. Nya användare, grupper eller ändringar av attribut från din Azure AD-klientorganisation eller din lokala AD DS-miljö synkroniseras automatiskt till Azure AD DS.
    * Konton i externa kataloger som är länkade till din Azure AD är inte tillgängliga i Azure AD DS. Autentiseringsuppgifter är inte tillgängliga för dessa externa kataloger, så kan inte synkroniseras till en Azure AD DS-hanterad domän.
* **Använd företagets autentiseringsuppgifter/lösenord:** Lösenord för användare i Azure AD DS är desamma som i din Azure AD-klientorganisation. Användare kan använda sina företagsautentiseringsuppgifter för domänanslutningsdatorer, logga in interaktivt eller över fjärrskrivbord och autentisera mot azure AD DS-hanterad domän.
* **NTLM- och Kerberos-autentisering:** Med stöd för NTLM- och Kerberos-autentisering kan du distribuera program som är beroende av Windows-integrerad autentisering.
* **Hög tillgänglighet:** Azure AD DS innehåller flera domänkontrollanter som ger hög tillgänglighet för din hanterade domän. Den här hög tillgängligheten garanterar drifttid för tjänsten och motståndskraft mot fel.
    * I regioner som stöder [Azure-tillgänglighetszoner][availability-zones]distribueras även dessa domänkontrollanter mellan zoner för ytterligare återhämtning.

Några viktiga aspekter av en Azure AD DS-hanterad domän är följande:

* Den Hanterade Azure AD DS-domänen är en fristående domän. Det är inte en förlängning av en lokal domän.
    * Om det behövs kan du skapa envägs utgående skogsförtroende från Azure AD DS till en lokal AD DS-miljö. Mer information finns i [Begrepp och funktioner för Resursskog för Azure AD DS][ forest-trusts].
* IT-teamet behöver inte hantera, korrigera eller övervaka domänkontrollanter för den här Azure AD DS-hanterade domänen.

För hybridmiljöer som kör AD DS lokalt behöver du inte hantera AD-replikering till azure AD DS-hanterad domän. Användarkonton, gruppmedlemskap och autentiseringsuppgifter från din lokala katalog synkroniseras till Azure AD via [Azure AD Connect][azure-ad-connect]. Dessa användarkonton, gruppmedlemskap och autentiseringsuppgifter är automatiskt tillgängliga i azure AD DS-hanterade domänen.

## <a name="how-does-azure-ad-ds-work"></a>Hur fungerar Azure AD DS?

För att tillhandahålla identitetstjänster skapar Azure en AD DS-instans i ett virtuellt nätverk som du väljer. Bakom kulisserna skapas ett par Windows Server-domänkontrollanter som körs på virtuella Azure-datorer. Du behöver inte hantera, konfigurera eller uppdatera dessa domänkontrollanter. Azure-plattformen hanterar domänkontrollanterna som en del av Azure AD DS-tjänsten.

Den Hanterade Azure AD DS-domänen är konfigurerad för att utföra en enkelriktad synkronisering från Azure AD för att ge åtkomst till en central uppsättning användare, grupper och autentiseringsuppgifter. Du kan skapa resurser direkt i Azure AD DS-hanterad domän, men de synkroniseras inte tillbaka till Azure AD. Program, tjänster och virtuella datorer i Azure som ansluter till det här virtuella nätverket kan sedan använda vanliga AD DS-funktioner som domänkoppling, grupprincip, LDAP- och Kerberos/NTLM-autentisering.

I en hybridmiljö med en lokal AD DS-miljö synkroniserar [Azure AD Connect][azure-ad-connect] identitetsinformation med Azure AD, som sedan synkroniseras med Azure AD DS.

![Synkronisering i Azure AD-domäntjänster med Azure AD och lokala Active Directory Domain Services med AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Om du vill se Azure AD DS i aktion ska vi titta på ett par exempel:

* [Azure AD DS för hybridorganisationer](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS för molnbaserade organisationer](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS för hybridorganisationer

Många organisationer kör en hybridinfrastruktur som innehåller både moln- och lokala programarbetsbelastningar. Äldre program som migreras till Azure som en del av en lyft- och skiftstrategi kan använda traditionella LDAP-anslutningar för att tillhandahålla identitetsinformation. För att stödja den här hybridinfrastrukturen kan identitetsinformation från en lokal AD DS-miljö synkroniseras med en Azure AD-klientorganisation. Azure AD DS tillhandahåller sedan dessa äldre program i Azure med en identitetskälla, utan att behöva konfigurera och hantera programanslutning tillbaka till lokala katalogtjänster.

Låt oss titta på ett exempel för Litware Corporation, en hybridorganisation som kör både lokala och Azure-resurser:

![Azure Active Directory Domain Services för en hybridorganisation som innehåller lokal synkronisering](./media/overview/synced-tenant.png)

* Program och serverarbetsbelastningar som kräver domäntjänster distribueras i ett virtuellt nätverk i Azure.
    * Detta kan inkludera äldre program som migrerats till Azure som en del av en lyft- och skiftstrategi.
* Om du vill synkronisera identitetsinformation från den lokala katalogen till deras Azure AD-klient distribuerar Litware Corporation [Azure AD Connect][azure-ad-connect].
    * Identitetsinformation som synkroniseras omfattar användarkonton och gruppmedlemskap.
* Litwares IT-team aktiverar Azure AD DS för sin Azure AD-klientorganisation i det här, eller ett peered, virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domänanslutning, LDAP-läsning, LDAP-bindning, NTLM- och Kerberos-autentisering och Grupprincip.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det stöds inte för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS för molnbaserade organisationer

En Azure AD-klient för endast molnet har ingen lokal identitetskälla. Användarkonton och gruppmedlemskap skapas och hanteras till exempel direkt i Azure AD.

Nu ska vi titta på ett exempel för Contoso, en moln-bara organisation som använder Azure AD för identitet. Alla användaridentiteter, deras autentiseringsuppgifter och gruppmedlemskap skapas och hanteras i Azure AD. Det finns ingen ytterligare konfiguration av Azure AD Connect för att synkronisera all identitetsinformation från en lokal katalog.

![Azure Active Directory Domain Services för en molnbaserad organisation utan lokal synkronisering](./media/overview/cloud-only-tenant.png)

* Program och serverarbetsbelastningar som kräver domäntjänster distribueras i ett virtuellt nätverk i Azure.
* Contosos IT-team aktiverar Azure AD DS för sin Azure AD-klientorganisation i det här, eller ett peered, virtuellt nätverk.
* Program och virtuella datorer som distribueras i det virtuella Azure-nätverket kan sedan använda Azure AD DS-funktioner som domänanslutning, LDAP-läsning, LDAP-bindning, NTLM- och Kerberos-autentisering och Grupprincip.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD DS-jämförelse med andra identitetslösningar och hur synkronisering fungerar finns i följande artiklar:

* [Jämföra Azure AD DS med Azure AD, Active Directory Domain Services på virtuella Azure-datorer och Active Directory Domain Services lokalt][compare]
* [Lär dig hur Azure AD Domain Services synkroniseras med din Azure AD-katalog][synchronization]

För att komma [igång, skapa en Azure AD DS-hanterad domän med Azure-portalen][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
