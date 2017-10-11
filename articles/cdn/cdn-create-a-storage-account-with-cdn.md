---
title: Integrera Azure storage-konto med Azure CDN | Microsoft Docs
description: "Lär dig hur du använder i Azure innehåll innehållsleveransnätverk (CDN) för att tillhandahålla hög bandbredd innehåll genom cachelagring blobbar från Azure Storage."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integrera Azure storage-konto med Azure CDN
CDN kan aktiveras till att cachelagra innehåll från ditt Azure storage. Den erbjuder utvecklare en global lösning för att leverera innehåll med hög bandbredd genom cachelagring av blobbar och compute-instanser på fysiska noder i USA, Europa, Asien, Australien och Sydamerika statiskt innehåll.

## <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto
Använd följande procedur för att skapa ett nytt lagringskonto för en Azure-prenumeration. Ett lagringskonto ger åtkomst till Azure storage-tjänster. Lagringskontot representerar den högsta nivån av namnområdet för åtkomst till alla komponenter för Azure storage-tjänsten: Blob-tjänster, Queue-tjänster och tabellen tjänster. Mer information finns i den [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md).

Du måste vara tjänstadministratör eller en medadministratör för den associera prenumerationen om du vill skapa ett lagringskonto.

> [!NOTE]
> Det finns flera metoder du kan använda för att skapa ett lagringskonto, inklusive Azure Portal och Powershell.  Den här självstudiekursen kommer ska vi använda Azure Portal.  
> 
> 

**Skapa ett lagringskonto för en Azure-prenumeration**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det övre vänstra hörnet väljer **ny**. I den **ny** väljer **Data + lagring**, klicka på **lagringskonto**.
    
    Den **skapa lagringskonto** bladet visas.   

    ![Skapa Lagringskonto][create-new-storage-account]  

3. I den **namnet** skriver du ett underdomännamn. Den här posten kan innehålla 3 till 24 gemena bokstäver och siffror.
   
    Det här värdet blir värdnamnet inom URI: N som används för att adressera Blob, kö eller tabell resurser för prenumerationen. För att lösa en behållare resurs i Blob-tjänsten har du använder en URI i följande format, där  *&lt;StorageAccountLabel&gt;*  refererar till värdet du angav i **ange en URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;minbehållare&gt;*
   
    **Viktigt:** URL: en etikett formulär underdomänen lagringskontot URI och måste vara unika bland alla värdbaserade tjänster i Azure.
   
    Det här värdet används också som namn på det här lagringskontot i portalen eller vid åtkomst till det här kontot programmässigt.
4. Lämnar du standardinställningarna för **distributionsmodellen**, **konto kind**, **prestanda**, och **replikering**. 
5. Välj den **prenumeration** som lagringskontot kommer att användas med.
6. Välj eller skapa en **Resursgrupp**.  Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj en plats för ditt lagringskonto.
8. Klicka på **Skapa**. Processen att skapa lagringskontot kan ta flera minuter att slutföra.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Steg 2: Aktivera CDN för lagringskontot

Med den senaste integrationen kan du nu aktivera CDN för ditt lagringskonto utan att lämna lagring-portaltillägg. 

1. Välj lagringskonto, söka efter ”CDN” eller bläddra nedåt från den vänstra navigeringsmenyn och klicka på ”Azure CDN”.
    
    Den **Azure CDN** bladet visas.

    ![CDN aktivera navigering][cdn-enable-navigation]
    
2. Skapa en ny slutpunkt genom att ange informationen som krävs
    - **CDN-profilen**: du kan skapa en ny eller använda en befintlig profil.
    - **Prisnivån**: du behöver bara välja en prisnivå om du skapar en ny CDN-profil.
    - **Namnet på slutpunkten CDN**: Ange en slutpunktsnamn per valet.

    > [!TIP]
    > Skapade CDN-slutpunkten använder värdnamnet för ditt lagringskonto som ursprung som standard.

    ! [cdn-slutpunkten skapandet av ny] [cdn--slutpunkt-skapa nya]

3. När den har skapats, ny slutpunkt kommer att visas i listan slutpunkt.

    ![CDN-slutpunkten ny lagring][cdn-storage-new-endpoint]

> [!NOTE]
> Du kan även gå till Azure CDN-tillägg för att aktivera CDN. [Kursen](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Steg 3: Aktivera ytterligare CDN-funktioner

Storage-konto ”Azure CDN” bladet Klicka CDN-slutpunkten i listan för att öppna bladet för CDN-konfiguration. Du kan aktivera ytterligare CDN-funktioner för leveransen, till exempel komprimering, frågesträngen geo filtrering. Du kan också lägga till anpassade domänmappning till CDN-slutpunkten och aktivera anpassad domän för HTTPS.
    
![cdn-CDN lagringskonfigurationen][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Steg 4: Åtkomst CDN innehåll
Använd CDN-URL som anges i portalen för att komma åt cachelagrade innehåll på CDN. Adressen för en cachelagrade blob ska vara liknar följande:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> När du aktiverar CDN åtkomst till ett lagringskonto, är alla offentligt tillgängliga objekt berättigade för cachelagring av CDN kant. Om du ändrar ett objekt som för tillfället är cachelagrade i CDN nytt innehåll inte tillgängliga via CDN förrän CDN uppdateras dess innehåll när den cachelagrade innehållet time to live-perioden har löpt ut.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Steg 5: Ta bort innehåll från CDN
Om du inte längre vill cachelagra ett objekt i Azure Content Delivery innehållsleveransnätverk (CDN) kan du göra något av följande steg:

* Du kan göra behållaren privat i stället för allmänheten. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).
* Du kan inaktivera eller ta bort CDN-slutpunkten med hanteringsportalen.
* Du kan ändra din värdbaserade tjänst för att inte längre svarar på begäranden för objektet.

Ett objekt som redan har cachelagrats i CDN förblir cachelagrade tills time to live-period för objektet har löpt ut eller slutpunkten tas bort. När time to live-period har löpt ut ska CDN kontrollera om CDN-slutpunkten är fortfarande giltigt och anonymt fortfarande komma åt objektet. Om det inte är det, kommer inte längre objektet cachelagras.

## <a name="additional-resources"></a>Ytterligare resurser
* [Mappa CDN-innehåll till en anpassad domän](cdn-map-content-to-custom-domain.md)
* [Aktivera HTTPS för den anpassade domänen](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
