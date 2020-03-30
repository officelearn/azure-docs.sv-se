---
title: Skapa ContentKeys med .NET
description: Den här artikeln visar hur du skapar innehållsnycklar som ger säker åtkomst till tillgångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: aebd6dee9314d6e5641988767c024790b6b721f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251159"
---
# <a name="create-contentkeys-with-net"></a>Skapa ContentKeys med .NET 
> [!div class="op_single_selector"]
> * [Resten](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Med Media Services kan du skapa och leverera krypterade tillgångar. En **ContentKey** ger säker åtkomst till dina **tillgångar.** 

När du skapar en ny tillgång (till exempel innan du [laddar upp filer)](media-services-dotnet-upload-files.md)kan du ange följande krypteringsalternativ: **StorageEncrypted**, **CommonEncryptionProtected**eller **EnvelopeEncryptionProtected**. 

När du levererar tillgångar till dina klienter kan du [konfigurera för att tillgångar ska krypteras dynamiskt](media-services-dotnet-configure-asset-delivery-policy.md) med en av följande två krypteringar: **DynamicEnvelopeEncryption** eller **DynamicCommonEncryption**.

Krypterade tillgångar måste associeras med **ContentKey**s. I den här artikeln beskrivs hur du skapar en innehållsnyckel.

> [!NOTE]
> När du skapar en ny **StorageEncrypted-tillgång** med hjälp av Media Services .NET SDK skapas och länkas **ContentKey** automatiskt till tillgången.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Ett av de värden som du måste ange när du skapar en innehållsnyckel är innehållsnyckeltypen. Välj bland något av följande värden. 

```csharp
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
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Skapa innehållsnyckel för kuverttyp
Följande kodavsnitt skapar en innehållsnyckel för kuvertkrypteringstypen. Den associerar sedan nyckeln med den angivna tillgången.

```csharp
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

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Skapa contentkey av vanlig typ
Följande kodavsnitt skapar en innehållsnyckel av den vanliga krypteringstypen. Den associerar sedan nyckeln med den angivna tillgången.

```csharp
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
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

