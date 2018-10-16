---
title: Välj en lösning med Azure hybrid-identitet | Microsoft Docs
description: Få en grundläggande förståelse för de tillgängliga hybrididentitetslösningar och rekommendationer för dig att fatta de bästa identitet styrning beslutet för din organisation.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 15a28cd5937be103aac888a5fc5485e39854a1b4
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319956"
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft hybrid identity-lösningar

[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybrididentitetslösningar gör det möjligt att synkronisera lokala katalogobjekt med Azure AD medan du fortfarande hanterar användare på plats. Det första beslutet att fatta när du planerar att synkronisera dina lokala Windows Server Active Directory med Azure AD är om du vill använda synkroniseras identitet eller federerad identitet. Synkroniserade identiteter och eventuellt hashvärden för lösenord, aktivera slutanvändarna ska använda samma lösenord för åtkomst till både lokala och molnbaserade organisationens resurser. För mer avancerade krav, till exempel single-sign-on (SSO) eller lokala MFA, måste du distribuera Active Directory Federation Services (AD FS) för att federera identiteter. 

Det finns flera alternativ för att konfigurera hybrid-identitet. Den här artikeln innehåller information som hjälper dig att välja den bästa för din organisation baserat på enkel distribution och måste din specifika identitets- och åtkomsthantering. När du planerar vilka identitetsmodellen som bäst passar organisationens behov, måste du också tänka på tid, befintlig infrastruktur, komplexiteten och kostnaden. Dessa faktorer är olika för varje organisation och kan ändras över tid. Men om ändrar dina krav kan har du även flexibiliteten att växla till en annan identitet modell.

> [!TIP]
> Dessa lösningar levereras av [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="synchronized-identity"></a>Synkroniserade identiteter 

Synkroniserade identiteter är det enklaste sättet att synkronisera lokala katalogobjekt (användare och grupper) med Azure AD. 

![Synkroniserade hybrididentitet](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Synkroniserade identiteter är den enklaste och snabbaste metoden, behöver användarna underhålla separata lösenord för molnbaserade resurser. Om du vill undvika detta kan du också (valfritt) [synkronisera en hash av användarlösenord](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) till Azure AD-katalogen. Synkronisering av lösenords-hash kan användare logga in på molnbaserade organisationens resurser med samma användarnamn och lösenord som de använder lokalt. Azure AD Connect kontrollerar regelbundet din lokala katalog för ändringar och ser Azure AD-katalogen synkroniseras. När en användarattribut eller ett lösenord är ändrade lokala Active Directory, uppdateras den automatiskt i Azure AD. 

För de flesta organisationer som bara behöver användare få möjlighet att logga in på Office 365, SaaS-program och andra Azure AD-baserade resurser är rekommenderas standardalternativet för synkronisering av lösenord. Om det inte fungerar för dig, behöver du välja mellan direktautentisering och AD FS.

> [!TIP]
> Lösenord lagras i den lokala Windows Server Active Directory i form av ett hash-värde som representerar faktiska användarens lösenord. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion (hash-algoritm). Det finns ingen metod för att återställa resultatet av en envägs funktion till oformaterad text-versionen av ett lösenord. Du kan inte använda lösenords-hash för att logga in på ditt lokala nätverk. När du väljer för att synkronisera lösenord extraherar lösenords-hash från en lokal Active Directory i Azure AD Connect och gäller extra säkerhet bearbetning till lösenords-hash innan det synkroniseras till Azure AD. Lösenordssynkronisering kan också användas tillsammans med tillbakaskrivning av lösenord för att aktivera Självbetjäning för återställning av lösenord i Azure AD. Dessutom kan du aktivera enkel inloggning (SSO) för användare på domänanslutna datorer som är anslutna till företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att få säker åtkomst till molnresurser. 
>

## <a name="pass-through-authentication"></a>Direktautentisering

[Azure AD-direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) är en lösning för verifiering av enkla lösenord för Azure AD-baserade tjänster med din lokala Active Directory. Om principer för säkerhet och efterlevnad för din organisation tillåter inte att skicka användarnas lösenord, även i form av en hashade och du behöver bara stöd för enkel inloggning för skrivbord för domänanslutna enheter, bör du utvärdera med hjälp av direktautentisering. Direktautentisering kräver inte alla distributioner i Perimeternätverket, vilket förenklar distributionsinfrastrukturen jämfört med AD FS. När användarna loggar in med Azure AD, verifierar den här autentiseringsmetoden användarnas lösenord direkt mot din lokala Active Directory.

![Direktautentisering](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Det finns inget behov av en komplex nätverksinfrastruktur med direktautentisering, och du behöver inte lagra lösenord i molnet. I kombination med enkel inloggning, direktautentisering ger en helt integrerad upplevelse vid inloggning till Azure AD eller andra molntjänster.

Direktautentisering har konfigurerats med Azure AD Connect, som använder en enkel lokal agent som lyssnar efter förfrågningar för verifiering av lösenord. Agenten kan distribueras enkelt till flera datorer för att tillhandahålla hög tillgänglighet och belastningsutjämning. Eftersom all kommunikation är bara utgående, finns inga krav för anslutningstjänsten installeras i en DMZ. Krav för server-datorer för anslutningstjänsten är följande:

- Windows Server 2012 R2 eller senare
- Ansluten till en domän i skogen som användare verifieras

## <a name="federated-identity-ad-fs"></a>Federerad identitet (AD FS)

För mer kontroll över hur användare kommer åt Office 365 och andra molntjänster, du kan konfigurera katalogsynkronisering med enkel inloggning (SSO) med hjälp av [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). Federera dina användares inloggningar med AD FS delegerar autentisering till en lokal server som verifierar användarens autentiseringsuppgifter. Den lokala Active Directory-autentiseringsuppgifter skickas aldrig till Azure AD i den här modellen.

![Federerade identiteter](./media/choose-hybrid-identity-solution/federated-identity.png)

Kallas även identitetsfederation, säkerställer den här inloggningsmetod att alla användarautentisering kontrollerad lokalt och gör att administratörer kan implementera mer rigorösa nivåer av åtkomstkontroll. Identitetsfederation med AD FS är alternativet mest komplicerade och kräver distribution av ytterligare servrar i din lokala miljö. Identitetsfederation genomför också du till att 24 x 7-stöd i din Active Directory och AD FS-infrastruktur. Den här hög nivå av support är nödvändigt eftersom om din lokala Internet-, domänkontrollant, eller AD FS-servrar är tillgängliga, användarna kan inte logga in på molntjänster.

> [!TIP]
> Om du vill använda Federation med Active Directory Federation Services (AD FS) kan du också ställa in synkronisering av lösenord som en säkerhetskopia om det inte går att AD FS-infrastrukturen.
>

## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer

Här följer några vanliga hybrid identitets- och hantering av scenarier med rekommendationer om vilka hybrid identity alternativet (eller alternativ) kan vara lämpligt för var och en.

|Jag behöver:|PWS och SSO<sup>1</sup>| PTA och SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synkronisera nya användare, kontakta och gruppkonton som skapas automatiskt i min lokala Active Directory till molnet.|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Konfigurera min klient för hybridscenarion för Office 365|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Aktivera Mina användare att logga in och komma åt cloud services med sina lokala lösenord|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementera enkel inloggning med företagets autentiseringsuppgifter|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Se till att inga hashvärden för lösenord lagras i molnet| |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Aktivera lokal multifaktorautentisering lösningar| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Stöd för autentisering med smartkort för Mina användare<sup>4</sup>| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Visa lösenord upphör att gälla meddelanden i Office-portalen och i Windows 10 desktop| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Lösenordssynkronisering med enkel inloggning.
>
> <sup>2</sup> direktautentisering och enkel inloggning. 
>
> <sup>3</sup> federerad enkel inloggning med AD FS.
>
> <sup>4</sup> AD FS kan integreras med din företagets PKI för att logga in med certifikat. Dessa certifikat kan vara mjuk-certifikat som har distribuerats via betrodda etablering kanaler, till exempel MDM eller GPO eller smartkort-certifikat (inklusive PIV/CAC kort) eller Hello för företag (cert-förtroende). Mer information om stöd för smartkort-autentisering finns i [den här bloggen](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="next-steps"></a>Nästa steg

[Mer information finns i en Azure Proof of Concept-miljö](https://aka.ms/aad-poc)
[installera Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
[övervaka hybrid identitetssynkronisering](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
