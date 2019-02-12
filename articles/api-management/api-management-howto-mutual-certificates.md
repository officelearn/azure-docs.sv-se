---
title: Säkra serverdelstjänster med hjälp av klienten certifikatautentisering – Azure API Management | Microsoft Docs
description: Lär dig hur du skyddar backend-tjänster med hjälp av autentisering av klientcertifikat i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: b8f8d0a089a74334a908e3dad65c63231bbe5975
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098943"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Hur du skyddar backend-tjänster med hjälp av klienten autentisering i Azure API Management

API Management kan du skyddar åtkomsten till backend-tjänst i ett API med hjälp av klientcertifikat. Den här guiden visar hur du hanterar certifikat i Azure API Management-tjänstinstans i Azure-portalen. Den förklarar också hur du konfigurerar ett API om du vill använda ett certifikat för att få åtkomst till en backend tjänst.

Information om hur du hanterar certifikat med hjälp av API Management REST API finns i <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API-certifikat entitet</a>.

## <a name="prerequisites"> </a>Nödvändiga komponenter

Den här guiden visar hur du konfigurerar din API Management-tjänstinstans för att använda autentisering med klientcertifikat för att få åtkomst till backend-tjänsten för ett API. Innan du följer stegen i den här artikeln bör du ha dina backend-tjänst som konfigurerats för autentisering av klientcertifikat ([konfigurera autentisering med datorcertifikat i Azure WebSites finns på den här artikeln] [ to configure certificate authentication in Azure WebSites refer to this article]). Du behöver åtkomst till certifikatet och lösenordet för att överföra den till API Management-tjänsten.

## <a name="step1"> </a>Ladda upp ett klientcertifikat

![Lägg till certifikat](media/api-management-howto-mutual-certificates/apim-client-cert.png)

Följ stegen nedan för att ladda upp ett nytt klientcertifikat. Om du inte har skapat en API Management-tjänstinstans ännu, finns i självstudierna [skapa en API Management-tjänstinstans][Create an API Management service instance].

1. Gå till din Azure API Management-tjänstinstans i Azure-portalen.
2. Välj **klientcertifikat** på menyn.
3. Klicka på knappen **+ Lägg till**.  
    ![Lägg till certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)  
4. Bläddra fram certifikatet, anger dess ID och lösenord.  
5. Klicka på **Skapa**.

> [!NOTE]
> Certifikatet måste vara i **.pfx** format. Självsignerade certifikat är tillåtna.

När certifikatet har överförts, visas i den **klientcertifikat**.  Om du har många certifikat, anteckna tumavtrycket för det önskade certifikatet för att [konfigurera ett API för att använda ett certifikat för gateway-autentisering][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Om du vill inaktivera verifiering av certifikatkedjan när du använder, till exempel ett självsignerat certifikat, följer du stegen som beskrivs i den här vanliga frågor och svar [objekt](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end).

## <a name="step1a"> </a>Ta bort ett klientcertifikat

Ta bort ett certifikat genom att klicka på snabbmenyn **...**  och välj **ta bort** bredvid certifikatet.

![Ta bort klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-delete.png)

Om certifikaten som används av ett API, visas en varning-skärm. Om du vill ta bort certifikatet, måste du först ta bort certifikatet från alla API: er som är konfigurerade för att använda den.

![Ta bort certifikat klientfel](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="step2"> </a>Konfigurera ett API för att använda ett certifikat för gateway-autentisering

1. Klicka på **API: er** från den **API Management** på vänstra menyn och navigera till API: et.  
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. I den **Design** fliken, klickar du på penn-ikonen för den **serverdel** avsnittet. 
3. Ändra den **Gateway autentiseringsuppgifter** till **Client cert** och välj ditt certifikat i listrutan.  
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klicka på **Spara**. 

> [!WARNING]
> Den här ändringen är omedelbart verksam och anrop till åtgärderna i den API ska använda certifikatet för autentisering på backend-servern.


> [!TIP]
> När ett certifikat har angetts för gateway-autentisering för backend-tjänst i ett API, blir en del av principen för det API: et och kan ses i principredigeraren.

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat, måste du inaktivera verifiering av certifikatkedjan för API Management att kommunicera med backend-system. Annars returneras en 500 felkod. Om du vill skapa den här konfigurationen kan du använda den [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (för ny serverdel) eller [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (för befintliga serverdel) PowerShell-cmdletar och ange den `-SkipCertificateChainValidation` parameter `True`.

```powershell
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

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
