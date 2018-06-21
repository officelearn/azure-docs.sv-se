---
title: Skydda backend-tjänster som använder klienten certifikatautentisering - Azure API Management | Microsoft Docs
description: Lär dig hur du skyddar backend-tjänster med hjälp av förautentisering av klientcertifikat i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 844a7ea1c2dd8f7dbb4984fc148575529ac154db
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292867"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Så här säkrar du backend-tjänster som använder klienten certifikatautentisering i Azure API Management

API-hantering kan få säker åtkomst till backend-tjänst för en API som använder klientcertifikat. Den här guiden visar hur du hanterar certifikat i Azure API Management service-instans i Azure-portalen. Det förklarar också hur du konfigurerar en API för att använda ett certifikat för åtkomst till en backend-tjänst.

Information om hur du hanterar certifikat med hjälp av API Management REST API finns <a href="https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API certifikat entiteten</a>.

## <a name="prerequisites"> </a>Nödvändiga komponenter

Den här guiden visar hur du konfigurerar API Management service-instans om du vill använda klientcertifikatautentisering för åtkomst till backend-tjänst för ett API. Innan du följer stegen i den här artikeln bör du ha en backend-tjänst som konfigurerats för autentisering av klientcertifikat ([så här konfigurerar du autentisering med datorcertifikat i Azure WebSites referera till den här artikeln] [ to configure certificate authentication in Azure WebSites refer to this article]). Du behöver åtkomst till certifikatet och lösenordet för att överföra den till API Management-tjänsten.

## <a name="step1"> </a>Ladda upp ett klientcertifikat

![Lägg till certifikat](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Följ stegen nedan för att ladda upp ett nytt klientcertifikat. Om du inte har skapat en instans för API Management-tjänsten, se självstudierna [skapa en instans för API Management-tjänsten][Create an API Management service instance].

1. Gå till Azure API Management service-instans i Azure-portalen.
2. Välj **klientcertifikat** på menyn.
3. Klicka på den **+ Lägg till** knappen.  
    ![Lägg till certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Bläddra efter certifikatet, ange dess ID och lösenord.  
5. Klicka på **Skapa**.

> [!NOTE]
> Certifikatet måste vara i **.pfx** format. Självsignerade certifikat är tillåtna.

När certifikatet har överförts visas i den **klientcertifikat**.  Om du har många certifikat anteckna tumavtrycket för det önskade certifikatet för att [konfigurera en API för att använda ett certifikat för gateway-autentisering][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Följ stegen som beskrivs här om du vill inaktivera verifiering av certifikatkedjan när du använder, till exempel ett självsignerat certifikat, [objektet](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Ta bort ett klientcertifikat

Om du vill ta bort ett certifikat klickar du på snabbmenyn **...**  och välj **ta bort** bredvid certifikatet.

![Ta bort klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Om certifikatet som används av en API, visas en varning skärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla API: er som är konfigurerade för att använda den.

![Ta bort certifikat klientfel](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Konfigurera en API för att använda ett certifikat för gateway-autentisering

1. Klicka på **API: er** från den **API Management** menyn till vänster och navigera till API: et.  
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. I den **Design** klickar du på en pennikonen i den **Backend** avsnitt. 
3. Ändra den **Gateway-autentiseringsuppgifter** till **klienten cert** och välja certifikatet i listrutan.  
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klicka på **Spara**. 

> [!WARNING]
> Den här ändringen börjar gälla omedelbart och anrop till åtgärder som API använder certifikatet för att autentisera på backend-servern.


> [!TIP]
> När ett certifikat har angetts för gateway-autentisering för backend-tjänst för en API, blir en del av principen för den API och kan visas i Redigeraren för grupprincipobjekt.

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat måste du inaktivera verifiering av certifikatkedjan för API-hantering att kommunicera med backend-systemet. Annars returneras ett fel 500-kod. Om du vill konfigurera det här, kan du använda den [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (för nya serverdel) eller [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (för befintliga serverdel) PowerShell-cmdlets och ange den `-SkipCertificateChainValidation` parameter till `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

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
