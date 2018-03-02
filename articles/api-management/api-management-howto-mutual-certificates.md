---
title: "Skydda backend-tjänster som använder klienten certifikatautentisering - Azure API Management | Microsoft Docs"
description: "Lär dig hur du skyddar backend-tjänster med hjälp av förautentisering av klientcertifikat i Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: afa5c6a07432456d703020c0b8c5c3606478b8fe
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Så här säkrar du backend-tjänster som använder klienten certifikatautentisering i Azure API Management
API Management ger möjlighet att säker åtkomst till backend-tjänst för en API som använder klientcertifikat. Den här guiden visar hur du hanterar certifikat i API publisher portal och hur du konfigurerar en API för att använda ett certifikat för åtkomst till dess backend-tjänst.

Information om hur du hanterar certifikat med hjälp av API Management REST API finns [Azure API Management REST API certifikat entiteten][Azure API Management REST API Certificate entity].

## <a name="prerequisites"> </a>Nödvändiga komponenter
Den här guiden visar hur du konfigurerar API Management service-instans om du vill använda klientcertifikatautentisering för åtkomst till backend-tjänst för ett API. Innan du följer stegen i det här avsnittet bör du ha en backend-tjänst som konfigurerats för autentisering av klientcertifikat ([så här konfigurerar du autentisering med datorcertifikat i Azure WebSites referera till den här artikeln][to configure certificate authentication in Azure WebSites refer to this article]), och har åtkomst till certifikatet och lösenordet för certifikatet för uppladdning i API Management publisher-portalen.

## <a name="step1"> </a>Ladda upp ett klientcertifikat
Börja genom att klicka på **Publisher-portal** på Azure Portal för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

![Utgivare för API-portalen][api-management-management-console]

> Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten][Create an API Management service instance].
> 
> 

Klicka på **säkerhet** från den **API Management** menyn till vänster och klicka på **klientcertifikat**.

![Klientcertifikat][api-management-security-client-certificates]

Om du vill överföra ett nytt certifikat klickar du på **överför certifikat**.

![Överför certifikat][api-management-upload-certificate]

Bläddra till ditt certifikat och ange lösenordet för certifikatet.

> Certifikatet måste vara i **.pfx** format. Självsignerade certifikat är tillåtna.
> 
> 

![Överför certifikat][api-management-upload-certificate-form]

Klicka på **överför** att ladda upp certifikatet.

> Lösenord för certifikatet verifieras just nu. Om det är felaktigt visas ett felmeddelande.
> 
> 

![Certifikatet har laddats upp][api-management-certificate-uploaded]

När certifikatet har överförts, visas det på den **klientcertifikat** fliken. Om du har flera certifikat att anteckna ämnet eller de sista fyra tecknen tumavtrycket för, som används för att välja certifikatet när du konfigurerar en API för att använda certifikat, som beskrivs i följande [konfigurera en API för att använda en klientcertifikat för gateway-autentisering] [ Configure an API to use a client certificate for gateway authentication] avsnitt.

> Följ stegen som beskrivs här om du vill inaktivera verifiering av certifikatkedjan när du använder, till exempel ett självsignerat certifikat, [objektet](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).
> 
> 

## <a name="step1a"> </a>Ta bort ett klientcertifikat
Om du vill ta bort ett certifikat klickar du på **ta bort** bredvid önskade certifikatet.

![Ta bort certifikat][api-management-certificate-delete]

Klicka på **Ja, ta bort den** att bekräfta.

![Bekräfta borttagning][api-management-confirm-delete]

Om certifikatet som används av en API, visas en varning skärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla API: er som är konfigurerade för att använda den.

![Bekräfta borttagning][api-management-confirm-delete-policy]

## <a name="step2"> </a>Konfigurera en API för att använda ett certifikat för gateway-autentisering
Klicka på **API: er** från den **API Management** menyn till vänster, klicka på namnet på den önskade API och klicka på den **säkerhet** fliken.

![API-säkerhet][api-management-api-security]

Välj **klientcertifikat** från den **med autentiseringsuppgifter** listrutan.

![Klientcertifikat][api-management-mutual-certificates]

Välj önskad certifikatet från den **klientcertifikat** listrutan. Om det finns flera certifikat tittar du på ett ämne eller de sista fyra tecknen tumavtrycket enligt beskrivningen i föregående avsnitt för att fastställa rätt certifikat.

![Välj certifikat][api-management-select-certificate]

Klicka på **spara** spara konfigurationsändringen-API: et.

> Den här ändringen börjar gälla omedelbart och anrop till åtgärder som API använder certifikatet för att autentisera på backend-servern.
> 
> 

![Spara ändringarna av API][api-management-save-api]

> När ett certifikat har angetts för gateway-autentisering för backend-tjänst för en API, blir en del av principen för den API och kan visas i Redigeraren för grupprincipobjekt.
> 
> 

![Certifikatprincip][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat, måste du inaktivera verifiering av certifikatkedjan för API-hantering att kommunicera med backend-systemet, annars en 500 felkod returneras. Om du vill konfigurera det här, kan du använda den [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (för nya serverdel) eller [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (för befintliga serverdel) PowerShell-cmdlets och ange den `-SkipCertificateChainValidation` parameter till `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



