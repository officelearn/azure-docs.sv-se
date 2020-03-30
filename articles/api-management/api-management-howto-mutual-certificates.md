---
title: Säkra backend-tjänster med klientcertifikatautentisering
titleSuffix: Azure API Management
description: Lär dig hur du skyddar backend-tjänster med klientcertifikatautentisering i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347096"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Så skyddar du backend-tjänster genom att autentisera klientcertifikat i Azure API Management

MED API Management kan du skydda åtkomsten till backend-tjänsten för ett API med hjälp av klientcertifikat. Den här guiden visar hur du hanterar certifikat i Azure API Management-tjänstinstansen i Azure-portalen. Det förklarar också hur du konfigurerar ett API för att använda ett certifikat för att komma åt en backend-tjänst.

Information om hur du hanterar certifikat med REST API-API:et för API-hantering finns i <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">azure API Management REST API-certifikatentiteten</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här guiden visar hur du konfigurerar tjänstinstansen för API Management så att klientcertifikatautentisering används för att komma åt backend-tjänsten för ett API. Innan du följer stegen i den här artikeln bör du ha din backend-tjänst konfigurerad för klientcertifikatautentisering ([för att konfigurera certifikatautentisering i Azure App Service se den här artikeln][to configure certificate authentication in Azure WebSites refer to this article]). Du behöver åtkomst till certifikatet och lösenordet för att ladda upp det till API Management-tjänsten.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Ladda upp ett certifikat

> [!NOTE]
> I stället för ett överfört certifikat kan du använda ett certifikat som lagras i [Azure Key Vault-tjänsten](https://azure.microsoft.com/services/key-vault/) som visas i det här [exemplet](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Lägga till klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Följ stegen nedan för att ladda upp ett nytt klientcertifikat. Om du inte har skapat en API Management-tjänstinstans ännu läser du självstudien [Skapa en API Management-tjänstinstans][Create an API Management service instance].

1. Navigera till din Azure API Management-tjänstinstans i Azure-portalen.
2. Välj **Certifikat** på menyn.
3. Klicka på knappen **+ Lägg till**.
    ![Lägga till klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Bläddra efter certifikatet, ange dess ID och lösenord.
5. Klicka på **Skapa**.

> [!NOTE]
> Certifikatet måste vara i **PFX-format.** Självsignerade certifikat är tillåtna.

När certifikatet har laddats upp visas det i **certifikaten**.  Om du har många certifikat kan du anteckna tumavtrycket för önskat certifikat för att [konfigurera ett API för att använda ett klientcertifikat för gatewayautentisering][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Om du vill inaktivera validering av certifikatkedja när du till exempel använder ett självsignerat certifikat följer du stegen som beskrivs i det här [faq-objektet](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end).

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ta bort ett klientcertifikat

Om du vill ta bort ett certifikat klickar du på snabbmenyn **...** och väljer **Ta bort** bredvid certifikatet.

![Ta bort klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Om certifikatet används av ett API visas en varningsskärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla API:er som är konfigurerade för att använda det.

![Det gick inte att ta bort klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurera ett API för att använda ett klientcertifikat för gatewayautentisering

1. Klicka på **API:er** från **API Management-menyn** till vänster och navigera till API:et.
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Klicka på en pennikon i **avsnittet Backend** på fliken **Design.**
3. Ändra **gatewayautentiseringsuppgifterna** till **klientcertifikatet** och välj ditt certifikat i listrutan.
    ![Aktivera klientcertifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klicka på **Spara**.

> [!WARNING]
> Den här ändringen träder i kraft omedelbart och anrop till åtgärder för det API:et använder certifikatet för att autentisera på serverdelsservern.


> [!TIP]
> När ett certifikat anges för gatewayautentisering för backend-tjänsten för ett API blir det en del av principen för det API:et och kan visas i principredigeraren.

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat måste du inaktivera validering av certifikatkedja för att API Management ska kunna kommunicera med serverdasystemet. Annars returnerar den en felkod på 500. Om du vill konfigurera [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) detta kan du använda [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (för ny serverdel) eller (för `-SkipCertificateChainValidation` befintliga `True`backend) PowerShell-cmdlets och ställa in parametern på .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
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
