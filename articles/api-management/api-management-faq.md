---
title: "Azure API Management vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor och svar (FAQ) mönster och bästa praxis i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 1903655a262583f1ba78b728bf404a81278e2275
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-api-management-faqs"></a>Azure API Management vanliga frågor och svar
Få svar på vanliga frågor, mönster och bästa praxis för Azure API Management.

## <a name="contact-us"></a>Kontakta oss
* [Hur kan jag fråga Microsoft Azure API Management-teamet?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* [Vad innebär det när en funktion i förhandsversionen?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hur kan jag skydda anslutningen mellan API Management gateway och mitt backend-tjänster?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hur kopierar min API Management service-instans till en ny instans?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kan jag hantera min API Management-instans genom programmering?](#can-i-manage-my-api-management-instance-programmatically)
* [Hur lägger jag till en användare i gruppen Administratörer?](#how-do-i-add-a-user-to-the-administrators-group)
* [Varför är den princip som du vill lägga till inte tillgänglig i Redigeraren för grupprincipobjekt?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hur ställer jag in flera miljöer i ett enda API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan jag använda SOAP med API Management?](#can-i-use-soap-with-api-management)
* [Är IP-adressen konstant för API Management gateway? Kan jag använda den i brandväggsregler?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Kan jag konfigurera en server för OAuth 2.0-auktorisering med AD FS-säkerhet?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Vilka routningsmetoden använder API-hantering i distributioner till flera geografiska platser?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan jag använda en Azure Resource Manager-mall för att skapa en instans för API Management-tjänsten?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan jag använda ett självsignerat certifikat för SSL för en serverdel?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Varför visas ett autentiseringsfel när jag försöker klona en GIT-lagringsplats?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Fungerar API-hantering med Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Varför behöver vi en dedikerad undernät i hanteraren för filserverresurser style Vnet när API Management har distribuerats till dem?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Vad är den minsta undernät storlek som krävs vid distribution av API-hantering i ett virtuellt nätverk?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kan jag flytta en API Management-tjänst från en prenumeration till en annan?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Finns det begränsningar eller kända problem med att importera min API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hur kan jag fråga Microsoft Azure API Management-teamet?
Du kan kontakta oss genom att använda något av följande alternativ:

* Dina frågor i våra [API Management MSDN-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Skicka ett e-postmeddelande till < mailto:apimgmt@microsoft.com >.
* Skicka en funktionsbegäran den [Azure Feedbackforum](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Vad innebär det när en funktion i förhandsversionen?
När det är en funktion i förhandsversionen, innebär det att vi aktivt som begär feedback på hur funktionen fungerar för dig. En funktion i förhandsversionen är funktionellt klar, men det är möjligt att vi ska skapa en ny ändring i följd av kundfeedback. Vi rekommenderar att du inte beroende av en funktion i förhandsversionen i produktionsmiljön. Om du har feedback på förhandsgranskningsfunktioner berätta genom en Kontaktalternativ i [hur kan jag fråga Microsoft Azure API Management-teamet?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hur kan jag skydda anslutningen mellan API Management gateway och mitt backend-tjänster?
Du har flera alternativ för att skydda anslutningen mellan API Management gateway och backend-tjänster. Du kan:

* Använd grundläggande HTTP-autentisering. Mer information finns i [Import och publicera din första API](import-and-publish.md).
* Använda SSL ömsesidig autentisering som beskrivs i [säkra backend-tjänster med hjälp av klienten certifikatautentisering i Azure API Management](api-management-howto-mutual-certificates.md).
* Använd IP-vitlistning på backend-tjänst. I alla nivåer av API-hantering, IP-adressen för gateway konstant, med några [varningar](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Du kan ange din godkända så att denna IP-adress. Du kan hämta IP-adressen för din API Management-instans på instrumentpanelen i Azure-portalen.
* Anslut din API Management-instans till ett Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hur kopierar min API Management service-instans till en ny instans?
Om du vill kopiera en API Management-instans till en ny instans har du flera alternativ. Du kan:

* Säkerhetskopiera och återställa funktionen i API-hantering. Mer information finns i [implementera katastrofåterställning genom att använda tjänsten säkerhetskopiering och återställning i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Skapa din egen säkerhetskopia och återställa funktionen med hjälp av den [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Använda REST API för att spara och återställa enheterna från service-instans som du vill.
* Hämta konfigurationen för tjänsten med hjälp av Git och överföra den till en ny instans. Mer information finns i [hur du sparar och konfigurera din konfiguration för API Management-tjänsten med hjälp av Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jag hantera min API Management-instans genom programmering?
Ja, kan du hantera API-hantering via programmering med hjälp av:

* Den [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* Den [bibliotek för Microsoft Azure Service ApiManagement SDK](http://aka.ms/apimsdk).
* Den [Tjänstedistributionen](https://msdn.microsoft.com/library/mt619282.aspx) och [Service management](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell-cmdlets.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hur lägger jag till en användare i gruppen Administratörer?
Här är hur du lägger till en användare i gruppen Administratörer:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till den resursgrupp som har API Management-instans som du vill uppdatera.
3. I API-hantering, tilldela den **Api Management deltagare** du användaren rollen.

Den nyligen tillagda deltagaren kan nu använda Azure PowerShell [cmdlets](https://msdn.microsoft.com/library/mt613507.aspx). Här är hur du loggar in som administratör:

1. Använd den `Login-AzureRmAccount` för att logga in.
2. Ange sedan kontexten för den prenumeration som har tjänsten med hjälp av `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Få en URL för enkel inloggning med hjälp av `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Använda Webbadressen för att få åtkomst till administrationsportal.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Varför är den princip som du vill lägga till inte tillgänglig i Redigeraren för grupprincipobjekt?
Om den princip som du vill lägga till visas nedtonat eller skuggade i Redigeraren för grupprincipobjekt, Tänk på att du är i rätt omfattning för principen. Varje Principframställning är utformad att använda specifika omfattningar och principen avsnitt. Om du vill granska princip avsnitt och scope för en princip finns principens användning i [API Management-principer](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hur ställer jag in flera miljöer i ett enda API?
Om du vill konfigurera flera miljöer, till exempel en testmiljö och en produktionsmiljö i ett enda API har du två alternativ. Du kan:

* Värden olika API: er på samma klientorganisation.
* Värd för samma API: er på olika klienter.

### <a name="can-i-use-soap-with-api-management"></a>Kan jag använda SOAP med API Management?
[SOAP-direkt](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) stöd är nu tillgänglig. Administratörer kan importera WSDL av deras SOAP-tjänst och Azure API Management skapar en SOAP-klientdel. Portalen utvecklardokumentationen, test-konsolen, principer och analytics är tillgängliga för SOAP-tjänster.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Är IP-adressen konstant för API Management gateway? Kan jag använda den i brandväggsregler?
I alla nivåer för API Management är offentliga IP-adressen (VIP) för API Management-klienten statiska för livslängden för klienten, med vissa undantag. IP-adress ändras under dessa omständigheter:

* Tjänsten har tagits bort och sedan återskapas.
* Prenumerationen på tjänsten är [avbruten](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) eller [varnad](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (t.ex, för nonpayment) och sedan återupprättas.
* Du lägger till eller ta bort Azure Virtual Network (du kan använda virtuella nätverk på utvecklaren och Premium-nivån).

För distributioner med flera regioner, regionala adressen ändras om regionen vacated och sedan återupprättas (du kan använda flera regioner distribution endast i Premium-nivån).

Premium-nivån klienter som är konfigurerade för distribution av flera regioner har tilldelats en offentlig IP-adress per region.

Du kan hämta IP-adressen (eller adresserna i en distribution med flera regioner) på sidan klient i Azure-portalen.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jag konfigurera en server för OAuth 2.0-auktorisering med AD FS-säkerhet?
Information om hur du konfigurerar en server för OAuth 2.0-auktorisering med Active Directory Federation Services (AD FS) säkerhet finns [med hjälp av AD FS i API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Vilka routningsmetoden använder API-hantering i distributioner till flera geografiska platser?
API Management används den [prestanda trafikroutningsmetoden](../traffic-manager/traffic-manager-routing-methods.md#priority) i distributioner till flera geografiska platser. Inkommande trafik dirigeras till den närmaste API-gatewayen. Om en region frånkopplas dirigeras automatiskt inkommande trafik till nästa närmaste gateway. Mer information om metoder i [routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jag använda en Azure Resource Manager-mall för att skapa en instans för API Management-tjänsten?
Ja. Finns det [Azure API Management-tjänsten](http://aka.ms/apimtemplate) snabbstartsmallar.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Kan jag använda ett självsignerat certifikat för SSL för en serverdel?
Ja. Detta kan göras med hjälp av PowerShell eller genom direkt att skicka-API: et. Detta inaktiverar verifiering av certifikatkedjan och gör att du kan använda självsignerat eller privat signerat certifikat vid kommunikation från API-hantering till backend-tjänster.

#### <a name="powershell-method"></a>PowerShell-metoden ####
Använd den [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (för nya serverdel) eller [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (för befintliga serverdel) PowerShell-cmdlets och ange den `-SkipCertificateChainValidation` parameter till `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Direkt metod för API-uppdatering ####
1. Skapa en [Backend](https://msdn.microsoft.com/library/azure/dn935030.aspx) entiteten med hjälp av API-hantering.       
2. Ange den **skipCertificateChainValidation** egenskapen **SANT**.     
3. Om du inte längre vill att självsignerade certifikat, ta bort entiteten Backend eller ange den **skipCertificateChainValidation** egenskapen **FALSKT**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Varför visas ett autentiseringsfel när jag försöker klona en Git-lagringsplats?
Om du använder Git Autentiseringshanteraren eller om du försöker klona en Git-lagringsplats med hjälp av Visual Studio kan du stöta på ett känt problem med dialogrutan Windows-autentiseringsuppgifter. Dialogrutan begränsar längd på lösenord till 127 tecken och Microsoft-genererade lösenordet trunkeras. Vi arbetar på förkorta lösenordet. Nu är att använda Git Bash för att klona Git-lagringsplatsen.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerar API-hantering med Azure ExpressRoute?
Ja. API Management fungerar med Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Varför behöver vi en dedikerad undernät i hanteraren för filserverresurser style Vnet när API Management har distribuerats till dem?
Kravet på dedikerade undernät för API-hantering kommer från det faktum att den bygger på modell-distribution (PAAS V1 layer). Vi kan distribuera till Resource Manager-VNET (V2 layer), finns men det konsekvenser som. Den klassiska distributionsmodellen i Azure direkt kopplade inte till Resource Manager-modellen och så om du skapar en resurs i V2 layer V1-lagret inte känner till den och problem kan inträffa, till exempel API-hantering som försöker använda en IP-adress som redan har allokerats till ett nätverkskort  (bygger på V2).
Läs mer om skillnaden mellan klassisk och Resource Manager modeller i Azure avser [skillnad i distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Vad är den minsta undernät storlek som krävs vid distribution av API-hantering i ett virtuellt nätverk?
Minsta undernätets storlek som krävs för att distribuera API-hantering är [/29](../virtual-network/virtual-networks-faq.md#configuration), vilket är minsta undernätets storlek som har stöd för Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jag flytta en API Management-tjänst från en prenumeration till en annan?
Ja. Mer information finns i avsnittet [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Finns det begränsningar eller kända problem med att importera min API?
[Kända problem och begränsningar](api-management-api-import-restrictions.md) öppna API(Swagger) WSDL och WADL format.
