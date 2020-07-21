---
title: AzCopy-inloggning | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 754baa66d79d169f830332f3c39660f1d71f608a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527922"
---
# <a name="azcopy-login"></a>azcopy inloggning

Loggar in på Azure Active Directory för att få åtkomst till Azure Storage resurser.

## <a name="synopsis"></a>Sammanfattning

Logga in på Azure Active Directory för att få åtkomst till Azure Storage resurser.

Om du vill ha behörighet till ditt Azure Storage-konto måste du tilldela rollen **Storage BLOB data Contributor** till ditt användar konto i kontexten för antingen lagrings kontot, den överordnade resurs gruppen eller den överordnade prenumerationen.

Det här kommandot cachelagrar krypterad inloggnings information för den aktuella användaren med hjälp av inbyggda OS-mekanismer.

Mer information finns i exemplen.

> [!IMPORTANT]
> Om du anger en miljö variabel med hjälp av kommando raden, kommer den variabeln att läsas i kommando rads historiken. Överväg att ta bort variabler som innehåller autentiseringsuppgifter från din kommando rads historik. Om du vill att variablerna ska visas i historiken kan du använda ett skript för att uppmana användaren att ange sina autentiseringsuppgifter och ange miljövariabeln.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Logga in interaktivt med standard-ID för AAD-klient har angetts till common:

```azcopy
azcopy login
```

Logga in interaktivt med ett angivet klient-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Logga in med den systemtilldelade identiteten för en virtuell dator (VM):

```azcopy
azcopy login --identity
```

Logga in med den användardefinierade identiteten för en virtuell dator och klient-ID: t för tjänstens identitet:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Logga in med den användardefinierade identiteten för en virtuell dator och ett objekt-ID för tjänst identiteten:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Logga in med den användardefinierade identiteten för en virtuell dator och resurs-ID: t för tjänstens identitet:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Logga in som tjänstens huvud namn med en klient hemlighet. Ange miljövariabeln AZCOPY_SPA_CLIENT_SECRET till klient hemligheten för den hemliga autentiseringen för tjänstens huvud namn.

```azcopy
azcopy login --service-principal --application-id "YOUR_APP_ID" --tenant-id "YOUR_TENANT_ID"
```

Logga in som tjänstens huvud namn med ett certifikat och lösen ord. Ange miljövariabeln AZCOPY_SPA_CERT_PASSWORD till certifikatets lösen ord för cert-baserad tjänstens huvud namns behörighet.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Se till att behandla/path/to/My/cert som en sökväg till en PEM-eller PKCS12-fil. AzCopy når inte till system certifikat arkivet för att hämta ditt certifikat.

--certifikat-sökväg är obligatorisk när du gör en cert-baserad tjänstens huvud namns-auth.

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--AAD-slut punkt|Den Azure Active Directory slut punkt som ska användas. Standardvärdet ( `https://login.microsoftonline.com` ) är korrekt för det offentliga Azure-molnet. Ange den här parametern vid autentisering i ett nationellt moln. Se [Azure AD-autentiseringens slut punkter](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Den här flaggan behövs inte för Hanterad tjänstidentitet.|
|--program-ID-sträng|Program-ID för användardefinierad identitet. Krävs för tjänstens huvud namns autentisering.|
|--certifikat Sök vägs sträng|Sökväg till certifikat för SPN-autentisering. Krävs för certifikatbaserad tjänstens huvud namns-auth.|
|-h,--hjälp|Visa hjälp innehåll för inloggnings kommandot.|
|--identitet|Logga in med den virtuella datorns identitet, även kallat hanterad tjänst identitet (MSI).|
|--Identity-Client-ID-sträng|Klient-ID för användarens tilldelade identitet.|
|--Identity-Object-ID-sträng|Objekt-ID för användardefinierad identitet.|
|--identitet-resurs-ID-sträng|Resurs-ID för användarens tilldelade identitet.|
|--tjänst-huvud konto|Logga in via SPN (tjänstens huvud namn) genom att använda ett certifikat eller en hemlighet. Klient hemligheten eller certifikat lösen ordet måste placeras i lämplig miljö variabel. Skriv `AzCopy env` för att se namn och beskrivningar för miljövariabler.|
|--ID-sträng för klient organisation| klient-ID: t för Azure Active Directory som ska användas för interaktiv inloggning i OAuth-enheter.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|
|--sträng för betrodd-Microsoft-suffix   |Anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.|

## <a name="see-also"></a>Se även

- [AzCopy](storage-ref-azcopy.md)
