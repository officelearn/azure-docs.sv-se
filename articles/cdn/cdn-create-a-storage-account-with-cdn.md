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
ms.date: 01/04/2018
ms.author: mazha
ms.openlocfilehash: 022071f7825cb9184bd4c815c09e1c202a0a6f91
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integrera Azure storage-konto med Azure CDN
Du kan aktivera Azure innehåll innehållsleveransnätverk (CDN) till att cachelagra innehåll från Azure storage. Azure CDN ger utvecklare en global lösning för att leverera innehåll med hög bandbredd. Det kan cachelagra blobbar och compute-instanser på fysiska noder i USA, Europa, Asien, Australien och Sydamerika statiskt innehåll.

## <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto
Använd följande procedur för att skapa ett nytt lagringskonto för en Azure-prenumeration. Ett lagringskonto ger åtkomst till Azure Storage-tjänster. Lagringskontot representerar den högsta nivån av namnområdet för åtkomst till var och en av tjänstkomponenter Azure Storage: lagring med Azure Blob, köer och tabellen. Mer information finns i [introduktion till Microsoft Azure Storage](../storage/common/storage-introduction.md).

Du måste vara tjänstadministratör eller en coadministrator för den associera prenumerationen om du vill skapa ett lagringskonto.

> [!NOTE]
> Du kan använda flera metoder för att skapa ett lagringskonto, inklusive Azure portal och PowerShell. Den här kursen visar hur du använder Azure-portalen.   
> 

**Skapa ett lagringskonto för en Azure-prenumeration**

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det övre vänstra hörnet och väljer **skapar du en resurs**. I den **ny** väljer **lagring**, och välj sedan **lagringskonto - blob, fil, tabell, kö**.
    
    Den **skapa lagringskonto** visas.   

    ![Skapa fönstret för storage-konto](./media/cdn-create-a-storage-account-with-cdn/cdn-create-new-storage-account.png)

3. I den **namn** anger du ett underdomännamn. Den här posten kan innehålla 3 till 24 gemena bokstäver och siffror.
   
    Det här värdet blir värdnamnet inom URI: N som används för att adressera blob, kö eller tabell resurser för prenumerationen. Använd en URI för att åtgärda en behållare resurs i Blob storage i följande format:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    där  *&lt;StorageAccountLabel&gt;*  refererar till värdet i den **namn** rutan.
   
    > [!IMPORTANT]    
    > URL-etiketten bildar underdomänen i lagringskontot URI och måste vara unika bland alla värdbaserade tjänster i Azure.
   
    Det här värdet används också som namnet på lagringskontot i portalen, eller när du försöker komma åt det här kontot programmässigt.
    
4. Använda standardinställningarna för **distributionsmodellen**, **konto kind**, **prestanda**, och **replikering**. 
    
5. För **prenumeration**, Välj prenumerationen som ska användas med storage-konto.
    
6. För **resursgruppen**väljer eller skapar en resursgrupp. Information om resursgrupper finns i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
    
7. För **plats**, Välj en plats för ditt lagringskonto.
    
8. Välj **Skapa**. Processen att skapa lagringskontot kan ta flera minuter att slutföra.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Steg 2: Aktivera CDN för lagringskontot

Du kan aktivera CDN för ditt lagringskonto direkt från ditt lagringskonto. 

1. Välj ett lagringskonto från instrumentpanelen och sedan **Azure CDN** i den vänstra rutan. Om den **Azure CDN** knappen visas inte direkt kan du ange CDN i den **Sök** rutan i det vänstra fönstret.
    
    Den **Azure Content Delivery Network** visas.

    ![Skapa CDN-slutpunkt](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)
    
2. Skapa en ny slutpunkt genom att ange informationen som krävs:
    - **CDN-profilen**: skapa en ny CDN-profil eller använda en befintlig CDN-profil.
    - **Prisnivån**: Välj en prisnivå bara om du skapar en CDN-profil.
    - **Namnet på slutpunkten CDN**: Ange ett namn för CDN-slutpunkten.

    > [!TIP]
    > Som standard använder en ny CDN-slutpunkt värdnamnet för ditt lagringskonto som den ursprungliga servern.

3. Välj **Skapa**. När slutpunkten har skapats visas den i listan med slutpunkten.

    ![Lagring ny CDN-slutpunkt](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!NOTE]
> Om du vill ange avancerade konfigurationsinställningar för CDN-slutpunkten, till exempel vilken typ av optimering, kan du i stället använda den [Azure CDN tillägget](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) att skapa en CDN-slutpunkt, eller en CDN-profil.

## <a name="step-3-enable-additional-cdn-features"></a>Steg 3: Aktivera ytterligare CDN-funktioner

Från lagringskontot **Azure CDN** fönstret Välj CDN-slutpunkten från listan för att öppna fönstret CDN konfiguration. Du kan aktivera ytterligare CDN-funktioner för din leverans, till exempel komprimering, frågesträngen och geo filtrering. Du kan också lägga till anpassade domänmappning till CDN-slutpunkten och aktivera anpassad domän för HTTPS.
    
![Lagringskonfigurationen CDN-slutpunkten](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)

## <a name="step-4-access-cdn-content"></a>Steg 4: Åtkomst CDN innehåll
Använd CDN-URL som anges i portalen för att komma åt cachelagrade innehåll på CDN. Adressen för en cachelagrade blob har följande format:

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> När du aktiverar CDN åtkomst till ett lagringskonto, är alla offentligt tillgängliga objekt berättigade för cachelagring av CDN kant. Om du ändrar ett objekt som för tillfället är cachelagrade i CDN nytt innehåll inte tillgängliga via CDN förrän CDN uppdaterar innehållet när time to live-period för cachelagrat innehåll har löpt ut.

## <a name="step-5-remove-content-from-the-cdn"></a>Steg 5: Ta bort innehåll från CDN
Om du inte längre vill cachelagra ett objekt i Azure CDN kan du göra något av följande steg:

* Gör så att privata behållare i stället för allmänheten. Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).
* Inaktivera eller ta bort CDN-slutpunkten med hjälp av Azure portal.
* Ändra din värdbaserade tjänst för att inte längre svarar på begäranden för objektet.

Ett objekt som redan har cachelagrats i Azure CDN kvar cachelagrade tills time to live-period för objektet har löpt ut eller slutpunkten tas bort. När time to live-period har löpt ut kontrollerar Azure CDN om CDN-slutpunkten fortfarande är giltigt och objektet är fortfarande anonymt tillgängligt. Om inte cachelagras inte längre objektet.

## <a name="additional-resources"></a>Ytterligare resurser
* [Lägga till en anpassad domän i CDN-slutpunkten](cdn-map-content-to-custom-domain.md)
* [Konfigurera HTTPS på en anpassad domän för Azure CDN](cdn-custom-ssl.md)

