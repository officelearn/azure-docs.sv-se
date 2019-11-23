---
title: 'Tutorial: Enable custom domain & SSL for a static website in Azure'
description: Lär dig hur du konfigurerar en anpassad domän för hantering av en statisk webbplats.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327498"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: Enable custom domain & SSL for a static website in Azure

Den här självstudien är del två i en serie. Där lär du dig att aktivera en slutpunkt för anpassad domän med SSL för din statiska webbplats. 

Självstudien visar hur du använder [Azure CDN](../../cdn/cdn-overview.md) för att konfigurera slutpunkten för anpassad domän för din statiska webbplats. Med Azure CDN kan du etablera anpassade SSL-certifikat, använda en anpassad domän och konfigurera anpassade omskrivningsregler på samma gång. Konfiguration av Azure CDN resulterar i ytterligare avgifter men ger konsekvent korta svarstider till din webbplats från hela världen. Azure CDN ger även SSL-kryptering med ditt eget certifikat. Information om priser för Azure CDN finns i [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/).

I del två i serien lär du dig hur du:

> [!div class="checklist"]
> * Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen
> * Aktivera anpassad domän och SSL

## <a name="prerequisites"></a>Krav

Before you begin this tutorial, complete part one, [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Skapa en CDN-slutpunkt på slutpunkten för den statisk webbplatsen

1. Locate your storage account in the Azure portal and display the account overview.
1. Välj **Azure CDN** under menyn **Blob Service** för att konfigurera Azure CDN.
1. In the **CDN profile** section, specify a new or existing CDN profile. Mer information finns i [Snabbstart: Skapa en Azure CDN-profil och CDN-slutpunkt](../../cdn/cdn-create-new-endpoint.md).
1. Specify a pricing tier for the CDN endpoint. This tutorial uses the **Standard Akamai** pricing tier, because it propagates quickly, typically within a few minutes. Other pricing tiers may take longer to propagate, but may also offer other advantages. For more information, see [Compare Azure CDN product features](../../cdn/cdn-features.md).
1. In the **CDN endpoint name** field, specify a name for your CDN endpoint. The CDN endpoint must be unique across Azure.
1. Specify your the static website endpoint in the **Origin hostname** field. To find your static website endpoint, navigate to the **Static website** settings for your storage account. Copy the primary endpoint and paste it into the CDN configuration, removing the protocol identifier (*e.g.* , HTTPS).

    The following image shows an example endpoint configuration:

    ![Screenshot showing sample CDN endpoint configuration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Create the CDN endpoint and wait for it to propagate.
1. To verify that the CDN endpoint is configured correctly, click on the endpoint to navigate to its settings. From the CDN overview for your storage account, locate the endpoint hostname, and navigate to the endpoint, as shown in the following image. The format of your CDN endpoint will be similar to `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot showing overview of CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Once the CDN endpoint propagation is complete, navigating to the CDN endpoint displays the contents of the index.html file that you previously uploaded to your static website.

1. To review the origin settings for your CDN endpoint, navigate to **Origin** under the **Settings** section for your CDN endpoint. You will see that the **Origin type** field is set to *Custom Origin* and that the **Origin hostname** field displays your static website endpoint.

    ![Screenshot showing Origin settings for CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aktivera anpassad domän och SSL

1. Skapa en CNAME-post hos din domänregistrator för att omdirigera din anpassade domän till CDN-slutpunkten. CNAME-posten för *www*-underdomänen bör likna följande:

    ![Ange CNAME-post för www-underdomänen](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. In the Azure portal, display the settings for your CDN endpoint. Navigate to **Custom domains** under **Settings** to configure the custom domain and the SSL certificate.
1. Välj **Lägg till anpassad domän**, ange ditt domännamn och klicka sedan på **Lägg till**.
1. Select the new custom domain mapping to provision an SSL certificate.
1. Set **Custom Domain HTTPS** to **ON**, then click **Save**. It may take several hours to configure your custom domain. The portal displays progress as shown in the following image.

    ![Screenshot showing progress of custom domain configuration](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Test the mapping of your static website to your custom domain by accessing the URL for your custom domain.

For more information about enabling HTTPS for custom domains, see [Tutorial: Configure HTTPS on an Azure CDN custom domain](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Nästa steg

I del två av den här självstudien lärde du dig att konfigurera en anpassad domän med SSL i Azure CDN för din statiska webbplats.

For more information about configuring and using Azure CDN, see [What is Azure CDN?](../../cdn/cdn-overview.md).