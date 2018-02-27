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
ms.openlocfilehash: 609ea3300806f3cfed4a3285a096f59be491c684
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Översikt över identitet för Azure-stacken

Azure-stacken kräver Azure AD eller Active Directory federerat Services (AD FS) backas upp av Active Directory (AD) som en identitetsleverantör. Följande begrepp och auktorisering information kan hjälpa dig att välja mellan att identitetsleverantörer. Valet av en provider är en enstaka beslut som du ska göra när du distribuerar Azure stacken.  

Ditt val mellan Azure AD och AD FS kan begränsas av det läge som du distribuerar Azure Stack: 
- När du distribuerar i anslutet läge kan du använda Azure AD eller AD FS. 
- När du distribuerar i frånkopplat läge utan en anslutning till Internet stöds endast AD FS.

Mer information om dessa alternativ kan du läsa följande artiklar beroende på miljön Azure Stack:
- Azure Stack Deployment Kit Se [identitet överväganden](azure-stack-datacenter-integration.md#identity-considerations).
- Azure Stack integrerat system Se [beslut för Azure-Stack för planering av distribution integrerat system](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Vanliga koncept för identitet
I följande avsnitt beskrivs vanliga koncept för identitetsleverantörer och deras användning i Azure-stacken.
![Terminologi för leverantörer](media/azure-stack-identity-overview/terminology.png)


### <a name="directories-tenants-and-organizations"></a>Kataloger klienter och organisationer
En katalog är en behållare som innehåller information om *användare*, *program*, *grupper*, och *tjänsten säkerhetsobjekt*.
 
En directory-klient är en *organisation*, t.ex. Microsoft eller ditt företag. 
- Azure AD stöder flera klienter och har stöd för flera organisationer i sina egna kataloger. Om du använder Azure AD och har flera klienter kan du bevilja program och användare från en klient åtkomst till andra klienter i samma katalog.
- AD FS stöder bara en enskild klient, och därmed en enda organisation. 

### <a name="users-and-groups"></a>Användare och grupper
Användarkonton (identiteter) är standardkonton som autentiseras enskilda användare med ett användar-ID och lösenord. Grupper kan innehålla användare eller andra grupper. 

Hur du skapar och hantera användare och grupper är beroende av identitetslösning som du använder. 

I Azure-stacken, användarkonton: 
- Skapas i den  *username@domain*  format. Även om AD FS mappar användarkonton till en Active Directory, AD FS har inte stöd för användning av den  _&lt;domän >\<alias >_ format. 
- Kan ställas in att använda multifaktorautentisering. 
- Är begränsade till katalogen där de först registrera, som är katalog för deras organisationer.
- Du kan importera från dina lokala kataloger. Mer information finns i [integrera dina lokala kataloger med Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect) i Azure-dokumentationen.  

När du loggar in i klientportal för organisationer, använder du https://portal.local.azurestack.external. 

### <a name="guest-users"></a>Gästanvändare
Gästanvändare är användarkonton från andra klienter i katalogen som har beviljats åtkomst till resurser i din katalog. Stöd för gästanvändare kan du använda Azure AD och aktivera stöd för flera innehavare. När stöds, kan du bjuda in gästanvändare att komma åt resurser i din katalog-klient som gör att samarbete med externa organisationer. 

Om du vill bjuda in gästanvändare molnoperatörer och användare kan använda [Azure AD B2B-samarbete](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Uppmanas användare få åtkomst till dokument, resurser och program från katalogen samtidigt som du behåller kontrollen över dina egna resurser och data. 

Du kan logga in på en annan organisationer directory-klient som en gästanvändare. Om du vill göra det, du måste lägga till som organisationer katalognamnet till portalen URL.  Till exempel om du tillhör contoso.com men vill logga in på katalogen Fabrikam du använda https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>Program
Du kan registrera program till Azure AD eller AD FS och erbjuda dem till användare i din organisation. 

Program innehåller:
- **Webbprogrammet** – exempel Azure-portalen och Azure Resource Manager.  De stöder Web API-anrop. 
- **-Klienten** – exempel Azure PowerShell, Visual Studio och Azure-kommandoradsgränssnittet (CLI).

Program kan stödja två typer av organisationer i samma installation: 
- **Stöd för en innehavare** program har stöd för användare och tjänster enbart från samma katalog där programmet har registrerats. 

  > [!NOTE]    
  > Eftersom AD FS stöder bara en katalog, är program som du skapar i en AD FS-topologi inbyggt stöd för en innehavare program.
- **Flera innehavare** program support används av användare och tjänster från både den katalog där programmet har registrerats och ytterligare klient kataloger.  Med flera innehavare program logga användare av en annan klient directory (en annan Azure AD-klient) kan in på ditt program.     

  Läs mer om multitenans [aktivera multitenans](azure-stack-enable-multitenancy.md).  

  Mer information om hur du utvecklar en app för flera innehavare finns [flera innehavare appar](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

När du registrerar ett program, skapas två objekt:
- **Programobjektet** – programobjektet är den globala representationen av programmet på alla klienter. Den här relationen är 1 med programmet och finns bara i katalogen där programmet är första register.

 
   
- **Tjänstens huvudnamn objekt** – ett huvudnamn för tjänsten är en autentiseringsuppgift som har skapats för ett program i katalogen där programmet först har registrerats. Ett huvudnamn för tjänsten skapas också i katalogen för varje ytterligare klient där programmet används. Den här relationen kan vara en en-till-många med programmet.   

Läs mer om programmet och service principal objekt i [program och tjänstens huvudnamn objekt i Azure Active Directory (AD Azure)](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Tjänstens huvudnamn 
Ett huvudnamn för tjänsten är en uppsättning *autentiseringsuppgifter* för ett program eller tjänst som ger åtkomst till resurser i Azure-stacken. Användning av en tjänst som separerar programbehörigheter från behörigheterna för den användare som använder programmet.

Ett huvudnamn för tjänsten har skapats i varje klient där programmet används. Tjänstens huvudnamn upprättar en identitet för inloggning och åtkomst till resurser (t.ex. användare) som skyddas av den klientorganisationen.   

- En enskild klient-programmet har bara en tjänstens huvudnamn i katalogen där den skapas.  Den här tjänstens huvudnamn skapas och samtycker till att använda under registrering av programmet. 
- Ett webbprogram för flera innehavare eller API har ett huvudnamn för tjänsten som skapats i varje klient där en användare från att innehavaren samtycker till användning av programmet.  

Autentiseringsuppgifter för tjänstens huvudnamn kan vara nyckel (genereras via Azure portal) eller ett certifikat.  Använda ett certifikat är lämpligt för automatisering eftersom certifikat anses vara säkrare än nycklar. 


> [!NOTE]    
> När du använder AD FS med Azure-stacken kan endast administrator skapa tjänstens huvudnamn. Med AD FS tjänstens huvudnamn kräver certifikat och har skapats via Privilegierade slutpunkten (program). Mer information finns i, [ge program åtkomst till Azure-stacken](azure-stack-create-service-principals.md).

Mer information om tjänstens huvudnamn för Azure-stacken, se [skapa tjänstens huvudnamn](azure-stack-create-service-principals.md).


### <a name="services"></a>Tjänster
Tjänster i Azure-stacken som samverkar med identitetsleverantören registreras som program med identitetsleverantören. T.ex. program aktiverar registrering en tjänst att autentisera med systemets identitet. 

Alla Azure-tjänster använder [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) protokoll och [JSON Web token](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) för att styrka sin identitet. På grund av konsekvent användning av protokoll av Azure AD och AD FS kan du använda Azure [Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) för att autentisera lokalt eller till Azure (i scenariot). ADAL också kan du använda verktyg som Azure PowerShell och CLI för cross-molnet och lokala resurshantering. 


### <a name="identities-and-your-identity-system"></a>Identiteter och identitetssystem 
Identiteter för Azure-stacken inkluderar användarkonton, grupper och tjänstens huvudnamn. När du installerar Azure Stack registrera flera inbyggda program och tjänster automatiskt sig i din identitetsleverantör i directory-klient. Vissa tjänster som registrerar används för administration. Andra tjänster som är tillgängliga för användare. Standard-registreringar ge core services identiteter som kan interagera med varandra och med identiteter du lägga till senare.
Om du konfigurerar Azure AD med flera innehavare, vissa program och tillämpas på nya kataloger.  

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
 

### <a name="authentication-by-applications-and-users"></a>Autentisering av program och användare
  
![Identitet mellan lager i Azure-stacken](media/azure-stack-identity-overview/identity-layers.png)

För program och användare beskrivs arkitekturen i Azure-stacken genom fyra lager. Samverkan mellan var och en av dessa lager kan använda olika typer av autentisering.


|Lager    |Autentisering mellan lager  |
|---------|---------|
|Verktyg och klienter, till exempel administrationsportal     | Att ändra en resurs i Azure-stacken, verktyg och klienter använder en [JSON Web token](/azure/active-directory/develop/active-directory-token-and-claims) (JWT) för att placera ett anrop till Azure Resource Manager.  <br><br> Azure Resource Manager verifierar JWT och tittar på det *anspråk* Utfärdad token att uppskatta utbud av auktoriseringar användaren eller tjänstens huvudnamn har i Azure-stacken.        |
|Azure Resource Manager och dess kärntjänster     |Azure Resource Manager kommunicerar med Resursproviders överföra kommunikation från användare. <br><br> Överför Använd *direkt tvingande* anrop eller *deklarativa* anropar [Azure Resource Manager-mallar](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Resursprovidrar     |Anrop till Resursleverantörer är skyddade med certifikatbaserad autentisering. <br><br>Azure Resource Manager och Resursprovidern sedan kvar i kommunikation via ett API. För varje anrop som tagits emot från Azure Resource Manager verifierar Resursprovidern anrop med certifikatet.|
|Infrastruktur och affärslogik     |Resursproviders kommunicera med affärslogik och infrastruktur med hjälp av ett autentiseringsläge efter eget val. Standard Resursleverantörer som levereras med Azure-stacken använder Windows-autentisering för att skydda den här kommunikationen.|

![Information som krävs för autentisering](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Autentisera till Azure Resource Manager
För att autentisera med identitetsleverantören och ta emot en JWT, måste du ha följande information: 
1.  **URL: en för identitetssystem (Authority)** – URL: en identitetsprovider går att nå. Till exempel  *&lt;https://login.windows.net>*. 
2.  **App-ID-URI för Azure Resource Manager** – den unika identifieraren för Azure Resource Manager som är registrerade med din identitetsprovider och unik för varje Azure Stack-installation.
3.  **Autentiseringsuppgifter** – den här autentiseringsuppgifter som du använder för att autentisera med identitetsleverantören.  
4.  **URL för Azure Resource Manager** – URL: en är platsen för Azure Resource Manager-tjänsten. Till exempel  *&lt;https://management.azure.com>* eller  *&lt;https://management.local.azurestack.external>*.

När en huvudansvarig (en klient, program eller användare) gör en autentiseringsbegäran till en resurs, måste begäran inkludera:
- Måste huvudkontots autentiseringsuppgifter.
- URI: N för App-ID för resursen som de vill ha åtkomst till.  

Att autentiseringsuppgifterna validerats av identitetsleverantören. Identitetsleverantören validerar också att App-ID-URI är för ett program som är registrerad och att objektet har rätt behörighet att hämta en token för den här resursen. Om begäran är giltig, tilldelas en JSON Web Token. 

Token måste sedan klara i rubriken för en begäran till Azure Resource Manager. Azure Resource Manager gör att följande verifieringar utan någon särskild ordning:
- Verifiera den *utfärdaren* (iss) anspråk och bekräfta att token från identitetsleverantören korrekt. 
- Verifiera den *målgruppen* (eller)-anspråk för att bekräfta att token utfärdats till Azure Resource Manager. 
- Verifiera att JWT har signerats med ett certifikat som har konfigurerats via OpenID och som är känd till Azure Resource Manager. 
- Granska de *vid* (iat) och *upphör att gälla* (exp) utger sig för att bekräfta att token är aktiv och kan accepteras. 

När du är klar med alla verifieringar Azure Resource Manager använder de *objekt* (oid) och *grupper* utger sig för att se en lista över resurser som kan komma åt objektet. 

![Diagram över token exchange-protokollet](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Använda rollbaserad åtkomstkontroll  
Rollbaserad åtkomstkontroll (RBAC) i Azure-stacken är konsekvent med implementeringen i Microsoft Azure.  Du kan hantera åtkomst till resurser genom att tilldela rollen RBAC till användare, grupper och program. För information om hur du använder RBAC med Azure-stacken, gå igenom följande artiklar:
- [Kom igång med rollbaserad åtkomstkontroll i Azure portal](/azure/active-directory/role-based-access-control-what-is).
- [Använda rollbaserad åtkomstkontroll för att hantera åtkomst till resurserna i Azure-prenumeration](/azure/active-directory/role-based-access-control-configure).
- [Skapa anpassade roller för rollbaserad åtkomstkontroll i](/azure/active-directory/role-based-access-control-custom-roles).
- [Hantera rollbaserad åtkomstkontroll](azure-stack-manage-permissions.md) i Azure-stacken.


### <a name="authenticate-with-azure-powershell"></a>Autentisera med Azure PowerShell  
Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns på [konfigurera Azure Stack användarens PowerShell-miljö](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Autentisera med Azure CLI
Information om hur du använder Azure PowerShell för att autentisera med Azure Stack finns på [installera och konfigurera CLI för användning med Azure-stacken](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Nästa steg
- [Identity-arkitektur](azure-stack-identity-architecture.md)   
- [Datacenter-integrering - identitet](azure-stack-integrate-identity.md)




