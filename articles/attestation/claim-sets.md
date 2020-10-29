---
title: Anspråks uppsättningar för Azure-attestering
description: Anspråks uppsättningarna för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909785"
---
# <a name="claim-sets"></a>Anspråksuppsättningar

Anspråk som genereras vid bestyrkande av enclaves med hjälp av Microsoft Azure attestering kan delas in i kategorierna nedan:

- **Inkommande anspråk** : anspråk som genereras av Microsoft Azure attestering efter parsningen av attesteringen.

- **Utgående anspråk** : anspråk som skapats som utdata av Azure-attestering. Den innehåller alla anspråk som ska avslutas i attesterings-token.

- **Egenskaps anspråk** : anspråk som skapats som utdata av Azure-attestering. Den innehåller alla anspråk som representerar egenskaperna för attesterings-token, till exempel kodning av rapporten, rapportens giltighets tid och så vidare.

Under anspråk som definieras av JWT RFC och används av Azure-attestering i objektet Response:

- **"ISS" (Issuer)-anspråk** : "ISS" (Issuer)-anspråket identifierar det huvud konto som utfärdade JWT. Bearbetningen av detta anspråk är i princip programspecifik. Värdet "ISS" är en Skift läges känslig sträng som innehåller ett StringOrURI-värde.
- **"IAT" (utfärdat vid)-anspråk** : anspråket "IAT" (utfärdat kl) anger den tid då JWT utfärdades. Detta anspråk kan användas för att fastställa Age of JWT. Värdet måste vara ett tal som innehåller ett NumericDate-värde.
- **"EXP"-anspråk (förfallo tid)** : anspråket "EXP" (förfallo tid) anger förfallo tiden på eller efter vilken JWT inte får godkännas för bearbetning. Bearbetningen av "EXP"-anspråket kräver att aktuellt datum/tid måste infalla före det datum/tid som anges i "EXP"-anspråket.

  Obs! en 5-minuters Leeway läggs till i utgivnings tiden (IAT) för att ge ett konto för klock skevning.
- **"NBF" (inte före)-anspråk** : "NBF" (inte före)-anspråket anger den tid som JWT inte ska accepteras för bearbetning. Bearbetningen av "NBF"-anspråket kräver att aktuellt datum/tid måste vara efter eller lika med det icke-före-datum/-tid som anges i "NBF"-anspråket.
  Obs! en 5-minuters Leeway läggs till i utgivnings tiden (IAT) för att ge ett konto för klock skevning.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>Anspråk som utfärdats av Azure-attestering i SGX-enclaves

### <a name="incoming-claims"></a>Inkommande anspråk 

- **$is-fel sökning** : ett booleskt värde som anger om enklaven har fel sökning aktiverat eller inte
- **$SGX-mrsigner** : hex-kodat värde för fältet "mrsigner" för offerten
- **$SGX-mrenclave** : hex-kodat värde för fältet "mrenclave" för offerten
- **$product-ID**
- **$SVN** : säkerhets versions nummer som har kodats i offerten 
- **$tee** : typ av enklaven 

### <a name="outgoing-claims"></a>Utgående anspråk

- **är-fel söknings** bara: ett booleskt värde som anger om enklaven har fel sökning aktiverat eller inte
- **SGX-mrsigner** : hex-kodat värde för fältet "mrsigner" för offerten
- **SGX-mrenclave** : hex-kodat värde för fältet "mrenclave" för offerten
- **produkt-ID**
- **SVN** : säkerhets versions nummer som har kodats i offerten 
- **tee** : typ av enklaven 
- **Maa-EHD** : Base64Url-kodad version av "enklaven kvarhållna data" som anges i begäran om attestering 
- **AAS-EHD** : Base64Url-kodad version av "enklaven undanhöll data" som anges i begäran om attestering 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>Anspråk som utfärdats av Azure-attestering i VBS enclaves

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>Inkommande anspråk (kan också användas som utgående anspråk)

- **aikValidated** : booleskt värde som innehåller information om certifikat för attestering av identitets nyckel (AIK) har verifierats eller inte.
- **aikPubHash** : sträng som innehåller base64 (offentlig nyckel för SHA256 (AIK i der-format)).
- **tpmVersion** : heltals värde som innehåller den Trusted Platform Module (TPM) huvud version.
- **secureBootEnabled** : booleskt värde som anger om säker start har Aktiver ATS.
- **iommuEnabled** : booleskt värde som anger om indata-utdata för minnes hanterings enhet (IOMMU) är aktive rad.
- **bootDebuggingDisabled** : booleskt värde som anger om start fel sökning har inaktiverats.
- **notSafeMode** : booleskt värde som anger om Windows inte körs i fel säkert läge.
- **notWinPE** : booleskt värde som anger om Windows inte körs i WinPE-läge.
- **vbsEnabled** : booleskt värde som anger om vbs är aktiverat.
- **vbsReportPresent** : booleskt värde som anger om vbs enklaven Report är tillgängligt.
- **enclaveAuthorId** : sträng värde som innehåller det Base64Url-kodade värdet för enklaven författar-ID: författar identifierare för den primära modulen för enklaven.
- **enclaveImageId** : sträng värde som innehåller det Base64Url-kodade värdet för enklaven-avbildnings-ID: t för avbildnings-ID: t för den primära modulen för enklaven.
- **enclaveOwnerId** : sträng värde som innehåller det Base64Url-kodade värdet för enklaven ägar-ID-ID: t för ägaren för enklaven.
- **enclaveFamilyId** : sträng värde som innehåller det Base64Url-kodade värdet för enklaven Family ID. Familje identifieraren för den primära modulen för enklaven.
- **enclaveSvn** : ett heltals värde som innehåller säkerhets versions numret för den primära modulen för enklaven.
- **enclavePlatformSvn** : ett heltals värde som innehåller säkerhets versions numret för den plattform som är värd för enklaven.
- **enclaveFlags** : enclaveFlags-anspråket är ett heltals värde som innehåller flaggor som beskriver körnings principen för enklaven.
  
### <a name="outgoing-claims"></a>Utgående anspråk

- **policy_hash** : sträng värde som innehåller SHA256-hash för princip texten som beräknats av BASE64URL (SHA256 (BASE64URL (UTF8 (princip text))).
- **policy_signer** : innehåller en JWK med den offentliga nyckeln eller certifikat kedjan som finns i den signerade princip rubriken.
- **ver (version)** : sträng värde som innehåller version av rapporten. För närvarande 1,0.
- **CNF (bekräftelse)-anspråk** : "CNF"-anspråket används för att identifiera den viktiga nyckeln. Bekräftelse anspråk enligt definitionen i RFC 7800 innehåller den offentliga delen av den bevisade enklaven-nyckeln som representeras som ett JWK-objekt (JSON Web Key) (RFC 7517).
- **rp_data (förlitande part data)** : förlitande part data, om sådana finns, som anges i begäran och som används av den förlitande parten som ett nonce för att säkerställa att rapporten uppdateras.
- **"JTI"-anspråk (JWT ID)** : anspråket "JTI" (JWT ID) tillhandahåller en unik IDENTIFIERARE för JWT. Identifier-värdet tilldelas på ett sätt som garanterar att det är en försumbar sannolikhet att samma värde har tilldelats av misstag till ett annat data objekt.

### <a name="property-claims"></a>Egenskaps anspråk

- **report_validity_in_minutes** : ett heltals anspråk som indikerar hur länge token är giltig.
  - **Standardvärde (tid)** : en dag på några minuter.
  - **Högsta värde (tid)** : ett år på några minuter.
- **omit_x5c** : ett booleskt anspråk som anger om Azure-attesteringen ska utelämna det certifikat som används för att tillhandahålla bevis på tjänstens äkthet. Om värdet är true kommer x5t att läggas till i attesterings-token. Om värdet är false (standard) kommer X5C att läggas till i attesterings-token.

## <a name="next-steps"></a>Nästa steg
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
