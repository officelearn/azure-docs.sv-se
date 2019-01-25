---
title: Konfigurera Azure Active Directory för klientautentisering för Service Fabric | Microsoft Docs
description: Lär dig hur du ställer in Azure Active Directory (Azure AD) för att autentisera klienter för Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 691995d0aa426766caed2f5e2458399b32332c9d
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903510"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

För kluster som körs på Azure, rekommenderar Azure Active Directory (Azure AD) att säkra åtkomst till av hanteringsslutpunkter.  Den här artikeln beskrivs hur att konfigurera Azure AD för att autentisera klienter för Service Fabric-kluster som måste göras innan [skapar klustret](service-fabric-cluster-creation-via-arm.md).  Azure AD kan organisationer (kallas även klienter) för att hantera åtkomst till program. Program är indelade i dem med en webbaserad Användargränssnittet för inloggning och personer med en intern klient-upplevelse. I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har börjar med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Skapa Azure AD-program
Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.  När programmen har skapats kan du tilldela användare till skrivskyddat läge och administrativa roller.

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av stegen som ingår i Konfigurera Azure AD med Service Fabric-kluster.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar dig klusternamn och slutpunkter, värdena bör planeras och värden inte att du redan har skapat.

1. [Ladda ned skripten](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) till datorn.
2. Högerklicka på zipfilen, Välj **egenskaper**väljer den **avblockera** och klicka sedan på **tillämpa**.
3. Extrahera zip-filen.
4. Kör `SetupApplications.ps1`, och ange TenantId, klusternamn och WebApplicationReplyUrl som parametrar. Exempel:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
```

> [!NOTE]
> För nationella moln (Azure Government, Azure Kina Azure Tyskland), bör du också ange den `-Location` parametern.

Du hittar ditt TenantId genom att köra PowerShell-kommandot `Get-AzureSubscription`. Kör det här kommandot visar TenantId för varje prenumeration.

Klusternamn används som prefix i Azure AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsedd att göra det enklare att mappa Azure AD-artefakter till Service Fabric-klustret som de som används med.

WebApplicationReplyUrl är den standardslutpunkt som Azure AD returnerar till användarna när de Slutför inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilket som standard är:

https://&lt;cluster_domain&gt;:19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klient. När du har loggat in skapar skriptet webb- och interna program som motsvarar ditt Service Fabric-kluster. Om du tittar på klientens program i den [Azure-portalen][azure-portal], bör du se två nya poster:

   * *Klusternamn*\_kluster
   * *Klusternamn*\_klienten

Skriptet skriver ut den JSON som krävs av Azure Resource Manager-mallen när du skapar klustret i nästa avsnitt, så det är en bra idé att öppna PowerShell-fönstret.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret kan tilldela dina användare till roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela rollerna med hjälp av den [Azure-portalen][azure-portal].

1. Välj din klient i det övre högra hörnet i Azure-portalen.

    ![Välj knappen för klient][select-tenant-button]
2. Välj **Azure Active Directory** på den vänstra fliken och väljer ”program”.
3. Välj ”alla program”, och leta upp och välj webbprogram som har ett namn som `myTestCluster_Cluster`.
4. Klicka på den **användare och grupper** fliken.

    ![Fliken användare och grupper][users-and-groups-tab]
5. Klicka på den **Lägg till användare** knapp på den nya sidan, Välj en användare och rollen för att tilldela och klicka sedan på den **Välj** längst ned på sidan.

    ![Tilldela användare till roller sida][assign-users-to-roles-page]
6. Klicka på den **tilldela** längst ned på sidan.

    ![Lägg till tilldelning bekräftelse][assign-users-to-roles-confirm]

> [!NOTE]
> Mer information om roller i Service Fabric finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).
>
>

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

```PowerShell
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
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
