---
title: Azure API Management vanliga frågor och svar | Microsoft Docs
description: Lär dig svar på vanliga frågor och svar (FAQ), mönster och bästa praxis i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 5fe2cab3ac9f9e5f3bbba17b8ab9467a298b24ea
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817690"
---
# <a name="azure-api-management-faqs"></a>Azure API Management vanliga frågor och svar
Få svar på vanliga frågor, mönster och metodtips för Azure API Management.

## <a name="contact-us"></a>Kontakta oss
* [Hur kan jag ställa till Microsoft Azure API Management-teamet en fråga?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
* [Vad innebär det när en funktion är i förhandsversion?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hur kan jag skydda anslutningen mellan API Management-gatewayen och mina backend-tjänster?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Hur kopierar jag mina API Management-tjänstinstans till en ny instans?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Kan jag hantera mina API Management-instans via programmering?](#can-i-manage-my-api-management-instance-programmatically)
* [Hur gör jag för att lägga till en användare i gruppen Administratörer?](#how-do-i-add-a-user-to-the-administrators-group)
* [Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hur ställer jag in flera miljöer i ett enda API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan jag använda SOAP med API Management?](#can-i-use-soap-with-api-management)
* [Är API Management gateway IP-adress konstant? Kan jag använda den i brandväggsreglerna?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* Kan jag konfigurera en server för OAuth 2.0-auktorisering med AD FS-säkerhet?
* [Vilken routningsmetod använder API Management i distributioner till flera geografiska platser?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan jag använda en Azure Resource Manager-mall för att skapa en API Management-tjänstinstans?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan jag använda ett självsignerat SSL-certifikat för en serverdel?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Varför får jag ett autentiseringsfel när jag försöker klona en GIT-databas?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Fungerar API Management med Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Varför behöver vi ett dedikerat undernät i Resource Manager-style virtuella nätverk när API Management har distribuerats till dem?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Vad är den minsta undernät diskstorlek som behövs när du distribuerar API Management till ett virtuellt nätverk?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Kan jag flytta en API Management-tjänsten från en prenumeration till en annan?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Finns det begränsningar för eller kända problem med att importera Mina API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hur kan jag ställa till Microsoft Azure API Management-teamet en fråga?
Du kan kontakta oss genom att använda något av följande alternativ:

* Ställa frågor i våra [API Management MSDN-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Skicka ett e-postmeddelande till <mailto:apimgmt@microsoft.com>.
* Skicka en funktionsförfrågan den [Azures Feedbackforum](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Vad innebär det när en funktion är i förhandsversion?
När en funktion är i förhandsversion, innebär det att vi aktivt feedback på hur funktionen fungerar för dig. En funktion i förhandsversionen är funktionellt klar, men det är möjligt att vi ser till att en större ändra som svar på kundfeedback. Vi rekommenderar att du inte beroende av en funktion som är i förhandsversion i din produktionsmiljö. Om du har synpunkter på förhandsversionsfunktioner meddela oss via ett av Kontaktalternativ i [hur kan jag be Microsoft Azure API Management-teamet en fråga?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hur kan jag skydda anslutningen mellan API Management-gatewayen och Mina backend-tjänster?
Du har flera alternativ för att säkra anslutningar mellan API Management-gatewayen och backend-tjänster. Du kan:

* Använd grundläggande HTTP-autentisering. Mer information finns i [importera och publicera ditt första API](import-and-publish.md).
* Använda SSL ömsesidig autentisering som beskrivs i [hur du skyddar backend-tjänster med hjälp av klienten autentisering i Azure API Management](api-management-howto-mutual-certificates.md).
* Använd IP-vitlistning på backend-tjänst. Alla nivåer av API Management IP-adressen för gatewayen är konstant med några [varningar](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Du kan ange din lista över tillåtna för att tillåta den här IP-adress. Du kan hämta IP-adressen för din API Management-instans på instrumentpanelen i Azure-portalen.
* Anslut din API Management-instans till Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Hur kopierar jag mina API Management-tjänstinstans till en ny instans?
Du har flera alternativ om du vill kopiera en API Management-instans till en ny instans. Du kan:

* Säkerhetskopiera och återställa funktion i API Management. Mer information finns i [implementera haveriberedskap med hjälp av service-säkerhetskopiering och återställning i Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Skapa egna säkerhetskopior och återställa funktionen med hjälp av den [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Använda REST API för att spara och återställa entiteterna från den tjänstinstans som du vill.
* Hämta tjänstens konfiguration med hjälp av Git och ladda upp den till en ny instans. Mer information finns i [spara och konfigurera din konfiguration för API Management-tjänsten med hjälp av Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan jag hantera mina API Management-instans via programmering?
Ja, kan du hantera API Management via programmering med hjälp av:

* Den [API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* Den [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* Den [-tjänstdistribution](https://docs.microsoft.com/powershell/module/wds) och [Tjänstehantering](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell-cmdletar.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hur gör jag för att lägga till en användare i gruppen Administratörer?
Här är hur du kan lägga till en användare i gruppen Administratörer:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Gå till den resursgrupp som har API Management-instans som du vill uppdatera.
3. I API Management, tilldela den **Api Management deltagare** du användaren rollen.

Nu den nyligen tillagda deltagaren kan använda Azure PowerShell [cmdletar](https://docs.microsoft.com/powershell/azure/overview). Här är hur du loggar in som administratör:

1. Använd den `Connect-AzureRmAccount` cmdlet för att logga in.
2. Ange sedan kontexten till den prenumeration som har tjänsten med hjälp av `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Hämta en URL för enkel inloggning med `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Använd URL: en för att få åtkomst till administrationsportalen.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Varför är den princip som jag vill lägga till inte tillgänglig i principredigeraren?
Om den princip som du vill lägga till visas nedtonade eller skuggas i principredigeraren var noga med att du är i rätt omfattning för principen. Varje Principframställning har utformats för dig att använda specifika omfattningar och principen avsnitt. Principen avsnitt och omfång för en princip finns i avsnittet för användning av principens i [API Management-principer](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hur ställer jag in flera miljöer i ett enda API?
Om du vill konfigurera flera miljöer, till exempel en testmiljö och en produktionsmiljö i ett enda API har du två alternativ. Du kan:

* Värden olika API: er på samma klientorganisation.
* Vara värd för samma API: er på olika klienter.

### <a name="can-i-use-soap-with-api-management"></a>Kan jag använda SOAP med API Management?
[SOAP-direkt](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) stöd är nu tillgängligt. Administratörer kan importera WSDL av deras SOAP-tjänst och Azure API Management skapar en SOAP-klientdel. Portalen utvecklardokumentation, testkonsol, principer och analyser är tillgängliga för SOAP-tjänster.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Är API Management gateway IP-adress konstant? Kan jag använda den i brandväggsreglerna?
Alla nivåer av API Management är offentliga IP-adress (VIP) för API Management-klienten statiska för livslängden för klienten, med vissa undantag. IP-adress ändras under dessa omständigheter:

* Tjänsten tas bort och sedan skapas igen.
* Tjänstprenumeration är [pausats](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) eller [varnad](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (till exempel för en utebliven betalning) och sedan återupprättas.
* Du lägger till eller ta bort Azure-nätverk (du kan använda virtuellt nätverk endast på utvecklaren och Premium-nivån).

För distributioner över flera regioner, regionala adressen ändras om regionen vacated och sedan inbetalning (du kan använda distribution i flera regioner endast på Premium-nivån).

Premium-nivån-klienter som är konfigurerade för distribution i flera regioner har tilldelats en offentlig IP-adress per region.

Du kan hämta din IP-adress (eller adresserna i en distribution i flera regioner) på sidan klient i Azure-portalen.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan jag konfigurera en server för OAuth 2.0-auktorisering med AD FS-säkerhet?
Läs hur du konfigurerar en server för OAuth 2.0-auktorisering med Active Directory Federation Services (AD FS) säkerhet i [med hjälp av AD FS i API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Vilken routningsmetod använder API Management i distributioner till flera geografiska platser?
API Management används den [prestanda prestandatrafik](../traffic-manager/traffic-manager-routing-methods.md#performance) i distributioner till flera geografiska platser. Inkommande trafik dirigeras till närmaste API-gatewayen. Om en region kopplas från, dirigeras automatiskt inkommande trafik till nästa närmaste gatewayen. Mer information om metoder för trafikroutning i [routningsmetoder för Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan jag använda en Azure Resource Manager-mall för att skapa en API Management-tjänstinstans?
Ja. Se den [Azure API Management-tjänsten](https://aka.ms/apimtemplate) QuickStart-mallar.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Kan jag använda ett självsignerat SSL-certifikat för en serverdel?
Ja. Detta kan göras via PowerShell eller genom att skicka direkt till API: et. Detta kommer att inaktivera verifiering av certifikatkedjan och gör att du kan använda självsignerat eller privat-signerade certifikat vid kommunikation från API Management till backend-tjänster.

#### <a name="powershell-method"></a>PowerShell-metod ####
Använd den [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (för ny serverdel) eller [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (för befintliga serverdel) PowerShell-cmdlets och ange den `-SkipCertificateChainValidation` parameter `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Direkt metod för API-uppdatering ####
1. Skapa en [serverdel](https://msdn.microsoft.com/library/azure/dn935030.aspx) entitet med hjälp av API Management.       
2. Ange den **skipCertificateChainValidation** egenskap **SANT**.     
3. Om du inte längre vill att självsignerade certifikat, ta bort entiteten serverdelen eller ange den **skipCertificateChainValidation** egenskap **FALSKT**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Varför får jag ett autentiseringsfel när jag försöker klona en Git-databas?
Om du använder Git Credential Manager eller om du försöker klona en Git-lagringsplats med hjälp av Visual Studio kan du stöta på ett känt problem med dialogrutan Windows-autentiseringsuppgifter. Dialogrutan begränsar längd på lösenord till 127 tecken och den trunkerar Microsoft genererade lösenordet. Vi arbetar på att förkorta lösenordet. Använd Git Bash för att klona Git-lagringsplatsen för tillfället.

### <a name="does-api-management-work-with-azure-expressroute"></a>Fungerar API Management med Azure ExpressRoute?
Ja. API Management fungerar med Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Varför behöver vi ett dedikerat undernät i Resource Manager-style virtuella nätverk när API Management har distribuerats till dem?
Kravet på dedikerat undernät för API Management kommer från det faktum att den är byggd på distributionsmodell för klassisk (PAAS V1 layer). Vi kan distribuera i ett VNET i Resource Manager (V2 layer), finns men det andra konsekvenser som. Den klassiska distributionsmodellen i Azure är inte direkt kopplade till Resource Manager-modellen så om du skapar en resurs i V2 layer V1-lagret inte känner till den och problem kan inträffa, t.ex API Management som försöker använda en IP-adress som redan har tilldelats till ett nätverkskort  (bygger på V2).
Lär dig mer om skillnaden mellan klassiska och Resource Manager-modeller i Azure finns på [skillnaden i distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Vad är den minsta undernät diskstorlek som behövs när du distribuerar API Management till ett virtuellt nätverk?
Den minsta undernät krävs för att distribuera API Management är [/29](../virtual-network/virtual-networks-faq.md#configuration), vilket är den minsta undernät storleken som stöds av Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Kan jag flytta en API Management-tjänsten från en prenumeration till en annan?
Ja. Läs hur genom att läsa [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Finns det begränsningar för eller kända problem med att importera Mina API?
[Kända problem och begränsningar](api-management-api-import-restrictions.md) för öppna API(Swagger) WSDL och WADL-format.
