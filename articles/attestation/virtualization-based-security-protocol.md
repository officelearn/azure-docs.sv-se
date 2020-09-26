---
title: Protokoll för virtualiseringsbaserad säkerhet (VBS) för Azure-attestering
description: VBS attesterings protokoll
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346082"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>Attesterings protokoll för virtualiseringsbaserad säkerhet (VBS) 

För att Microsoft Azure attestering för att ge starka säkerhets garantier för att de data som rapporteras är autentiska, krävs det att skapa en kedja av förtroende från den inbyggda program varan till lanseringen av hypervisor-programmet och säker kernel. För att uppnå denna Azure-attestering måste du intyga datorns start tillstånd innan vi kan upprätta förtroende i den säkra enklaven. Binärfilerna för operativ systemet, hypervisor och säker kernel måste signeras av rätt officiella Microsoft-myndigheter och konfigureras på ett säkert sätt. När vi har bundit förtroende mellan Trusted Platform Module (TPM) och hälsan för hypervisorn kan vi lita på den VBS-IDKS som tillhandahålls i den uppmätta start loggen. Med det här kan vi verifiera att ett nyckel par genererades av enklaven och mintgrön en attesterings rapport som binder förtroendet i nyckeln och innehåller andra anspråk som säkerhets nivå och egenskaper för start attestering.

## <a name="protocol-messages"></a>Protokoll meddelanden

### <a name="init-message"></a>Init-meddelande

#### <a name="direction"></a>Riktning

Klient-> Azure-attestering

#### <a name="payload"></a>Innehållet

```
{
  "type": "aikcert"
}
```

"typ" (ASCII-sträng): representerar den typ av attestering som begärs. För närvarande stöds endast "aikcert".

### <a name="challenge-message"></a>Utmanings meddelande

#### <a name="direction"></a>Riktning

Azure-attestering – >-klient

#### <a name="payload"></a>Innehållet

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**Challenge** (BASE64URL (oktetter)): slumpmässigt värde som utfärdats av tjänsten.

**service_context** (BASE64URL (oktetter)): ogenomskinlig, krypterad kontext som skapats av tjänsten, bland annat, utmaningen och förfallo tid för den utmaningen.


### <a name="request-message"></a>Begär ande meddelande

#### <a name="direction"></a>Riktning

Klient-> Azure-attestering 

#### <a name="payload"></a>Innehållet

```
{

  "request": "<JWS>"
  
}
```

**Request** (JWS): begäran består av en JWS-struktur (JSON Web Signature). JWS skyddade huvud-och JWS-nyttolasten visas nedan. Som i någon JWS-struktur består det slutliga värdet av:

BASE64URL (UTF8 (JWS skyddat huvud)) | | '.' ||

BASE64URL (JWS-nyttolast) | | '.' ||

BASE64URL (JWS Signature)

##### <a name="jws-protected-header"></a>JWS skyddat huvud

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS-nyttolast

JWS-nyttolasten kan vara av typen Basic eller VBS. Basic används när bestyrkande bevis inte innehåller VBS-data.

Basic-exempel

``` 
{
  "att_type": "basic",
  "att_data": {
    "rp_id": "<URL>",
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
    "challenge": "<BASE64URL(CHALLENGE)>",
    "tpm_att_data": {
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
      "aik_pub": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "current_claim": "<BASE64URL(CURRENTCLAIM)>",
      "boot_claim": "<BASE64URL(BOOTCLAIM)>"
    },
    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
    "attest_key": {
      "kty": "RSA",
      "n": "<Base64urlUInt(MODULUS)>",
      "e": "<Base64urlUInt(EXPONENT)>"
    },
    "custom_claims": [
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      },
      {
        "name": "<name>",
        "value": "<value>",
        "value_type": "<value_type>"
      }
    ],
    "service_context": "<BASE64URL(SERVICECONTEXT)>"
  }
}
```

VBS-exempel

``` 
{
  "att_type": "vbs",
  "att_data": {
    "report_signed": {
      "rp_id": "<URL>",
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>",
      "challenge": "<BASE64URL(CHALLENGE)>",
      "tpm_att_data": {
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>",
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>",
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>",
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>",
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>",
        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517).
        "aik_pub": {
          "kty": "RSA",
          "n": "<Base64urlUInt(MODULUS)>",
          "e": "<Base64urlUInt(EXPONENT)>"
        },
        "current_claim": "<BASE64URL(CURRENTCLAIM)>",
        "boot_claim": "<BASE64URL(BOOTCLAIM)>"
      },
      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517).
      "attest_key": {
        "kty": "RSA",
        "n": "<Base64urlUInt(MODULUS)>",
        "e": "<Base64urlUInt(EXPONENT)>"
      },
      "custom_claims": [
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        },
        {
          "name": "<name>",
          "value": "<value>",
          "value_type": "<value_type>"
        }
      ],
      "service_context": "<BASE64URL(SERVICECONTEXT)>"
    },
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (StringOrURI): identifierare för förlitande part. Används av tjänsten vid beräkning av dator-ID-anspråk

**rp_data** (BASE64URL (oktetter)): täckande data som skickas av den förlitande parten. Detta används vanligt vis av den förlitande parten som ett nonce för att garantera rapportens aktualitet

**Challenge** (BASE64URL (oktetter)): slumpmässigt värde utfärdat av tjänsten

**tpm_att_data**: TPM-relaterade attesterings data

- **srtm_boot_log (BASE64URL (oktetter))**: SRTM start logg som hämtas av Function Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (oktetter))**: SRTM återuppta loggen som hämtats av funktionen Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (oktetter))**: drtm start logg som hämtas av Function Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (oktetter))**: drtm återuppta loggen som hämtats av funktionen Tbsi_Get_TCG_Log_Ex med loggtyp = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (oktetter))**: X. 509-certifikatet för AIK som returneras av Function NCryptGetProperty med property = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: den offentliga delen av AIK visas som ett JWK-objekt (JSON Web Key) (RFC 7517)

- **current_claim (BASE64URL (oktetter))**: attesterings anspråket för det aktuella PCR-läget som returneras av Function NCryptCreateClaim med dwClaimType = NCRYPT_CLAIM_PLATFORM och parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ange att inkludera alla PCRs. Anropet som skickas av tjänsten bör också användas för att beräkna detta anspråk

- **boot_claim (BASE64URL (oktetter))**: attesterings anspråket för PCR-tillstånd vid start som returneras av Function NCryptCreateClaim med dwClaimType = NCRYPT_CLAIM_PLATFORM och parameter NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ange att inkludera alla PCRs

**vbs-rapport** (BASE64URL (oktetter)): rapporten vbs enklaven-attestering som returneras av Function EnclaveGetAttestationReport. EnclaveData-parametern måste vara SHA-512-hashvärdet för värdet för report_signed (inklusive inledande och avslutande klammerparenteser). Inmatade hash-funktioner är UTF8 (report_signed)

**attest_key**: den offentliga delen av enklaven-nyckeln som representeras som ett JWK-objekt (JSON Web Key) (RFC 7517)

**custom_claims**: matris med anpassade enklaven-anspråk som skickas till tjänsten som kan utvärderas av principen. Anspråket

- **namn (sträng)**: namnet på anspråket. Det här namnet läggs till i en URL som fastställs av attesterings tjänsten (för att undvika konflikter) och den sammanfogade strängen blir den typ av anspråk som kan användas i principen

- **värde (sträng)**: värde för anspråket

- **value_type (sträng)**: data typen för anspråkets värde

**service_context** (BASE64URL (oktetter)): ogenomskinlig, krypterad kontext som skapats av tjänsten, bland annat, utmaningen och förfallo tid för den utmaningen.

### <a name="report-message"></a>Rapport meddelande

#### <a name="direction"></a>Riktning

Azure-attestering – >-klient

#### <a name="payload"></a>Innehållet

```
{
  "report": "<JWT>"
}
```

**rapport** (JWT): rapport om attestering i JSON Web token (JWT) (RFC 7519).
