---
title: "Översikt över identitet för Azure-stacken | Microsoft Docs"
description: "Läs mer om identitetssystem som du kan använda med Azure-stacken."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: deebe5d8ff4c35c4507d2daf5c15025a1810d865
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Översikt över identitet för Azure-stacken

Azure-stacken kräver Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS), backas upp av Active Directory som en identitetsleverantör. Valet av en provider är en enstaka beslut som du ska göra när du distribuerar Azure stacken. Koncept och auktorisering information i den här artikeln kan hjälpa dig att välja mellan identitetsleverantörer. 

Ditt val av Azure AD eller AD FS kan fastställas genom det läge som du distribuerar Azure Stack: 
- När du distribuerar den i anslutet läge kan du använda antingen Azure AD eller AD FS. 
- När du distribuerar den i ett frånkopplat läge, utan en anslutning till internet, stöds endast AD FS.

Mer information om alternativen, beroende på din Azure Stack-miljö finns i följande artiklar:
- Azure Stack deployment kit: [identitet överväganden](azure-stack-datacenter-integration.md#identity-considerations).
- Azure-stacken integrerat system: [beslut för Azure-Stack för planering av distribution integrerat system](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Vanliga koncept för identitet
I nästa avsnitt beskrivs vanliga begrepp om identitetsleverantörer och deras användning i Azure-stacken.

![Terminologi för leverantörer](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Directory-klienter och organisationer
En katalog är en behållare som innehåller information om *användare*, *program*, *grupper*, och *tjänsten säkerhetsobjekt*.
 
En directory-klient är en *organisation*, till exempel Microsoft eller ditt företag. 
- Azure AD stöder flera klienter och den kan stödja flera organisationer i en egen katalog. Om du använder Azure AD och har flera klienter kan du bevilja program och användare från en klient åtkomst till andra klienter i samma katalog.
- AD FS stöder bara en enda klient och därmed endast en enda organisation. 

### <a name="users-and-groups"></a>Användare och grupper
Användarkonton (identiteter) är standardkonton som enskilda användare autentiseras med hjälp av användar-ID och lösenord. Grupper kan innehålla användare eller andra grupper. 

Hur du skapar och hantera användare och grupper är beroende av identitetslösning som du använder. 

I Azure-stacken, användarkonton: 
- Skapas i den  *username@domain*  format. Även om AD FS mappar användarkonton till en instans av Active Directory, AD FS inte stöder användning av den  *\<domän >\<alias >* format. 
- Kan ställas in att använda multifaktorautentisering. 
- Är begränsade till den katalog där de först registrera, vilket är organisationens katalog.
- Du kan importera från dina lokala kataloger. Mer information finns i [integrera dina lokala kataloger med Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect). 

När du loggar in på klientportal för din organisation använder du den  *https://portal.local.azurestack.external*  URL. 

### <a name="guest-users"></a>Gästanvändare
Gästanvändare är användarkonton från andra klienter i katalogen som har beviljats åtkomst till resurser i din katalog. Stöd för gästanvändare kan du använda Azure AD och aktivera stöd för flera innehavare. Du kan be gästanvändare åtkomst till resurser i din katalog-klient, som i sin tur aktiverar sina samarbete med externa organisationer när stöd är aktiverat. 

Om du vill bjuda in gästanvändare molnoperatörer och användare kan använda [Azure AD B2B-samarbete](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Inbjudna användare få åtkomst till dokument, resurser och program från katalogen och du behålla kontrollen över dina egna resurser och data. 

Som gästanvändare, kan du logga in på en annan organisation directory-klient. Om du vill göra det måste du lägga till katalognamnet för den organisation till portalen URL. Exempelvis om du hör till Contoso-organisationen och vill logga in till katalogen Fabrikam, använder du https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Program
Du kan registrera Azure AD eller AD FS-program och sedan erbjuda program till användare i din organisation. 

Program innehåller:
- **Webbprogrammet**: exempel Azure-portalen och Azure Resource Manager. De stöder Web API-anrop. 
- **-Klienten**: exempel: Azure PowerShell, Visual Studio och Azure CLI.

Program kan stödja två typer av organisationer i samma installation: 
- **Stöd för en innehavare**: har stöd för användare och tjänster enbart från samma katalog där programmet har registrerats. 

  > [!NOTE]    
  > Eftersom AD FS stöder bara en katalog kan är program som du skapar i en AD FS-topologin inbyggt stöd för en innehavare program.

- **Flera innehavare**: använda av användare och tjänster från både katalogen där programmet har registrerats och ytterligare klient kataloger. Med flera innehavare program logga användare av en annan klient directory (en annan Azure AD-klient) kan in på ditt program. 

  Läs mer om multitenans [aktivera multitenans](azure-stack-enable-multitenancy.md). 

  Mer information om hur du utvecklar en app för flera innehavare finns [flera innehavare appar](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

När du registrerar ett program kan skapa du två objekt:

- **Programobjektet**: globala representation av program över alla klienter. Den här relationen är 1 med programmet och finns bara i katalogen där programmet först har registrerats.

- **Tjänstens huvudnamn objekt**: autentiseringsuppgifter som har skapats för ett program i katalogen där programmet först har registrerats. Ett huvudnamn för tjänsten skapas också i katalogen för varje ytterligare klient där programmet används. Den här relationen kan vara en-till-många med programmet. 

Läs mer om programmet och service principal objekt i [program och tjänstens huvudnamn objekt i Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Tjänstens huvudnamn 
Ett huvudnamn för tjänsten är en uppsättning *autentiseringsuppgifter* för ett program eller tjänst som ger åtkomst till resurser i Azure-stacken. Användning av en tjänst som separerar programbehörigheter från användarens behörighet för programmet.

Ett huvudnamn för tjänsten har skapats i varje klient där programmet används. Tjänstens huvudnamn upprättar en identitet för inloggning och åtkomst till resurser som skyddas av den klienten (till exempel användare). 

- En enskild klient-programmet har bara en tjänstens huvudnamn i katalogen där den skapas. Den här tjänstens huvudnamn skapas och samtycker till som används under registreringen av programmet. 
- Ett webbprogram för flera innehavare eller API har ett huvudnamn för tjänsten som skapas i varje klient där en användare från att innehavaren samtycker till användning av programmet. 

Autentiseringsuppgifter för tjänstens huvudnamn kan vara antingen en nyckel som skapas via Azure-portalen eller ett certifikat. Användning av ett certifikat är lämpligt för automatisering eftersom certifikat anses vara säkrare än nycklar. 


> [!NOTE]    
> När du använder AD FS med Azure-stacken kan endast administrator skapa tjänstens huvudnamn. Med AD FS tjänstens huvudnamn kräver certifikat och har skapats via Privilegierade slutpunkten (program). Mer information finns i [ge program åtkomst till Azure-stacken](azure-stack-create-service-principals.md).

Mer information om tjänstens huvudnamn för Azure-stacken, se [skapa tjänstens huvudnamn](azure-stack-create-service-principals.md).


### <a name="services"></a>Tjänster
Tjänster i Azure-stacken som samverkar med identitetsleverantören är registrerad som program med identitetsleverantören. T.ex. program aktiverar registrering en tjänst att autentisera med systemets identitet. 

Alla Azure-tjänster använder [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokoll och [JSON Web token](/azure/active-directory/develop/active-directory-token-and-claims) att styrka sin identitet. Eftersom Azure AD och AD FS använder protokoll konsekvent, kan du använda [Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) för att autentisera lokalt eller till Azure (i scenariot). Du kan också använda verktyg som Azure PowerShell och Azure CLI för cross-molnet och lokala resurshantering med ADAL. 


### <a name="identities-and-your-identity-system"></a>Identiteter och identitetssystem 
Identiteter för Azure-stacken inkluderar användarkonton, grupper och tjänstens huvudnamn. 

När du installerar Azure Stack registrera flera inbyggda program och tjänster automatiskt sig i din identitetsleverantör i directory-klient. Vissa tjänster som registrerar används för administration. Andra tjänster som är tillgängliga för användare. Standard-registreringar ge core services identiteter som kan interagera med varandra såväl med identiteter som du lägger till senare.

Om du konfigurerar Azure AD med flera innehavare, vissa program och tillämpas på nya kataloger. 

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
 

### <a name="authentication-by-applications-and-users"></a>Autentisering av program och användare
  
![Identitet mellan lager i Azure-stacken](media/azure-stack-identity-overview/identity-layers.png)

För program och användare beskrivs arkitekturen i Azure-stacken genom fyra lager. Samverkan mellan var och en av dessa lager kan använda olika typer av autentisering.


|Lager    |Autentisering mellan lager  |
|---------|---------|
|Verktyg och klienter, till exempel administrationsportal     | Att ändra en resurs i Azure-stacken, verktyg och klienter använder en [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) att ringa ett samtal till Azure Resource Manager. <br>Azure Resource Manager validerar JSON Web Token och tittar på det *anspråk* Utfärdad token att uppskatta utbud av auktoriseringar användaren eller tjänstens huvudnamn har i Azure-stacken. |
|Azure Resource Manager och dess kärntjänster     |Azure Resource Manager kommunicerar med resursproviders överföra kommunikation från användare. <br> Överför Använd *direkt tvingande* anrop eller *deklarativa* anropar [Azure Resource Manager-mallar](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Resursproviders     |Anrop som skickas till resursproviders är skyddade med certifikatbaserad autentisering. <br>Azure Resource Manager och resursprovidern sedan kvar i kommunikation via ett API. För varje anrop som tas emot från Azure Resource Manager verifierar resursprovidern anrop med certifikatet.|
|Infrastruktur- och business logic     |Resursproviders kommunicera med affärslogik och infrastruktur med hjälp av ett autentiseringsläge efter eget val. Standard-resursproviders som levereras med Azure-stacken använder Windows-autentisering för att skydda den här kommunikationen.|

![Information som krävs för autentisering](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Autentisera till Azure Resource Manager
För att autentisera med identitetsleverantören och ta emot en JSON Web Token, måste du ha följande information: 
1.  **URL: en för identitetssystem (Authority)**: URL: en identitetsprovider går att nå. Till exempel  *https://login.windows.net* . 
2.  **App-ID-URI för Azure Resource Manager**: den unika identifieraren för Azure Resource Manager som har registrerats med din identitetsprovider. Det är unik för varje Azure Stack-installation.
3.  **Autentiseringsuppgifter**: autentiseringsuppgifter som du använder att autentisera med identitetsleverantören. 
4.  **URL för Azure Resource Manager**: URL: en är platsen för Azure Resource Manager-tjänsten. Till exempel  *https://management.azure.com*  eller  *https://management.local.azurestack.external* .

När en huvudansvarig (en klient, program eller användare) gör en autentiseringsbegäran till en resurs, måste begäran inkludera:
- Måste huvudkontots autentiseringsuppgifter.
- App-ID-URI för den resurs som huvudansvarig vill komma åt. 

Att autentiseringsuppgifterna validerats av identitetsleverantören. Identitetsleverantören validerar också att appen ID URI är för ett registrerat program och att objektet har rätt privilegier för att få en token för den här resursen. Om begäran är giltig, tilldelas en JSON Web Token. 

Token måste sedan klara i rubriken för en begäran till Azure Resource Manager. Azure Resource Manager gör följande, utan någon särskild ordning:
- Verifierar den *utfärdaren* (iss) anspråk och bekräfta att token från identitetsleverantören korrekt. 
- Verifierar den *målgruppen* (eller)-anspråk för att bekräfta att token utfärdats till Azure Resource Manager. 
- Verifierar att JSON Web Token har signerats med ett certifikat som är konfigurerad via OpenID är känd till Azure Resource Manager. 
- Granska de *vid* (iat) och *giltighetstid* (exp) utger sig för att bekräfta att token är aktiv och kan accepteras. 

När du är klar med alla verifieringar Azure Resource Manager använder de *objekt* (oid) och *grupper* utger sig för att se en lista över resurser som kan komma åt objektet. 

![Diagram över token exchange-protokollet](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Använda rollbaserad åtkomstkontroll  
Rollbaserad åtkomstkontroll (RBAC) i Azure-stacken är konsekvent med implementeringen i Microsoft Azure. Du kan hantera åtkomst till resurser genom att tilldela rollen RBAC till användare, grupper och program. Information om hur du använder RBAC med Azure-stacken, finns i följande artiklar:
- [Kom igång med rollbaserad åtkomstkontroll i Azure portal](/azure/active-directory/role-based-access-control-what-is).
- [Använda rollbaserad åtkomstkontroll för att hantera åtkomst till resurserna i Azure-prenumeration](/azure/active-directory/role-based-access-control-configure).
- [Skapa anpassade roller för rollbaserad åtkomstkontroll i](/azure/active-directory/role-based-access-control-custom-roles).
- [Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) i Azure-stacken.


### <a name="authenticate-with-azure-powershell"></a>Autentisera med Azure PowerShell  
Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns på [konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autentisera med Azure CLI
Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns [installera och konfigurera Azure CLI för användning med Azure-stacken](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Nästa steg
- [Identity-arkitektur](azure-stack-identity-architecture.md)   
- [Datacenter-integrering - identitet](azure-stack-integrate-identity.md)




