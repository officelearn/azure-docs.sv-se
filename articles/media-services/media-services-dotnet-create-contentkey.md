---
title: Skapa ContentKeys med .NET
description: "Lär dig hur du skapar innehåll nycklar som ger säker åtkomst till resurser."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 3280a6fcde59bae360da7cb9fea4bb649f984e43
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-contentkeys-with-net"></a>Skapa ContentKeys med .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services kan du skapa och leverera krypterade tillgångar. En **ContentKey** ger säker åtkomst till din **tillgången**s. 

När du skapar en ny tillgång (till exempel innan du [ladda upp filer](media-services-dotnet-upload-files.md)), kan du ange följande krypteringsalternativen: **StorageEncrypted**, **CommonEncryptionProtected**, eller **EnvelopeEncryptionProtected**. 

När du levererar tillgångar till dina klienter kan du [konfigurera för tillgångar ska vara dynamiskt krypterad](media-services-dotnet-configure-asset-delivery-policy.md) med något av följande två krypteringar: **DynamicEnvelopeEncryption** eller **DynamicCommonEncryption**.

Krypterade tillgångar måste associeras med **ContentKey**s. Den här artikeln beskriver hur du skapar en innehållsnyckel.

> [!NOTE]
> När du skapar en ny **StorageEncrypted** tillgång med Media Services .NET SDK i **ContentKey** skapas automatiskt och länkad till tillgången.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Ett av de värden som du måste ange när du skapar en innehåll nyckeln är viktiga innehållstypen. Välj något av följande värden. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a id="envelope_contentkey"></a>Skapa kuvert typen ContentKey
Följande kodavsnitt skapar en innehållsnyckel av typen kuvert kryptering. Sedan associerar den nyckeln med den angivna resursen.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

Anropa

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>Skapa gemensam typ. ContentKey
Följande kodavsnitt skapar en innehållsnyckel av den gemensamma krypteringstypen. Sedan associerar den nyckeln med den angivna resursen.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
Anropa

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

