---
title: azcopy inloggning | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för inloggningskommandot för azkoposkopi.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295407"
---
# <a name="azcopy-login"></a>azcopy inloggning

Loggar in på Azure Active Directory för att komma åt Azure Storage-resurser.

## <a name="synopsis"></a>Synopsis

Logga in på Azure Active Directory för att komma åt Azure Storage-resurser.

Om du vill ha behörighet till ditt Azure Storage-konto måste du tilldela rollen **Storage Blob Data Contributor** till ditt användarkonto i kontexten för antingen lagringskontot, den överordnade resursgruppen eller den överordnade prenumerationen.

Det här kommandot cachelagrar krypterad inloggningsinformation för den aktuella användaren med hjälp av de inbyggda operativsystemet-mekanismerna.

Se exemplen för mer information.

> [!IMPORTANT]
> Om du anger en miljövariabel med hjälp av kommandoraden kan variabeln läsas i kommandoradshistoriken. Överväg att rensa variabler som innehåller autentiseringsuppgifter från kommandoradshistoriken. Om du vill förhindra att variabler visas i historiken kan du använda ett skript för att fråga användaren om deras autentiseringsuppgifter och ange miljövariabeln.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

Logga in interaktivt med standard-AAD-klient-ID som vanligt:

```azcopy
azcopy login
```

Logga in interaktivt med ett angivet klient-ID:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Logga in med hjälp av den systemtilldelade identiteten för en virtuell dator (VM):

```azcopy
azcopy login --identity
```

Logga in med hjälp av den användartilldelade identiteten för en virtuell dator och ett klient-ID för tjänstidentiteten:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Logga in med hjälp av den användartilldelade identiteten för en virtuell dator och ett objekt-ID för tjänstidentiteten:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Logga in med hjälp av den användartilldelade identiteten för en virtuell dator och ett resurs-ID för tjänstidentiteten:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Logga in som tjänsthuvudnamn med hjälp av en klienthemlighet. Ange miljövariabeln AZCOPY_SPA_CLIENT_SECRET till klienthemligheten för huvudnamnsautentisering för underrättelsetjänst.

```azcopy
azcopy login --service-principal
```

Logga in som tjänsthuvudnamn med hjälp av ett certifikat och lösenord. Ange miljövariabeln AZCOPY_SPA_CERT_PASSWORD till certifikatets lösenord för certifikatbaserad huvudauktorisering för tjänsten.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Se till att behandla /path/to/my/cert som en sökväg till en PEM- eller PKCS12-fil. AzCopy når inte in i systemcert-arkivet för att hämta ditt certifikat.

--certificate-path är obligatoriskt när du gör cert-based service principal auth.

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|--aad-slutpunkt|Slutpunkten för Azure Active Directory som ska användas. Standard (`https://login.microsoftonline.com`) är korrekt för det offentliga Azure-molnet. Ange den här parametern när du autentiserar i ett nationellt moln. Se [slutpunkter för Azure AD-autentisering](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Den här flaggan behövs inte för hanterad tjänstidentitet.|
|--application-id sträng|Program-ID för användartilldelade identitet. Krävs för tjänstens huvudnamnsamut.|
|--certifikat-sökvägssträng|Sökväg till certifikat för SPN-autentisering. Krävs för certifikatbaserade tjänsthuvudnamnsauth.|
|-h, --hjälp|Visa hjälpinnehåll för inloggningskommandot.|
|--identitet|logga in med hjälp av den virtuella datorns identitet, även känd som MSI (Managed Service Identity).|
|--identity-client-id sträng|Klient-ID för användartilldelade identitet.|
|--identity-object-id-sträng|Objekt-ID för användartilldelade identitet.|
|--identity-resource-id sträng|Resurs-ID för användartilldelade identitet.|
|--service-huvudman|Logga in via SPN (Tjänstens huvudnamn) med hjälp av ett certifikat eller en hemlighet. Klienthemligheten eller certifikatlösenordet måste placeras i rätt miljövariabel. Skriv `AzCopy env` om du vill visa namn och beskrivningar av miljövariabler.|
|--tenant-id-sträng| Azure active directory-klient-ID som ska användas för interaktiv inloggning på OAuth-enheten.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ ärvda från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--cap-mbps uint32 --cap-mbps|Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.|
|--utdata-typ sträng|Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text".|

## <a name="see-also"></a>Se även

- [azcopy (azcopy)](storage-ref-azcopy.md)
