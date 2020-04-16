---
title: Övervaka och hantera processen för att skapa certifikat
description: Scenarier som visar en rad alternativ för att skapa, övervaka och interagera med processen för att skapa certifikat med Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 02e13ce81ed2f11c0bb69015a4864c4a1ad55593
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430973"
---
# <a name="monitor-and-manage-certificate-creation"></a>Övervaka och hantera processen för att skapa certifikat
Gäller: Azure

De scenarier / åtgärder som beskrivs i den här artikeln är:

- Begära ett KV-certifikat med en utfärdare som stöds
- Hämta väntande begäran - begäran status är "inProgress"
- Hämta väntande begäran - begäran status är "klar"
- Hämta väntande begäran - väntande begäran status är "annullerad" eller "misslyckades"
- Hämta väntande begäran - väntande begäran status är "borttagen" eller "överskriven"
- Skapa (eller Importera) när väntande begäran finns - status är "inProgress"
- Koppla när väntande begäran skapas med en utfärdare (digiCert, till exempel)
- Begär en annullering medan den väntande begäran status är "inProgress"
- Ta bort ett väntande begärandeobjekt
- Skapa ett KV-certifikat manuellt
- Koppla när en väntande begäran skapas - manuell certifikatgenerering

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Begära ett KV-certifikat med en utfärdare som stöds 

|Metod|URI för förfrågan|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Följande exempel kräver att ett objekt med namnet "mydigicert" redan är tillgängligt i nyckelvalvet med utfärdaren som DigiCert. Certifikatutfärdaren är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan till ett KV-certifikat. utfärdarens namn, leverantör, autentiseringsuppgifter och annan administrativ information.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Hämta väntande begäran - begäran status är "inProgress"

|Metod|URI för förfrågan|
|------------|-----------------|
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Förfrågan
Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Om *request_id* anges i frågan fungerar det som ett filter. Om *request_id* i frågan och i det väntande objektet är olika returneras en http-statuskod på 404.

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

## <a name="get-pending-request---request-status-is-complete"></a>Hämta väntande begäran - begäran status är "klar"

### <a name="request"></a>Förfrågan

|Metod|URI för förfrågan|
|------------|-----------------|
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Hämta väntande begäran - väntande begäran status är "annullerad" eller "misslyckades"

### <a name="request"></a>Förfrågan

|Metod|URI för förfrågan|
|------------|-----------------|
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Värdet på *felkoden* kan vara "Certifikatutfärdarefel" eller "Begäran avvisad" baserat på utfärdarens eller användarfelet.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Hämta väntande begäran - väntande begäran status är "borttagen" eller "överskriven"
Ett väntande objekt kan tas bort eller skrivas över av en skapa/import-åtgärd när dess status inte är "inProgress".

|Metod|URI för förfrågan|
|------------|-----------------|
|HÄMTA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Förfrågan
Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Få`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Svar

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Skapa (eller Importera) när väntande begäran finns - status är "inProgress"
Ett väntande objekt har fyra möjliga lägen. "inprogress", "canceled", "failed" eller "completed".

När en väntande begärans tillstånd är "inprogress" misslyckas (och importerar) åtgärder med en http-statuskod på 409 (konflikt).

Så här åtgärdar du en konflikt:

- Om certifikatet skapas manuellt kan du antingen fylla i KV-certifikatet genom att göra en koppling eller ta bort det väntande objektet.

- Om certifikatet skapas med en utfärdare kan du vänta tills certifikatet har slutförts, misslyckas eller avbryts. Du kan också ta bort det väntande objektet.

> [!NOTE]
> Om du tar bort ett väntande objekt kan x509-certifikatbegäran avbrytas med leverantören.

|Metod|URI för förfrågan|
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
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Koppla när väntande begäran skapas med en utfärdare
Koppling tillåts inte när ett väntande objekt skapas med en utfärdare men tillåts när dess tillstånd är "inProgress". 

Om begäran om att skapa x509-certifikatet misslyckas eller avbryts av någon anledning, och om ett x509-certifikat kan hämtas med out-of-band-medel, kan en sammanfogning göras för att slutföra KV-certifikatet.

|Metod|URI för förfrågan|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Förfrågan

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Svar

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Begär en annullering medan den väntande begäran status är "inProgress"
En avbokning kan endast begäras. En begäran kan avbrytas eller inte. Om en begäran inte är "inProgress" returneras en http-status på 400 (felaktig begäran).

|Metod|URI för förfrågan|
|------------|-----------------|
|Patch|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Förfrågan
Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Ta bort ett väntande begärandeobjekt

> [!NOTE]
> Om du tar bort det väntande objektet kan x509-certifikatbegäran avbrytas med leverantören.

|Metod|URI för förfrågan|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Förfrågan
Ta bort`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

ELLER

Ta bort`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="create-a-kv-certificate-manually"></a>Skapa ett KV-certifikat manuellt
Du kan skapa ett certifikat som utfärdats med en certifikatutfärdar som du väljer genom en manuell skapandeprocess. Ange namnet på utfärdaren till "Okänd" eller ange inte utfärdarfältet.

|Metod|URI för förfrågan|
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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Koppla när en väntande begäran skapas - manuell certifikatgenerering

|Metod|URI för förfrågan|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Förfrågan

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elementnamn|Krävs|Typ|Version|Beskrivning|
|------------------|--------------|----------|-------------|-----------------|
|x5c (på)|Ja|matris|\<introducera version>|X509 certifikatkedja som bas 64 strängmatris.|

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
            "validity_months": 12
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
