---
title: Vanliga frågor och svar om Azure API Management | Microsoft-dokument
description: Lär dig svaren på vanliga frågor, mönster och metodtips i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335978"
---
# <a name="azure-api-management-faqs"></a>Vanliga frågor och frågor om Azure API Management
Få svar på vanliga frågor, mönster och metodtips för Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Kontakta oss
* [Hur ställer jag en fråga till Microsoft Azure API Management-teamet?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* [Vad betyder det när en funktion förhandsgranskas?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kan jag hantera min API Management-instans programmässigt?](#can-i-manage-my-api-management-instance-programmatically)
* [Hur gör jag för att lägga till en användare i gruppen Administratörer?](#how-do-i-add-a-user-to-the-administrators-group)
* [Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hur konfigurerar jag flera miljöer i ett enda API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan jag använda SOAP med API Management?](#can-i-use-soap-with-api-management)
* [Kan jag konfigurera en OAuth 2.0-auktoriseringsserver med AD FS-säkerhet?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Vilken routningsmetod använder API Management i distributioner till flera geografiska platser?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan jag använda en Azure Resource Manager-mall för att skapa en API Management-tjänstinstans?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan jag använda ett självsignerat TLS/SSL-certifikat för en serverdel?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Varför får jag ett autentiseringsfel när jag försöker klona en GIT-databas?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Fungerar API Management med Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Varför kräver vi ett dedikerat undernät i Resource Manager-stil VNETs när API Management distribueras till dem?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Vilken är den minsta undernätsstorlek som behövs när API-hantering distribueras till ett virtuella nätverk?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kan jag flytta en API Management-tjänst från en prenumeration till en annan?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Finns det begränsningar för eller kända problem med att importera mitt API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hur ställer jag en fråga till Microsoft Azure API Management-teamet?
Du kan kontakta oss genom att använda något av följande alternativ:

* Skicka dina frågor i vår [API Management MSDN forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Skicka ett e-postmeddelande till <mailto:apimgmt@microsoft.com>.
* Skicka oss en funktionsförfrågan i [Azure feedback-forumet](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Vad betyder det när en funktion förhandsgranskas?
När en funktion är i förhandsversion innebär det att vi aktivt söker feedback om hur funktionen fungerar för dig. En funktion i förhandsversionen är funktionellt klar, men det är möjligt att vi gör en breaking change som svar på feedback från kunder. Vi rekommenderar att du inte är beroende av en funktion som förhandsgranskas i produktionsmiljön. Om du har några synpunkter på förhandsversionsfunktioner kan du meddela oss via något av kontaktalternativen i [Hur kan jag ställa en fråga till Microsoft Azure API Management-teamet?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?
Du har flera alternativ för att skydda anslutningen mellan API Management-gatewayen och backend-tjänsterna. Du kan:

* Använd HTTP grundläggande autentisering. Mer information finns i [Importera och publicera ditt första API](import-and-publish.md).
* Använd TLS-ömsesidig autentisering enligt beskrivningen i [Så här skyddar du backend-tjänster med hjälp av klientcertifikatautentisering i Azure API Management](api-management-howto-mutual-certificates.md).
* Använd IP-vitlistning på din backend-tjänst. I alla nivåer av API Management, med undantag för förbrukningsnivå, förblir IP-adressen för gatewayen konstant, med några varningar som beskrivs i [IP-dokumentationsartikeln](api-management-howto-ip-addresses.md).
* Anslut DIN API Management-instans till ett Virtuellt Azure-nätverk.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hur gör jag för att kopiera min API Management-tjänstinstans till en ny instans?
Du har flera alternativ om du vill kopiera en API Management-instans till en ny instans. Du kan:

* Använd funktionen säkerhetskopiering och återställning i API Management. Mer information finns i Så här implementerar du [haveriberedskap med hjälp av säkerhetskopiering och återställning av tjänster i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Skapa din egen säkerhetskopierings- och återställningsfunktion med hjälp av [API Management REST API](/rest/api/apimanagement/). Använd REST API för att spara och återställa entiteterna från den tjänstinstans som du vill använda.
* Hämta tjänstkonfigurationen med Git och ladda sedan upp den till en ny instans. Mer information finns i [Så här sparar och konfigurerar du api-hanteringstjänstkonfigurationen med Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jag hantera min API Management-instans programmässigt?
Ja, du kan hantera API Management programmässigt med hjälp av:

* [REST-API:et för API-hantering](/rest/api/apimanagement/).
* [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* PowerShell-cmdlets för [tjänstdistribution](https://docs.microsoft.com/powershell/module/wds) och [tjänsthantering.](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hur gör jag för att lägga till en användare i gruppen Administratörer?
Så här kan du lägga till en användare i gruppen Administratörer:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Gå till resursgruppen som har den API Management-instans som du vill uppdatera.
3. I API Management tilldelar du rollen **Api Management Service Contributor** till användaren.

Nu kan den nyligen tillagda bidragsgivaren använda Azure [PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview). Så här loggar du in som administratör:

1. Använd `Connect-AzAccount` cmdleten för att logga in.
2. Ange kontexten till den prenumeration `Set-AzContext -SubscriptionID <subscriptionGUID>`som har tjänsten med hjälp av .
3. Få en enda inloggnings-URL med hjälp `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`av .
4. Använd URL:en för att komma åt administratörsportalen.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?
Om principen som du vill lägga till visas nedtonad eller skuggad i principredigeraren måste du se till att du befinner dig i rätt omfång för principen. Varje principsats är utformad för att du ska kunna använda i specifika scope och principavsnitt. Om du vill granska principavsnitten och scope för en princip läser du avsnittet Användning i [API Management-principer](/azure/api-management/api-management-policies).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hur konfigurerar jag flera miljöer i ett enda API?
Om du vill ställa in flera miljöer, till exempel en testmiljö och en produktionsmiljö, i ett enda API, har du två alternativ. Du kan:

* Vara värd för olika API:er på samma klient.
* Vara värd för samma API:er på olika klienter.

### <a name="can-i-use-soap-with-api-management"></a>Kan jag använda SOAP med API Management?
[SOAP-vidarestöd](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) finns nu tillgängligt. Administratörer kan importera WSDL för sin SOAP-tjänst och Azure API Management skapar en SOAP-klientdel. Dokumentation av utvecklarportalen, testkonsolen, policyerna och analyserna är alla tillgängliga för SOAP-tjänster.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jag konfigurera en OAuth 2.0-auktoriseringsserver med AD FS-säkerhet?
Mer information om hur du konfigurerar en OAuth 2.0-auktoriseringsserver med AD FS-säkerhet (Active Directory Federation Services) finns [i Använda ADFS i API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Vilken routningsmetod använder API Management i distributioner till flera geografiska platser?
API Management använder [routningsmetoden för prestandatrafik](../traffic-manager/traffic-manager-routing-methods.md#performance) i distributioner till flera geografiska platser. Inkommande trafik dirigeras till närmaste API-gateway. Om en region kopplas från dirigeras inkommande trafik automatiskt till nästa närmaste gateway. Läs mer om routningsmetoder i [Routningsmetoder för Trafikhanteraren](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jag använda en Azure Resource Manager-mall för att skapa en API Management-tjänstinstans?
Ja. Se snabbstartsmallarna för [Azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan jag använda ett självsignerat TLS/SSL-certifikat för en serverdel?
Ja. Detta kan göras via PowerShell eller genom att skicka direkt till API:et. Detta inaktiverar validering av certifikatkedja och gör att du kan använda självsignerade eller privat signerade certifikat när du kommunicerar från API Management till serverdelstjänsterna.

#### <a name="powershell-method"></a>Powershell-metod ####
Använd [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (för ny backend) eller [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (för befintliga backendlar) `-SkipCertificateChainValidation` PowerShell-cmdlets och ställ in parametern på `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Direkt API-uppdateringsmetod ####
1. Skapa en [serverdentitet](/rest/api/apimanagement/) med hjälp av API Management.     
2. Ange egenskapen **skipCertificateChainValidation** till **true**.     
3. Om du inte längre vill tillåta självsignerade certifikat tar du bort serverdatiteten eller anger egenskapen **skipCertificateChainValidation** till **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Varför visas ett autentiseringsfel när jag försöker klona en Git-databas?
Om du använder Git-autentiseringshanteraren, eller om du försöker klona en Git-databas med Visual Studio, kan du stöta på ett känt problem med dialogrutan Windows-autentiseringsuppgifter. Dialogrutan begränsar lösenordslängden till 127 tecken och trunkerar det Microsoft-genererade lösenordet. Vi arbetar på att förkorta lösenordet. För nu, använd Git Bash för att klona din Git-lagringsplats.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerar API Management med Azure ExpressRoute?
Ja. API Management fungerar med Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Varför kräver vi ett dedikerat undernät i Resource Manager-stil VNETs när API Management distribueras till dem?
Det dedikerade undernätskravet för API Management kommer från det faktum att det bygger på den klassiska (PAAS V1-lager) distributionsmodellen. Även om vi kan distribuera till ett Resource Manager VNET (V2-lager), det finns konsekvenser för det. Den klassiska distributionsmodellen i Azure är inte tätt kopplad till Resource Manager-modellen och så om du skapar en resurs i V2-lager, vet V1-lagret inte om det och problem kan inträffa, till exempel API Management som försöker använda en IP som redan har allokerats till ett nätverkskort ( bygger på V2).
Mer information om skillnaden mellan Klassiska och Resource Manager-modeller i Azure finns [i skillnaden i distributionsmodeller](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Vilken är den minsta undernätsstorlek som behövs när API-hantering distribueras till ett virtuella nätverk?
Den minsta undernätsstorlek som krävs för att distribuera API Management är [/29](../virtual-network/virtual-networks-faq.md#configuration), vilket är den minsta undernätsstorlek som Azure stöder.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jag flytta en API Management-tjänst från en prenumeration till en annan?
Ja. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Finns det begränsningar för eller kända problem med att importera mitt API?
[Kända problem och begränsningar](api-management-api-import-restrictions.md) för Open API(Swagger), WSDL- och WADL-format.
