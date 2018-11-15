---
title: Förstå Azure Digital Twins enhetsanslutning och autentisering | Microsoft Docs
description: Använd Azure Digital Twins att ansluta och autentisera enheter
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636245"
---
# <a name="create-and-manage-role-assignments"></a>Skapa och hantera rolltilldelningar

Azure Digital Twins använder rollbaserad åtkomstkontroll ([RBAC](./security-role-based-access-control.md)) att hantera åtkomst till resurser.

Varje rolltilldelningen omfattar:

* **Objekt-ID:**: en Azure Active Directory-ID, tjänstens huvudnamn objekt-ID eller domännamn
* **Typ av identifierare**
* **Rolldefinitions-ID**
* **Utrymme sökväg**
* **Klient-ID**: I de flesta fall en Azure Active Directory klient-ID

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>Rollen definition identifierare

I följande tabell visas vad som kan hämtas genom att fråga system/roller API.

| **Roll** | **identifierare** |
| --- | --- |
| Utrymme administratör | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Användaradministratör| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Enhetsadministratör | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Nyckel-administratör | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token-administratör | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Användare | b1ffdb77-c635-4E7E-ad25-948237d85b30 |
| Support-expert | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installationsprogram för enhet | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Gateway-enhet | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Stöds ObjectIdTypes

Den stöds `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

```plaintext
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Namn** | **Krävs** | **Typ** | **Beskrivning** |
| --- | --- | --- | --- |
| RoleId| Ja |Sträng | Rollen definition identifierare. Hitta roll- och deras identifierare genom att fråga systemet API. |
| objekt-ID | Ja |Sträng | Objekt-ID för den rolltilldelning som måste vara formaterad enligt dess associerade typ. För den `DomainName` ObjectIdType, ObjectId måste börja med den `“@”` tecken. |
| objectIdType | Ja |Sträng | Typ av rolltilldelningen. Måste vara något av följande rader i den här tabellen. |
| TenantId | Varierar | Sträng |Klient-ID. Tillåts inte för `DeviceId` och `TenantId` ObjectIdTypes. Krävs för `UserId` och `ServicePrincipalId` ObjectIdTypes. Valfritt för DomainName ObjectIdType. |
| sökvägen * | Ja | Sträng |Fullständig åtkomst till sökvägen till den `Space` objekt. Ett exempel är `/{Guid}/{Guid}`. Om en identifierare som behöver rolltilldelningen för hela diagrammet, ange `"/"`. Det här tecknet betecknar roten, men dess användning rekommenderas inte. Alltid följa principen för lägsta behörighet. |

## <a name="sample-configuration"></a>Exempel på konfiguration

En användare behöver administrativ åtkomst till en våning utrymme för en klient i det här exemplet.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

I det här exemplet kör ett program testscenarier simulerade enheter och sensorer.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Alla användare som tillhör en domän får läsbehörighet för blanksteg, sensorer och användare. Den här åtkomsten innehåller motsvarande relaterade objekt.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Använd får att få en rolltilldelning.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Namn** | **I** | **Krävs** |    **Typ** |  **Beskrivning** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | Sökväg | True | Sträng |    Den fullständiga sökvägen till området |

Använd ta bort för att ta bort en rolltilldelning.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Namn** | **I** | **Krävs** | **Typ** | **Beskrivning** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | Sökväg | True | Sträng | Tilldelnings-ID |

## <a name="next-steps"></a>Nästa steg

Läs om Azure Digital Twins säkerhet [API-autentisering](./security-authenticating-apis.md).
