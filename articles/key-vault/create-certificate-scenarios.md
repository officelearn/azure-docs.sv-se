---
title: Övervaka och hantera certifikat skapas
description: Scenarier som visar ett antal alternativ för att skapa, bearbeta övervakning och interagerar med certifikat att med Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 0d0995aa-b60d-4811-be12-ba0a45390197
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: e1ea77304fa59b67e0e28a4c7e0b13633eeeff6f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012214"
---
# <a name="monitor-and-manage-certificate-creation"></a>Övervaka och hantera certifikat skapas
Gäller för: Azure  

Följande 

Scenarier / åtgärder som beskrivs i den här artikeln är:

- Begära ett certifikat för KV med en utfärdare som stöds
- Hämta väntande begäran - begärandestatus är ”inProgress”
- Hämta väntande begäran - begärandestatus är ”klar”
- Hämta väntande begäran - väntande begäran status ”avbryts” eller ”misslyckades”
- Hämta väntande begäran - väntande begäran statusen ”borttagen” eller ”över”
- Skapa (eller importera) när väntande begäran finns - status är ”inProgress”
- Merge när väntande begäran har skapats med en utfärdare (till exempel DigiCert)
- Begär en annullering medan väntande begärandestatus är ”inProgress”
- Ta bort en väntande begäran-objekt
- Skapa ett KV certifikat manuellt
- Sammanfoga när en väntande begäran skapas - skapande av manuell certifikat

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Begära ett certifikat för KV med en utfärdare som stöds 

|Metod|Förfrågans URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

I följande exempel kräver ett objekt med namnet ”mydigicert” ska redan finnas i nyckelvalvet med utfärdaren leverantören som DigiCert. Mer information om hur du arbetar med utfärdare finns [certifikat utfärdare](/rest/api/keyvault/certificate-issuers.md).  

### <a name="request"></a>Förfrågan  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert",  
    "cty": "OV-SSL",  
    }  
  }  
}  

```  

### <a name="response"></a>Svar  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "mydigicert"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "InProgress",  
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-inprogress"></a>Hämta väntande begäran - begärandestatus är ”inProgress”

|Metod|Förfrågans URI|  
|------------|-----------------|  
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  
 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

> [!NOTE]
>  Om *request_id* har angetts i frågan, fungerar som ett filter. Om den *request_id* i frågan och väntande objekt är olika, returneras ett HTTP-statuskoden 404.  

### <a name="response"></a>Svar  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="get-pending-request---request-status-is-complete"></a>Hämta väntande begäran - begärandestatus är ”klar”

### <a name="request"></a>Förfrågan  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Svar  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "completed",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
}  

```  

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Hämta väntande begäran - väntande begäran status ”avbryts” eller ”misslyckades”  

### <a name="request"></a>Förfrågan  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 HÄMTA  `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Svar  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "failed",  
  "status_details": "",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
   "error":  
    {  
        "code": "<errorcode>",    
        "message": "<message>"  
    }  
}  

```  

> [!NOTE]
> Värdet för den *errorcode* kan vara ”certifikat utfärdaren error” eller ”begäran avvisades” baserat på företaget eller användaren fel respektive.  

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Hämta väntande begäran - väntande begäran statusen ”borttagen” eller ”över”  
 En väntande objekt kan tas bort eller skrivs över av skapa/importen när dess status inte är ”inProgress”.

|Metod|Förfrågans URI|  
|------------|-----------------|  
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  
 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 HÄMTA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Svar  

```  
StatusCode: 404, ReasonPhrase: 'Not Found'  
{  
  "error":  
    {  
         "code": "PendingCertificateNotFound",  
        "message": "…"  
    }  
}  

```  

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Skapa (eller importera) när väntande begäran finns - status är ”inProgress”
 En väntande objekt har fyra möjliga tillstånd; ”inprogress”, ”avbrutits”, ”misslyckades” eller ”klar”.

 En väntande begäran status är ”inprogress”, skapa (och importera) åtgärder misslyckas med en HTTP-statuskoden 409 (konflikt).  

 Att lösa en konflikt:  

 - Om certifikatet skapas manuellt, kan du antingen avsluta KV certifikatet genom att göra en sammanslagning eller ta bort på väntande objekt.  

 - Om certifikatet skapas med en utfärdare, kan du vänta tills certifikatet har slutförts, misslyckas eller avbryts. Alternativt kan du ta bort väntande objekt.

> [!NOTE]
> Ta bort en väntande objekt kan eller kan inte avbryta x509 certifikatbegäran med providern.  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    },  
  "issuer": {  
       "name": "mydigicert"  
    }  
  }  
}  

```  

### <a name="response"></a>Svar  

```  
StatusCode: 409, ReasonPhrase: 'Conflict'  
{  
  "error":  
    {  
        "code": "Forbidden",  
        "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."  
    }  
}  

```  

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Sammanfoga när väntande begäran har skapats med en utfärdare
 Merge tillåts inte när en väntande objekt har skapats med en utfärdare men tillåts när tillståndet är ”inProgress”. 

 Om en begäran att skapa x509 certifikat misslyckas eller avbryts av någon anledning och om x509 certifikatet kan hämtas av out-of-band-innebär en merge-operation kan göras för att slutföra KV certifikatet.  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

### <a name="response"></a>Svar  

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'  
{  
  "error":  
    {  
       "code": "Forbidden",  
       "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."  
    }  
}  

```  

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Begär en annullering medan väntande begärandestatus är ”inProgress”  
 En annullering kan endast begäras.  En begäran kan eller kan inte avbrytas. Om en begäran inte är ”inProgress”, returneras ett http-status 400 (felaktig begäran).  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  
 KORRIGERING `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 KORRIGERING `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

```json
{  
  "cancellation_requested": true  
}  

```  

### <a name="response"></a>Svar  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": true,  
  "status": "inProgress",  
  "status_details": "…",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="delete-a-pending-request-object"></a>Ta bort en väntande begäran-objekt  

> [!NOTE]
> Ta bort objektet väntande kan eller kan inte avbryta x509 certifikatbegäran med providern.  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  
 TA BORT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`  

 ELLER  

 TA BORT `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`  

### <a name="response"></a>Svar  

```  
StatusCode: 200, ReasonPhrase: 'OK'  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "{issuer-name}"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "cancellation_requested": false,  
  "status": "inProgress",  
  "request_id": "a76827a18b63421c917da80f28e9913d",  
}  
```  

## <a name="create-a-kv-certificate-manually"></a>Skapa ett KV certifikat manuellt  
 Du kan skapa ett certifikat med en Certifikatutfärdare önskat via en manuell skapandeprocessen. Ange ett namn för utfärdaren att ”okänt” eller inte anger fältet utfärdare.  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  

```json
{  
  "policy": {  
    "x509_props": {  
      "subject": "CN=MyCertSubject1"  
    }  
  "issuer": {  
       "name": "Unknown"  
    }  
  }  
}  

```  

### <a name="response"></a>Svar  

```  
StatusCode: 202, ReasonPhrase: 'Accepted'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"  
{  
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",  
  "issuer": {  
    "name": "Unknown"  
  },  
  "csr": "MIICq......DD5Lp5cqXg==",  
  "status": "inProgress",  
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",  
  "request_id": "a76827a18b63421c917da80f28e9913d"  
}  

```  

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Sammanfoga när en väntande begäran skapas - skapande av manuell certifikat  

|Metod|Förfrågans URI|  
|------------|-----------------|  
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|  

### <a name="request"></a>Förfrågan  

```json
{  
  "x5c": [  "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8="  
  ]  
}  

```  

|Elementnamn|Krävs|Typ|Version|Beskrivning|  
|------------------|--------------|----------|-------------|-----------------|  
|x5c|Ja|matris|\<Introduktion till version >|X509 certifikatkedjan som base 64 strängmatris.|  

### <a name="response"></a>Svar  

```  
StatusCode: 201, ReasonPhrase: 'Created'  
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"  
{  
"id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",  
    "cer": "……de34534……",  
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",  
    "attributes": {  
        "enabled": true,  
        "exp": 1530394215,  
        "nbf": 1435699215,  
        "created": 1435699919,  
        "updated": 1435699919  
    },  
    "pending": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"  
    },  
    "policy": {  
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",  
        "key_props": {  
            "exportable": false,  
            "kty": "RSA",  
            "key_size": 2048,  
            "reuse_key": false  
        },  
        "secret_props": {  
            "contentType": "application/x-pkcs12"  
        },  
        "x509_props": {  
            "subject": "CN=Mycert1",  
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],  
                       "validity_months":12  
        },  
        "lifetime_actions": [{  
            "trigger": {  
                "lifetime_percentage": 80  
            },  
            "action": {  
                "action_type": "EmailContacts"  
            }  
        }],  
        "issuer": {  
            "name": "Unknown"  
        },  
        "attributes": {  
            "enabled": true,  
            "created": 1435699811,  
            "updated": 1435699811  
        }  
    }  
}  

```

## <a name="see-also"></a>Se även
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
