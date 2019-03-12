---
title: Översikt över identitet för Azure Stack | Microsoft Docs
description: Läs mer om identitetssystem som du kan använda med Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e548188f1fc44ec32b90d0eaaa4154602e3e51c4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764089"
---
# <a name="overview-of-identity-for-azure-stack"></a>Översikt över identitet för Azure Stack

Azure Stack kräver Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) med Active Directory som en identitetsleverantör. Valet av en provider är ett enstaka beslut som du gör första gången du distribuerar Azure Stack. Koncept och auktorisering information i den här artikeln hjälper dig att välja mellan identitetsleverantörer.

Ditt val av Azure AD eller AD FS avgörs av det läge som du distribuerar Azure Stack:

- När du distribuerar den i ett anslutet läge kan använda du antingen Azure AD eller AD FS.
- När du distribuerar det i frånkopplat läge, utan en anslutning till internet, stöds endast AD FS.

Mer information om dina alternativ, som är beroende av Azure Stack-miljön, finns i följande artiklar:

- Kit för Azure Stack-distribution: [Identitet överväganden](azure-stack-datacenter-integration.md#identity-considerations).
- Integrerade Azure Stack-system: [Distributionsplanerings-beslut för Azure Stack-integrerade system](azure-stack-deployment-decisions.md).

## <a name="common-concepts-for-identity"></a>Vanliga begrepp för identitet

I nästa avsnitt beskrivs vanliga begrepp om identitetsleverantörer och deras användning i Azure Stack.

![Terminologi för Identitetsproviders](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Directory-klienter och organisationer

En katalog är en behållare som innehåller information om *användare*, *program*, *grupper*, och *tjänsthuvudnamn*.

En directory-klient är en *organisation*, till exempel Microsoft eller ditt företag.

- Azure AD stöder flera klienter och den har stöd för flera organisationer i sin egen katalog. Om du använder Azure AD och har flera klienter, kan du bevilja program och användare från en klient åtkomst till andra klienter i samma katalog.
- AD FS stöder bara en enda klient och därför bara en enda organisation.

### <a name="users-and-groups"></a>Användare och grupper

Användarkonton (identiteter) är standardkonton som enskilda användare autentiseras med hjälp av ett användar-ID och lösenord. Grupper kan innehålla användare eller andra grupper.

Hur du skapar och hanterar användare och grupper är beroende av ID-lösning som du använder.

I Azure Stack, användarkonton:

- Skapas i den *username@domain* format. Även om AD FS mappar användarkonton till en Active Directory-instans, AD FS inte stöd för användning av den  *\\ \<domän >\\\<alias >* format.
- Kan ställas in för att använda multifaktorautentisering.
- Är begränsade till den katalog där de först registrera, vilket är sin organisations katalog.
- Du kan importera från dina lokala kataloger. Mer information finns i [integrerar dina lokala kataloger med Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

När du loggar in till din organisations klient-portalen kan du använda den *https://portal.local.azurestack.external* URL: en. När du loggar in på Azure Stack-portalen från andra domäner än den som används för att registrera Azure Stack, domännamn som används för att registrera Azure Stack måste läggas till på portalen URL: en. Till exempel om Azure Stack har registrerats med fabrikam.onmicrosoft.com och det användarkonto som har loggat in är admin@contoso.com, URL: en du använder för att logga in på användarportalen: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Gästanvändare

Gästanvändare är användarkonton från andra klienter i katalogen som har beviljats åtkomst till resurser i din katalog. För gästanvändare måste du använda Azure AD och aktivera stöd för flera innehavare. När support har aktiverats kan du bjuda in gästanvändare rätt att komma åt resurser i din katalog-klient, som i sin tur aktiverar sina samarbete med utanför organisationer.

Om du vill bjuda in gästanvändare molnoperatörer och användare kan använda [Azure AD B2B-samarbete](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Inbjudna användare få åtkomst till dokument, resurser och program från din katalog och du behåller kontrollen över dina egna resurser och data. 

Som gästanvändare, kan du logga in till en annan organisation directory-klient. Om du vill göra det du lägga till den organisationens katalognamnet på portalen URL: en. Till exempel om du ingår i Contoso-organisation och vill logga in till katalogen Fabrikam du använda https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Appar

Du kan registrera program till Azure AD eller AD FS och sedan erbjuda program till användare i din organisation.

Programmen innehåller:

- **Webbprogram**: Exempel: Azure-portalen och Azure Resource Manager. De stöder webb-API-anrop.
- **Inbyggd klient**: Exempel: Azure PowerShell, Visual Studio och Azure CLI.

Program som stöder två typer av organisationer i samma installation:

- **Enda klient**: Har stöd för användare och tjänster från samma katalog där programmet har registrerats.

  > [!NOTE]
  > Eftersom AD FS stöder endast en enskild katalog kan är program som du skapar i en AD FS-topologi avsiktligt enda klient program.

- **Flera innehavare**: Stöder Använd av användare och tjänster från både directory där programmet har registrerats och ytterligare klient kataloger. Program med flera klienter logga användare av en annan klient directory (en annan Azure AD-klient) kan in på ditt program. 

  Läs mer om flera innehavare, [aktivera flera innehavare](azure-stack-enable-multitenancy.md).

  Mer information om hur du utvecklar en app för flera klienter finns i [appar för flera klienter](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

När du registrerar ett program kan skapa du två objekt:

- **Programobjekt**: En global representation av programmet för alla klienter. Den här relationen är en med programmet och finns bara i den katalog där programmet registreras första gången.

- **Tjänstens huvudnamnsobjekt**: En autentiseringsuppgift som har skapats för ett program i katalogen där programmet registreras första gången. Ett huvudnamn för tjänsten skapas också i katalogen för varje ytterligare klient där programmet används. Den här relationen kan vara en-till-många med programmet.

Läs mer om program och tjänstobjekt i [program och tjänstobjekt i Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Tjänstens huvudnamn

Ett huvudnamn för tjänsten är en uppsättning *autentiseringsuppgifter* för ett program eller tjänst som ger åtkomst till resurser i Azure Stack. Användning av ett huvudnamn för tjänsten separerar programbehörigheter från användarens behörigheter för programmet.

Ett huvudnamn för tjänsten har skapats i varje klient där programmet används. Tjänstens huvudnamn upprättar en identitet för inloggning och åtkomst till resurser (t.ex användare) som skyddas av den klienten.

- En enda klient-programmet har bara ett tjänstens huvudnamn i katalogen där den skapas. Den här tjänstens huvudnamn har skapats och godkänner att som används under registreringen av programmet.
- Ett webbprogram för flera innehavare eller API har ett huvudnamn för tjänsten som skapas i varje klient där en användare från den klienten godkänner du användningen av programmet.

Autentiseringsuppgifter för tjänstens huvudnamn kan vara antingen en nyckel som skapas via Azure-portalen eller ett certifikat. Användning av ett certifikat är lämpligt för automation eftersom certifikat anses vara säkrare än nycklar. 

> [!NOTE]
> När du använder AD FS med Azure Stack kan kan bara administratörer skapa tjänstens huvudnamn. Med AD FS, tjänstens huvudnamn kräver certifikat och skapas via den privilegierade slutpunkten (program). Mer information finns i [ge program åtkomst till Azure Stack](azure-stack-create-service-principals.md).

Läs om tjänstens huvudnamn för Azure Stack i [skapa tjänstens huvudnamn](azure-stack-create-service-principals.md).

### <a name="services"></a>Tjänster

Tjänster i Azure Stack som interagerar med identitetsprovidern som är registrerade som program med identitetsprovidern. Registreringen kan t.ex. program, en tjänst för att autentisera med ID-system.

Alla Azure-tjänster använder [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokoll och [JSON Web token](/azure/active-directory/develop/active-directory-token-and-claims) att upprätta sin identitet. Eftersom Azure AD och AD FS använder protokollen konsekvent, kan du använda [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) för att autentisera lokalt eller till Azure (i ett scenario med anslutna). Du kan också använda verktyg som Azure PowerShell och Azure CLI för molnet och lokala resurshantering med ADAL.

### <a name="identities-and-your-identity-system"></a>Identiteter och ID-system

Identiteter för Azure Stack är användarkonton, grupper och tjänstens huvudnamn.

När du installerar Azure Stack, flera inbyggda program och tjänster automatiskt registrera med din identitetsprovider i directory-klient. Vissa tjänster som registrerar används för administration. Andra tjänster är tillgängliga för användare. Standard-registreringar ge core services identiteter som kan användas både med varandra och med identiteter som du lägger till senare.

Om du har konfigurerat Azure AD med flera innehavare kan spridas vissa program till nya kataloger.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

### <a name="authentication-by-applications-and-users"></a>Autentisering av program och användare

![Identitet mellan lager i Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

För program och användare beskrivs arkitekturen för Azure Stack med fyra lager. Samverkan mellan var och en av dessa lager kan använda olika typer av autentisering.

|Lager    |Autentisering mellan lager  |
|---------|---------|
|Verktyg och klienter, till exempel Admin portal     | Att ändra en resurs i Azure Stack, verktyg och -klienter använder en [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) att ringa ett samtal till Azure Resource Manager. <br>Azure Resource Manager verifierar JSON Web Token och tittar på den *anspråk* i utfärdade token för att uppskatta utbud av auktoriseringar har användaren eller tjänstens huvudnamn i Azure Stack. |
|Azure Resource Manager och dess kärntjänsterna     |Azure Resource Manager kommunicerar med resursprovidrar att överföra kommunikation från användare. <br> Överför Använd *direkt tvingande* anrop eller *deklarativa* anropar [Azure Resource Manager-mallar](/azure/azure-stack/user/azure-stack-arm-templates).|
|Resursproviders     |Anrop som skickas till resursprovidrar skyddas med certifikatbaserad autentisering. <br>Azure Resource Manager och resursprovidern sedan Behåll kommunikation via ett API. För varje anrop som tas emot från Azure Resource Manager, verifierar resursprovidern anropet med certifikatet.|
|Infrastruktur och affärslogik     |Resursproviders kommunicera med affärslogik och infrastruktur med hjälp av ett autentiseringsläge för valfri. Standard-resursprovidrar som levereras med Azure Stack använder Windows-autentisering för att skydda den här kommunikationen.|

![Information som behövs för autentisering](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Autentisera till Azure Resource Manager

För att autentisera med identitetsprovidern och ta emot en JSON Web Token, måste du ha följande information:

1. **URL: en för ID-system (Authority)**: Den URL som din identitetsprovider kan nås. Till exempel *https://login.windows.net*.
2. **App-ID-URI: N för Azure Resource Manager**: Den unika identifieraren för Azure Resource Manager som har registrerats hos din identitetsprovider. Det är också unik för varje Azure Stack-installation.
3. **autentiseringsuppgifter**: De autentiseringsuppgifter som du använder för att autentisera med identitetsprovidern.
4. **URL för Azure Resource Manager**: URL: en är platsen för Azure Resource Manager-tjänsten. Till exempel *https://management.azure.com* eller *https://management.local.azurestack.external*.

När ett huvudnamn (en klient, program eller användare) gör en autentiseringsbegäran till en resurs, måste begäran innehålla:

- Huvudkontots autentiseringsuppgifter.
- App-ID-URI för den resurs som huvudkontot som vill få åtkomst.

Att autentiseringsuppgifterna validerats av identitetsleverantören. Identitetsprovidern validerar också att ID-URI är för ett registrerat program och att huvudkontot har rätt behörighet för att hämta en token för den resursen. Om förfrågan är giltig, tilldelas en JSON Web Token.

Token måste sedan klara i rubriken för en begäran till Azure Resource Manager. Azure Resource Manager har följande, utan någon särskild ordning:

- Verifierar den *utfärdare* (iss) gör anspråk på att bekräfta att token kommer från rätt identitetsprovidern.
- Verifierar den *målgrupp* (aud) anspråk för att bekräfta att token utfärdats till Azure Resource Manager.
- Verifierar att JSON Web Token har signerats med ett certifikat som är konfigurerad via OpenID är känd till Azure Resource Manager.
- Granska den *vid* (iat) och *upphör att gälla* (exp) utger sig för att bekräfta att token är aktiv och kan godkännas.

När alla verifieringar har slutförts, Azure Resource Manager använder den *objekt* (oid) och *grupper* utger sig för att se en lista över resurser som huvudkontot som har åtkomst till.

![Diagram över protokollet tokenutbytet](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Efter distributionen krävs inte behörighet för Azure Active Directory global administratör. Vissa åtgärder kan dock kräva autentiseringsuppgifter för global administratör. Till exempel ett resource provider installer skript eller en ny funktion som kräver en behörighet som ska beviljas. Du kan tillfälligt återställa kontots behörigheter som global administratör eller använda ett separat globalt administratörskonto som äger den *standard providerprenumeration*.

### <a name="use-role-based-access-control"></a>Använda rollbaserad åtkomstkontroll

Rollbaserad åtkomstkontroll (RBAC) i Azure Stack är konsekvent med implementering i Microsoft Azure. Du kan hantera åtkomst till resurser genom att tilldela lämpliga RBAC-roll till användare, grupper och program.
Information om hur du använder RBAC med Azure Stack finns i följande artiklar:

- [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](/azure/role-based-access-control/overview).
- [Använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumerationsresurser](/azure/role-based-access-control/role-assignments-portal).
- [Skapa anpassade roller för rollbaserad åtkomstkontroll i](/azure/role-based-access-control/custom-roles).
- [Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) i Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Autentisera med Azure PowerShell

Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns på [konfigurerar du Azure Stack PowerShell-miljö](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autentisera med Azure CLI

Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns i [installera och konfigurera Azure CLI för användning med Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli).

## <a name="next-steps"></a>Nästa steg

- [Identitetsarkitektur](azure-stack-identity-architecture.md)
- [Datacenter-integrering - identitet](azure-stack-integrate-identity.md)
