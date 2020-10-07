---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779584"
---
> [!NOTE]
> Använd `domain=phi` parametern och modell versionen eller senare för att identifiera skyddad hälso information (Phi) `2020-04-01` .
>
> Exempelvis: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
Följande enhets kategorier returneras när du skickar begär anden till `/v3.1-preview.2/entities/recognition/pii` slut punkten.

| Kategori   | Underkategori | Beskrivning                          | Startar modell version | Kommentarer |
|------------|-------------|--------------------------------------|------------------------|---|
| Person     | Ej tillämpligt         | Namn på personer.  | `2019-10-01`  | Returneras också med `domain=phi` . |
| PersonType | Ej tillämpligt         | Jobb typer eller roller som innehas av en person. | `2020-02-01` | |
| PhoneNumber | Ej tillämpligt | Telefonnummer (endast USA och EU-telefonnummer). | `2019-10-01` | Returneras också med `domain=phi` . |
|Organisation  | Ej tillämpligt | Företag, politiska grupper, musik band, sport klubbar, myndighets organ och offentliga organisationer.  | `2019-10-01` | Nationella objekt och religions ingår inte i den här entitetstypen.  |
|Organisation | Sjukdom | Medicinska företag och grupper. | `2020-04-01` |  |
|Organisation | Börs kurs | Fond börs grupper. | `2020-04-01` |  |
| Organisation | Sport | Idrotts relaterade organisationer. | `2020-04-01` |  |
| Adress | Ej tillämpligt | Fullständiga post adresser.  | `2020-04-01` | Returneras också med `domain=phi` . |
| EU GPS-koordinater | Ej tillämpligt | GPS-koordinater för platser inom Europeiska unionen.  | `2019-10-01` |  |
| E-post | Ej tillämpligt | E-postadresser. | `2019-10-01` | Returneras också med `domain=phi` .   |
| URL | Ej tillämpligt | URL: er till webbplatser. | `2019-10-01` | Returneras också med `domain=phi` . |
| IP-adress | Ej tillämpligt | Nätverks-IP-adresser. | `2019-10-01` | Returneras också med `domain=phi` . |
| DateTime | Ej tillämpligt | Datum och tidpunkter på dagen. | `2019-10-01` |  | 
| DateTime | Datum | Kalender datum. | `2019-10-01` | Returneras också med `domain=phi` . |
| Kvantitet | Ej tillämpligt | Siffror och numeriska kvantiteter. | `2019-10-01` |  |
| Quantity | Ålder | Personer. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure-information

Den här enhets kategorin innehåller identifierbar Azure-information, inklusive autentiseringsinformation och anslutnings strängar. Tillgängligt från och med modell version `2019-10-01` . Returnerades inte med `domain=phi` parametern.

| Underkategori                           | Beskrivning                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB auth-nyckel             | Auktoriseringskod för en Azure Cosmos DB-server.                           |
| Anslutnings sträng för Azure IAAS Database och Azure SQL-anslutningssträng | Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS) och SQL-anslutningssträng. |
| Azure SQL-anslutningssträng           | Anslutnings sträng för en databas i Azure SQL Database.                                |
| Azure IoT-anslutningssträng           | Anslutnings sträng för Azure IoT.                        |
| Lösen ord för Azure publicerings inställning        | Lösen ord för Azures publicerings inställningar.                                        |
| Azure Redis Cache anslutnings sträng   | Anslutnings sträng för Redis-cache.                             |
| Azure SAS                             | Anslutnings sträng för Azure program vara som en tjänst (SaaS).                     |
| Azure Service Bus anslutnings sträng   | Anslutnings sträng för en Azure Service Bus.                                 |
| Azure Storage konto nyckel             | Konto nyckel för ett Azure Storage-konto.                                   |
| Azure Storage konto nyckel (allmän)   | Allmän konto nyckel för ett Azure Storage-konto.                           |
| SQL Server anslutnings sträng          | Anslutnings sträng för en dator som kör SQL Server.                                         |

## <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
