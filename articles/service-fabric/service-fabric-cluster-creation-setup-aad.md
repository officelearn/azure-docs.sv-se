---
title: Konfigurera Azure Active Directory för klientautentisering för Service Fabric | Microsoft Docs
description: Lär dig hur du ställer in Azure Active Directory (Azure AD) för att autentisera klienter för Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: 132609529fbeda9b6dbd76a3ef6c824e84c15164
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670769"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

För kluster som körs på Azure, rekommenderar Azure Active Directory (Azure AD) att säkra åtkomst till av hanteringsslutpunkter.  Den här artikeln beskrivs hur att konfigurera Azure AD för att autentisera klienter för Service Fabric-kluster som måste göras innan [skapar klustret](service-fabric-cluster-creation-via-arm.md).  Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. Program är indelade i dem med en webbaserad Användargränssnittet för inloggning och personer med en intern klient-upplevelse. 

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.  När programmen har skapats tilldelar du användare till roller som skrivskyddad och administratör.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar sig klusternamn och slutpunkter bör värdena vara planerade och inte värden som du redan har skapat.

## <a name="prerequisites"></a>Förutsättningar
I den här artikeln förutsätter vi att du redan har skapat en klientorganisation. Om du inte har börjar med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av de steg som används för att konfigurera Azure AD med Service Fabric-kluster.

1. [Ladda ned skripten](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) till datorn.
2. Högerklicka på zipfilen, Välj **egenskaper**väljer den **avblockera** och klicka sedan på **tillämpa**.
3. Extrahera zip-filen.

## <a name="create-azure-ad-applications-and-asssign-users-to-roles"></a>Skapa Azure AD-program och asssign användare till roller
Skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program. När du har skapat programmen för att representera klustret tilldelar du dina användare till de [roller som stöds av Service Fabric](service-fabric-cluster-security-roles.md): skrivskyddad och administratör.

Kör `SetupApplications.ps1` och ange klientorganisations-ID, klusternamn och svars-URL för webbprogram som parametrar.  Ange även användarnamn och lösenord för användarna.  Exempel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> För nationella moln (till exempel Azure Government, Azure Kina och Azure Tyskland) bör du även ange parametern `-Location`.

Du kan hitta din *TenantId* genom att köra PowerShell-kommandot `Get-AzureSubscription`. Kör det här kommandot visar TenantId för varje prenumeration.

*ClusterName* (Klusternamn) används för att prefigera de AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsett att göra det enklare att mappa Azure AD-artefakter till det Service Fabric-kluster som de används med.

*WebApplicationReplyUrl* är den standardslutpunkt som Azure AD returnerar till dina användare när de har slutfört inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilken som standard är:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klientorganisationen. När du har loggat in skapar skriptet webbprogrammet och det interna programmet för att representera ditt Service Fabric-kluster. Om du tittar på klientens program i den [Azure-portalen][azure-portal], bör du se två nya poster:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Skriptet skriver ut den JSON som krävs av Azure Resource Manager-mallen när du [skapa klustret](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), så det är en bra idé att öppna PowerShell-fönstret.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hjälp att konfigurera Azure Active Directory med felsökning
Konfigurera Azure AD och använda det kan vara en utmaning, så här följer några tips på vad du kan göra för att felsöka problemet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer uppmanar dig att välja ett certifikat
#### <a name="problem"></a>Problem
När du har loggat in har till Azure AD i Service Fabric Explorer returneras från webbläsaren till startsidan, men ett meddelande som uppmanar dig att välja ett certifikat.

![Dialogrutan för SFX-certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Orsak
Användaren är inte tilldelad en roll i Azure AD-programmet för klustret. Azure AD-autentisering går därför inte i Service Fabric-kluster. Service Fabric Explorer faller tillbaka till autentisering med datorcertifikat.

#### <a name="solution"></a>Lösning
Följ anvisningarna för att konfigurera Azure AD och tilldela användarroller. Dessutom rekommenderar vi att du aktiverar ”Användartilldelning krävs för att få åtkomst till appen”, som `SetupApplications.ps1` har.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Anslutning med PowerShell misslyckas med felmeddelandet: ”De angivna autentiseringsuppgifterna är ogiltiga”
#### <a name="problem"></a>Problem
När du använder PowerShell för att ansluta till klustret med hjälp av läget för ”AzureActiveDirectory” säkerhet när du loggar in har till Azure AD, misslyckas om anslutningen med felmeddelandet: ”De angivna autentiseringsuppgifterna är ogiltiga”.

#### <a name="solution"></a>Lösning
Den här lösningen är samma som det föregående.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer returnerar ett fel när du loggar in: "AADSTS50011"
#### <a name="problem"></a>Problem
När du försöker logga in på Azure AD i Service Fabric Explorer returnerar sidan ett fel: "AADSTS50011: Svarsadressen &lt;url&gt; matchar inte svars-adresser som har konfigurerats för programmet: &lt;guid&gt;”.

![SFX svarsadressen matchar inte][sfx-reply-address-not-match]

#### <a name="reason"></a>Orsak
Klustret (webb)-program som motsvarar Service Fabric Explorer försöker autentisera mot Azure AD och som en del av begäran ger den returnera omdirigerings-URL. Men URL: en har inte listats i Azure AD-programmet **SVARS-URL** lista.

#### <a name="solution"></a>Lösning
Välj ”appregistreringar” i AAD-sidan, Välj ditt program i klustret och välj sedan den **Svarswebbadresser** knappen. Lägg till URL: en för Service Fabric Explorer i listan på sidan ”svars-URL: er eller ersätta ett av objekten i listan. När du är klar kan du spara ändringarna.

![Web application svars-url][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ansluta till klustret med hjälp av Azure AD-autentisering via PowerShell
Använd följande PowerShell-exemplet för att ansluta Service Fabric-klustret:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Mer information finns i [cmdleten Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan jag återanvända samma Azure AD-klienten i flera kluster?
Ja. Men kom ihåg att lägga till URL: en för Service Fabric Explorer i ditt program i klustret (webb). I annat fall fungerar Service Fabric Explorer inte.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Varför jag fortfarande behöver ett certifikat medan Azure AD är aktiverat?
FabricClient och FabricGateway utför du en ömsesidig autentisering. Azure AD-integrering ger en klientens identitet till servern under Azure AD-autentisering och certifikatet används för att verifiera serveridentitet. Läs mer om Service Fabric-certifikat, [X.509-certifikat och Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nästa steg
När du har installerat Azure Active Directory-program och inställningen användarroller [konfigurera och distribuera ett kluster](service-fabric-cluster-creation-via-arm.md).


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
