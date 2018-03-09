---
title: "Välj en Azure hybrididentitetslösning | Microsoft Docs"
description: "Hämta en grundläggande förståelse av de tillgängliga hybrididentitetslösningar och rekommendationer att göra det bästa identity styrning beslutet för din organisation."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 9f9099c0ebd65ba84e171314e6f04d858648a805
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft hybrididentitetslösningar
[Microsoft Azure Active Directory (AD Azure)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hybrididentitetslösningar gör det möjligt att synkronisera lokala katalogobjekt med Azure AD när fortfarande hantera användare lokalt. Det första beslutet att fatta när du planerar att synkronisera dina lokala Windows Server Active Directory med Azure AD är om du vill använda synkroniserade identitet eller federerad identitet. Synkroniserade identiteter och eventuellt lösenordshashvärden, att användarna kan använda samma lösenord för åtkomst till både lokala och molnbaserade organisationens resurser. För mer avancerade krav för scenarier, till exempel enkel inloggning (SSO) eller lokal MFA som du behöver distribuera Active Directory Federation Services (AD FS) för att federera identiteter. 

Det finns flera alternativ för att konfigurera hybrididentitet. Den här artikeln innehåller information som hjälper dig att välja den bästa för din organisation baserat på enkel distribution och måste din specifika identitets- och åtkomsthantering. När du planerar vilka identitetsmodellen som bäst passar organisationens behov måste du också tänka tid, befintlig infrastruktur, komplexiteten och kostnaden. Dessa faktorer är olika för varje organisation och kan ändras med tiden. Om dina krav gör, har du även dock möjlighet att växla till en annan identitet modell.

> [!TIP]
> Dessa lösningar levereras av [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Synkroniserade identiteter 
Synkroniserade identiteten är det enklaste sättet att synkronisera lokala katalogobjekt (användare och grupper) med Azure AD. 

![Synkroniserade hybrididentitet](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Synkroniserade identitet är den enklaste och snabbaste metoden, måste användarna fortfarande att underhålla separata lösenord för molnbaserade resurser. Om du vill undvika detta kan du också (valfritt) [synkronisera en hash av användarlösenord](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) till Azure AD-katalogen. Synkronisera lösenordshashvärden kan användare logga in på molnbaserade organisationsresurser med samma användarnamn och lösenord som de använder lokalt. Azure AD Connect kontrollerar regelbundet din lokala katalog för ändringar och behåller synkroniserad Azure AD-katalogen. När en användarattribut eller lösenordet är ändrade lokala Active Directory, uppdateras den automatiskt i Azure AD. 

För de flesta organisationer som bara behöver användare att logga in på Office 365, SaaS-program och andra Azure AD-baserade resurser är rekommenderas standardalternativet för synkronisering av lösenord. Om det inte fungerar för dig, måste du välja mellan direktautentisering och AD FS.

> [!TIP]
> Lösenord lagras i lokala Windows Server Active Directory i form av ett hash-värde som representerar faktiska användarens lösenord. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion (hash-algoritm). Det finns ingen metod för att återställa resultatet av en envägs funktion till oformaterad text-versionen av ett lösenord. Du kan inte använda lösenords-hash för att logga in på ditt lokala nätverk. När du väljer för att synkronisera lösenord extraherar lösenordshashvärden från lokala Active Directory Azure AD Connect och extra säkerhet bearbetning till lösenords-hash innan det synkroniseras till Azure AD. Lösenordssynkronisering kan också användas tillsammans med tillbakaskrivning av lösenord för att aktivera Självbetjäning för återställning av lösenord i Azure AD. Dessutom kan du aktivera enkel inloggning (SSO) för användare på domänanslutna datorer som är anslutna till företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att säker åtkomst till molnresurser. 

## <a name="pass-through-authentication"></a>Direktautentisering
[Azure AD-direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) är en lösning för verifiering av enkla lösenord för Azure AD-baserade tjänster som använder din lokala Active Directory. Om säkerhet och principer för efterlevnad för din organisation inte tillåter att skicka användarnas lösenord, även i ett hashformaterats formulär och du behöver bara stöder skrivbord SSO för domänanslutna enheter, bör du utvärdera med hjälp av direktautentisering. Direkt-autentisering kräver inte en distribution i Perimeternätverket, vilket förenklar distributionsinfrastrukturen jämfört med AD FS. När användare loggar in med Azure AD, verifierar den här autentiseringsmetoden användarnas lösenord direkt mot din lokala Active Directory.

![Direktautentisering](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Det finns inget behov av en komplex nätverksinfrastruktur med direktautentisering, och du behöver inte lagra lokala lösenord i molnet. I kombination med enkel inloggning, direktautentisering ger en helt integrerad upplevelse när du loggar in till Azure AD eller andra molntjänster.

Direkt-autentisering har konfigurerats med Azure AD Connect, som använder en enkel lokal agent som lyssnar efter förfrågningar för verifiering av lösenord. Agenten kan enkelt distribueras till flera datorer för att tillhandahålla hög tillgänglighet och belastningsutjämning. Eftersom all kommunikation är endast utgående, krävs inte för anslutningstjänsten installeras i en DMZ. Serverkrav för datorn för anslutningen är följande:

- Windows Server 2012 R2 eller senare
- Ansluten till en domän i skogen som användare verifieras

## <a name="federated-identity-ad-fs"></a>Federerade identiteter (AD FS)
För mer kontroll över hur användare kommer åt Office 365 och andra molntjänster som du kan ställa in katalogsynkronisering med enkel inloggning (SSO) [Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). Federering användarens inloggningar med AD FS delegerar autentisering till en lokal server som validerar användarens autentiseringsuppgifter. Lokala Active Directory-autentiseringsuppgifter skickas aldrig till Azure AD i den här modellen.

![Federerade identiteter](./media/choose-hybrid-identity-solution/federated-identity.png)

Kallas även identitetsfederation, säkerställer den här inloggningsmetod att alla användarautentisering kontrollerade lokalt och gör att administratörer kan implementera mer rigorösa åtkomstkontroll. Identitetsfederation med AD FS är alternativet mest komplicerad och kräver distribution av ytterligare servrar i din lokala miljö. Identitetsfederation genomför också du till att ge stöd för 24 x 7 för Active Directory och AD FS-infrastruktur. Den här hög nivå av stöd är nödvändigt eftersom om din lokala Internet-, domänkontrollant, eller AD FS-servrar är tillgängliga, användare kan inte logga in till molntjänster.

> [!TIP]
> Om du vill använda Federation med Active Directory Federation Services (AD FS) kan du om du vill ställa in synkronisering av lösenord som en säkerhetskopia om det inte går att AD FS-infrastrukturen.


## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer
Här följer några vanliga hybrid identitets- och hantering av scenarier med rekommendationer om vilka hybrid identity alternativet (eller alternativ) kan vara lämpligt för varje.

|Jag behöver:|PWS och enkel inloggning<sup>1</sup>| Tereftalsyra och enkel inloggning<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synkronisera nya, kontakt, grupp- och användarkonton skapas automatiskt i min lokala Active Directory till molnet.|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Konfigurera min klient för hybridscenarion för Office 365|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Användarna att logga in och komma åt molntjänster med hjälp av sina lokala lösenord|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementera enkel inloggning med företagets autentiseringsuppgifter|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png) |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Se till att inga lösenordshashvärden lagras i molnet| |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Aktivera lokal multifaktorautentisering lösningar| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Stöd för autentisering med smartkort för Mina användare<sup>4</sup>| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Meddelandevisning lösenord upphör att gälla i Office-portalen och på Windows 10 desktop| | |![Rekommenderas](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Lösenordssynkronisering med enkel inloggning. 

> <sup>2</sup> direkt-autentisering och enkel inloggning. 

> <sup>3</sup> federerad enkel inloggning med AD FS.

> <sup>4</sup> AD FS kan integreras med din enterprise PKI för att logga in med certifikat. Dessa certifikat kan vara soft-certifikat som har distribuerats via betrodda etablering kanaler, till exempel MDM eller grupprincipobjekt eller smartkort-certifikat (inklusive PIV/CAC kort) eller Hello för företag (cert-förtroende). Mer information om stöd för smartkort-autentisering finns [bloggen](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Nästa steg
[Läs mer i en Azure Proof of Concept-miljö](https://aka.ms/aad-poc)

[Installera Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Övervaka hybrid identitetssynkronisering](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

