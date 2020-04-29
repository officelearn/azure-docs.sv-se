---
title: Säkra backend-tjänster med autentisering av klient certifikat
titleSuffix: Azure API Management
description: Lär dig hur du skyddar backend-tjänster med autentisering av klient certifikat i Azure API Management.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80347096"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Så skyddar du backend-tjänster genom att autentisera klientcertifikat i Azure API Management

API Management ger dig säker åtkomst till backend-tjänsten för ett API med hjälp av klient certifikat. Den här guiden visar hur du hanterar certifikat i Azure API Management-tjänsteinstansen i Azure Portal. Det beskriver också hur du konfigurerar ett API för att använda ett certifikat för att få åtkomst till en backend-tjänst.

Information om hur du hanterar certifikat med hjälp av API Management REST API finns i <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API Management REST API certifikat enhet</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här guiden visar hur du konfigurerar din API Management tjänst instans så att autentisering med klient certifikat används för att komma åt backend-tjänsten för ett API. Innan du följer stegen i den här artikeln bör du ha konfigurerat din server dels tjänst för autentisering av klient certifikat ([för att konfigurera certifikatautentisering i Azure App Service referera till den här artikeln][to configure certificate authentication in Azure WebSites refer to this article]). Du behöver åtkomst till certifikatet och lösen ordet för att ladda upp det till API Management tjänsten.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Ladda upp ett certifikat

> [!NOTE]
> I stället för ett överfört certifikat kan du använda ett certifikat som lagras i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -tjänsten, vilket visas i det här [exemplet](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Lägg till klient certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Följ stegen nedan för att ladda upp ett nytt klient certifikat. Om du inte har skapat en API Management tjänst instans ännu går du till självstudien [skapa en API Management tjänst instans][Create an API Management service instance].

1. Gå till Azure API Management Service-instansen i Azure Portal.
2. Välj **certifikat** på menyn.
3. Klicka på knappen **+ Lägg till**.
    ![Lägg till klient certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Bläddra efter certifikatet, ange dess ID och lösen ord.
5. Klicka på **Skapa**.

> [!NOTE]
> Certifikatet måste vara i **PFX** -format. Självsignerade certifikat är tillåtna.

När certifikatet har laddats upp visas det i **certifikaten**.  Om du har många certifikat ska du anteckna tumavtrycket för det önskade certifikatet för att [Konfigurera ett API för att använda ett klient certifikat för gateway-autentisering][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Om du vill inaktivera verifiering av certifikat kedjan när du använder, till exempel ett självsignerat certifikat, följer du stegen som beskrivs i den här [artikeln](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)om vanliga frågor och svar.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ta bort ett klient certifikat

Ta bort ett certifikat genom att klicka på snabb menyn **...** och välj **ta bort** bredvid certifikatet.

![Ta bort klient certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Om certifikatet används av ett API visas en varnings skärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla API: er som har kon figurer ATS för att använda det.

![Ta bort klient certifikats problem](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Konfigurera ett API för att använda ett klient certifikat för gateway-autentisering

1. Klicka på **API: er** på **API Management** menyn till vänster och navigera till API: et.
    ![Aktivera klient certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. På fliken **design** klickar du på en Penn ikon i **Server delen** .
3. Ändra **Gateway-autentiseringsuppgifterna** till **klient certifikat** och välj ditt certifikat i list rutan.
    ![Aktivera klient certifikat](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klicka på **Spara**.

> [!WARNING]
> Den här ändringen börjar gälla omedelbart och anrop till åtgärder i detta API använder certifikatet för att autentisera på backend-servern.


> [!TIP]
> När ett certifikat anges för gateway-autentisering för backend-tjänsten för ett API, blir det en del av principen för detta API och kan visas i princip redigeraren.

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat måste du inaktivera verifiering av certifikat kedjan för att API Management ska kunna kommunicera med backend-systemet. Annars returneras en felkod på 500. Om du vill konfigurera detta kan du använda [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) PowerShell-cmdletarna (for New [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) Server End) eller (för befintliga Server delar) och ange `-SkipCertificateChainValidation` parametern till `True`.

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
