---
title: Konfigurera Azure Active Directory för klientautentisering
description: Lär dig hur du konfigurerar Azure Active Directory (Azure AD) för att autentisera klienter för Service Fabric-kluster.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193396"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

För kluster som körs på Azure rekommenderas Azure Active Directory (Azure AD) för att skydda åtkomst till hanteringsslutpunkter. I den här artikeln beskrivs hur du konfigurerar Azure AD för att autentisera klienter för ett Service Fabric-kluster.

I den här artikeln används termen "program" för att referera till [Azure Active Directory-program](../active-directory/develop/developer-glossary.md#client-application), inte Service Fabric-program. distinktionen kommer att göras vid behov. Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program.

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer][service-fabric-visualizing-your-cluster] och [Visual Studio][service-fabric-manage-application-in-visual-studio]. Därför skapar du två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett inbyggt program. När programmen har skapats tilldelar du användare till skrivskyddade och administratörsroller.

> [!NOTE]
> På Linux måste du slutföra följande steg innan du skapar klustret. I Windows har du också möjlighet att [konfigurera Azure AD-autentisering för ett befintligt kluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Det är ett [känt problem](https://github.com/microsoft/service-fabric/issues/399) att program och noder på Linux AAD-aktiverade kluster inte kan visas i Azure Portal.



## <a name="prerequisites"></a>Krav
I den här artikeln förutsätter vi att du redan har skapat en klientorganisation. Om du inte har gjort det börjar du med att läsa avsnittet om [hur du skaffar en Azure Active Directory-klientorganisation][active-directory-howto-tenant].

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av de steg som används för att konfigurera Azure AD med Service Fabric-kluster.

1. [Klona reporäntan](https://github.com/Azure-Samples/service-fabric-aad-helpers) till datorn.
2. [Se till att du har alla förutsättningar](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) för skripten installerade.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Skapa Azure AD-program och tilldela användare till roller

Vi använder skripten för att skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett inbyggt program. När du har skapat program som ska representera klustret skapar du användare för de [roller som stöds av Service Fabric:](service-fabric-cluster-security-roles.md)skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar.  Ange även användarnamn och lösenord för användarna. Ett exempel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure Kina och Azure Tyskland) bör du även ange parametern `-Location`.

Du hittar din *TenantId* genom att `Get-AzureSubscription`köra PowerShell-kommandot . När du kör det här kommandot visas TenantId för varje prenumeration.

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsett att göra det enklare att mappa Azure AD-artefakter till det Service Fabric-kluster som de används med.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slutpunkten som service fabric Explorer-slutpunkt för klustret. Om du skapar Azure AD-program för att representera ett befintligt kluster kontrollerar du att den här URL:en matchar det befintliga klustrets slutpunkt. Om du skapar program för ett nytt kluster planerar du slutpunkten som klustret ska ha och se till att inte använda slutpunkten för ett befintligt kluster. Som standard är slutpunkten för Service Fabric Explorer:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klientorganisationen. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. Om du tittar på klientorganisationens program i [Azure-portalen][azure-portal] bör du se två nya poster:

   * *ClusterName-kluster*\_
   * *ClusterName-klient*\_

Skriptet skriver ut den JSON som krävs av Azure Resource Manager-mallen när du [skapar ditt AAD-aktiverat kluster,](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)så det är en bra idé att hålla PowerShell-fönstret öppet.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hjälp med felsökning kring att konfigurera Azure Active Directory
Det kan vara svårt att konfigurera Azure AD och använda det, så här är några tips på vad du kan göra för att felsöka problemet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer uppmanar dig att välja ett certifikat
#### <a name="problem"></a>Problem
När du har loggat in på Azure AD i Service Fabric Explorer återgår webbläsaren till startsidan, men ett meddelande uppmanar dig att välja ett certifikat.

![Dialogrutan SFX-certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Orsak
Användaren tilldelas inte en roll i Azure AD-klusterprogrammet. Azure AD-autentisering misslyckas därför i Service Fabric-klustret. Service Fabric Explorer återgår till certifikatautentisering.

#### <a name="solution"></a>Lösning
Följ instruktionerna för att konfigurera Azure AD och tilldela användarroller. Vi rekommenderar också att du aktiverar "Användartilldelning `SetupApplications.ps1` som krävs för att komma åt appen", precis som.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Anslutningen med PowerShell misslyckas med ett fel: "De angivna autentiseringsuppgifterna är ogiltiga"
#### <a name="problem"></a>Problem
När du använder PowerShell för att ansluta till klustret med hjälp av säkerhetsläget "AzureActiveDirectory" misslyckas anslutningen med ett fel: "De angivna autentiseringsuppgifterna är ogiltiga".

#### <a name="solution"></a>Lösning
Den här lösningen är densamma som den föregående.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer returnerar ett fel när du loggar in: "AADSTS50011"
#### <a name="problem"></a>Problem
När &lt;du försöker logga in på Azure AD i Service Fabric Explorer returnerar sidan ett fel: "AADSTS50011: Svarsadressen&gt; matchar inte svarsadresserna som konfigurerats för programmet: &lt;guid&gt;."

![SFX-svarsadressen matchar inte][sfx-reply-address-not-match]

#### <a name="reason"></a>Orsak
Klusterprogrammet (webb) som representerar Service Fabric Explorer försöker autentisera mot Azure AD, och som en del av begäran tillhandahåller det omdirigeringsretur-URL:en. Men URL:en visas inte i URL-listan för SVAR PÅ AZURE **AD-program.**

#### <a name="solution"></a>Lösning
På registreringssidan för Azure AD-appar för klustret väljer du **Autentisering**och lägger till URL:en för Service Fabric Explorer i listan under avsnittet **Omdirigera URI:er.** Spara din växel.

![URL för svar i webbprogram][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Att ansluta till klustret med Azure AD-autentisering via PowerShell ger ett felmeddelande när du loggar in: "AADSTS50011"
#### <a name="problem"></a>Problem
NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta till ett Service Fabric-kluster med Azure AD via PowerShell returneras ett fel på &lt;inloggningssidan: "AADSTS50011: Svars-URL:n som anges i begäran matchar inte svars-url:arna som konfigurerats fÃ¶r programmet: guid&gt;."

#### <a name="reason"></a>Orsak
I likhet med föregående problem försöker PowerShell autentisera mot Azure AD, som tillhandahåller en omdirigerings-URL som inte finns med i listan **Svarsadresser** för Azure AD-program.  

#### <a name="solution"></a>Lösning
Använd samma process som i föregående problem, men URL:en måste anges till `urn:ietf:wg:oauth:2.0:oob`, en särskild omdirigering för kommandoradsautentisering.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ansluta klustret med hjälp av Azure AD-autentisering via PowerShell
Om du vill ansluta Service Fabric-klustret använder du följande PowerShell-kommandoexempel:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Mer information finns i [Cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan jag återanvända samma Azure AD-klient i flera kluster?
Ja. Men kom ihåg att lägga till URL:en för Service Fabric Explorer i klusterprogrammet (webb). Annars fungerar inte Service Fabric Explorer.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Varför behöver jag fortfarande ett servercertifikat medan Azure AD är aktiverat?
FabricClient och FabricGateway utför en ömsesidig autentisering. Under Azure AD-autentisering tillhandahåller Azure AD-integrering en klientidentitet till servern och servercertifikatet används av klienten för att verifiera serverns identitet. Mer information om Service Fabric-certifikat finns i [X.509-certifikat och Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat Azure Active Directory-program och ange roller för användare [konfigurerar och distribuerar du ett kluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
