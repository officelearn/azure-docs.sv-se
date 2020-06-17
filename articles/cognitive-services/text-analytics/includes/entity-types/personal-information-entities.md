---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: 637c2bb1bc37d26dcdf1a169b2f5d20766d0a27f
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84902473"
---
> [!NOTE]
> `PHI`Använd `domain=phi` parametern och modell versionen eller senare för att identifiera `2020-04-01` .
>
> Exempelvis: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Följande enhets kategorier returneras när begär Anden skickas till `/v3.1-preview.1/entities/recognition/pii` slut punkten.

| Kategori   | Underkategori | Description                          | Startar modell version | Anteckningar |
|------------|-------------|--------------------------------------|------------------------|---|
| Person     | Saknas         | Namn på personer.  | `2019-10-01`  | Returneras också med `domain=phi` . |
| PersonType | Saknas         | Jobb typer eller roller som innehas av en person. | `2020-02-01` | |
| PhoneNumber | Saknas | Telefonnummer (endast USA och EU-telefonnummer). | `2019-10-01` | Returneras också med`domain=phi` |
|Organisation  | Saknas | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  | `2019-10-01` | Nationella objekt och religions ingår inte i den här entitetstypen.  |
|Organisation | Sjukdom | Medicinska företag och grupper. | `2020-04-01` | Returneras också med `domain=phi` . |
|Organisation | Börs kurs | Fond börs grupper. | `2020-04-01` | Returneras också med `domain=phi` . |
| Organisation | Sport | Idrotts relaterade organisationer. | `2020-04-01` | Returneras också med `domain=phi` . |
| Adress | Saknas | Fullständiga post adresser.  | `2020-04-01` | Returneras också med `domain=phi` . |
| EU GPS-koordinater | Saknas | GPS-koordinater för platser inom Europeiska unionen.  | `2019-10-01` |  |
| E-post | Saknas | E-postadresser. | `2019-10-01` | Returneras också med `domain=phi` .   |
| URL | Saknas | URL: er till webbplatser. | `2019-10-01` | Returneras också med `domain=phi` . |
| IP-adress | Saknas | Nätverks-IP-adresser. | `2019-10-01` | |
| DateTime | Saknas | Datum och tidpunkter på dagen. | `2019-10-01` |  | 
| DateTime | Date | Kalender datum. | `2019-10-01` | Returneras också med `domain=phi` . |
| Kvantitet | Saknas | Siffror och numeriska kvantiteter. | `2019-10-01` |  |
| Kvantitet | Ålder | Personer. | `2019-10-01` | | |
| Internationell klassificering av sjukdomar (ICD-9-CM) | Saknas | Enheter som rör internationell klassificering av sjukdomar, nionde revidering.   | `2020-04-01` | |
| Internationell klassificering av sjukdomar (ICD-10-CM) | Saknas | Enheter som rör internationell klassificering av sjukdomar, tionde revidering.    | `2020-04-01` | |

## <a name="azure-information"></a>Azure-information

Den här enhets kategorin innehåller identifierbar Azure-information, inklusive autentiseringsinformation och anslutnings strängar. Tillgängligt från och med modell version `2019-10-01` . Returnerades inte med `domain=phi` parametern.

| Underkategori                           | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB auth-nyckel             | Auktoriseringsregel för en Azure DocumentDB-Server.                           |
| Anslutnings sträng för Azure IAAS Database och Azure SQL-anslutningssträng | Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS) och SQL-anslutningssträng. |
| Azure SQL-anslutningssträng           | Anslutnings sträng för en Azure SQL-databas.                                |
| Azure IoT-anslutningssträng           | Anslutnings sträng för Azure sakernas internet (IoT).                        |
| Lösen ord för Azure publicerings inställning        | Lösen ord för Azures publicerings inställningar.                                        |
| Azure Redis Cache anslutnings sträng   | Anslutnings sträng för en Azure-cache för Redis.                             |
| Azure SAS                             | Anslutnings sträng för Azure program vara som en tjänst (SAS).                     |
| Azure Service Bus anslutnings sträng   | Anslutnings sträng för en Azure Service Bus.                                 |
| Azure Storage konto nyckel             | Konto nyckel för ett Azure Storage-konto.                                   |
| Azure Storage konto nyckel (allmän)   | Allmän konto nyckel för ett Azure Storage-konto.                           |
| SQL Server anslutnings sträng          | Anslutnings sträng för en SQL-Server.                                         |

## <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
