---
title: Namngivna enheter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086893"
---
## <a name="personal-information-entity-types"></a>Enhetstyper för personlig information:

### <a name="person"></a>Person
Känn igen personnamn i text.

Språk:
* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                               | Tillgänglig från och med modellversion |
|--------------|-----------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Erkända namn, `Bill Gates`till exempel ,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organisation  

Erkänn organisationer, företag, byråer, företag, klubbar och andra grupper av människor.

Språk: 

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                                                                       | Tillgänglig från och med modellversion|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| Ej tillämpligt          | organisationer, till `Microsoft`exempel `NASA`,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer (endast amerikanska telefonnummer). 

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                    | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Amerikanska telefonnummer, till exempel`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>E-post

E-postadress. 

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                      | Tillgänglig från och med modellversion |
|--------------|--------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | E-postadress, till exempel`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

Internet-URL:er.

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                                          | Tillgänglig från och med modellversion |
|--------------|------------------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Webbadresser till webbplatser, till exempel`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP-adress

Internet-protokolladress

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                              | Tillgänglig från och med modellversion |
|--------------|------------------------------------------|----------------------------------------|
| Ej tillämpligt          | Nätverksadress till exempel`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Kvantitet 

Numeriska kvantiteter

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                   | Tillgänglig från och med modellversion |
|--------------|-------------------------------|----------------------------------------|
| Ålder          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Datum- och tidsenheter

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                   | Tillgänglig från och med modellversion |
|--------------|-------------------------------|----------------------------------------|
| Datum         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU:s GPS-koordinater

 GPS-koordinater för platser inom Europeiska unionen. 

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn | Beskrivning                               | Tillgänglig från och med modellversion |
|--------------|-------------------------------------------|----------------------------------------|
| Ej tillämpligt          | GPS-koordinater inom Europeiska unionen | `2019-10-01`                           |

### <a name="azure-information"></a>Azure-information

Identifierbar Azure-information inklusive autentiseringsinformation och anslutningssträngar. 

* Tillgänglig från och `2019-10-01`med modellversion .

Språk:

* Offentlig förhandsgranskning:`English`

| Undertypsnamn                          | Beskrivning                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Auth-nyckel för Azure DocumentDB             | Auktoriseringsnyckel för en Azure DocumentDB-server.                           |
| Anslutningssträng för Azure IAAS-databas | Anslutningssträng för en Azure Infrastructure som tjänstdatabas (IaaS). |
| Azure SQL-anslutningssträng           | Anslutningssträng för en Azure SQL-databas.                                |
| Anslutningssträng för Azure IoT           | Anslutningssträng för Azure Internet of things(IoT).                        |
| Lösenord för Azure-publiceringsinställning        | Lösenord för Azure Publish-inställningar.                                        |
| Anslutningssträng för Azure Redis-cache   | Anslutningssträng för en Azure-cache för Redis.                             |
| Azure SAS                             | Anslutningssträng för Azure Software som tjänst (SAS).                     |
| Sträng för Azure Service Bus-anslutning   | Anslutningssträng för en Azure-tjänstbuss.                                |
| Nyckeln för Azure Storage-konto             | Kontonyckel för ett Azure-lagringskonto.                                   |
| Azure Storage-kontonyckel (allmän)   | Allmän kontonyckel för ett Azure-lagringskonto.                           |
| Anslutningssträng för SQL Server          | Anslutningssträng för en SQL-server.                                         |

### <a name="identification"></a>Identification

* Tillgänglig från och `2019-10-01`med modellversion .

Språk:

* Offentlig förhandsgranskning:`English`

#### <a name="financial-account-identification"></a>Identifiering av finansiella konton

| Undertypsnamn               | Beskrivning                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-routningsnummer        | American Banker Association (ABA) transit routing nummer.                  |
| SWIFT-kod                 | SWIFT-koder för betalningsinstruktionsinformation.                           |
| Kreditkort                | Kreditkortsnummer.                                                       |
| IBAN-kod                  | IBAN-koder för betalningsinstruktionsinformation.                            |

#### <a name="government-and-country-specific-identification"></a>Regeringen och landsspecifik identifiering

Enheterna nedan är grupperade och listade efter land:

Argentina
* Nationellt identitetsnummer (DNI)

Australien
* Momsfilnummer 
* Körkorts-ID
* Pass-ID
* Medicinskt kontonummer
* bankkontonummer (t.ex. checkkonton, sparkonton och debetkonton)

Belgien
* Nationellt nummer

Brasilien
* Nummer för juridisk person (CNPJ)
* CPF-nummer
* Nationellt ID-kort (RG)

Kanada
* Pass-ID
* Körkorts-ID
* Sjukförsäkringsnummer
* Phin -nummer för personlig hälsa (PHIN)
* Personnummer
* bankkontonummer (t.ex. checkkonton, sparkonton och debetkonton)

Chile
* Id-kortsnummer 

Kina
* Id-kortsnummer
* ID-kort (Kina)

Kroatien
* ID-kortsnummer
* OIB-nummer (Personal ID)

Tjeckien
* Nationellt ID-kortsnummer

Danmark
* Personnummer

Europeiska unionen (EU)
* Nationellt ID-nummer
* Pass-ID
* Körkorts-ID
* Personnummer (SSN) eller motsvarande ID
* EU:s skatteregistreringsnummer (TIN)
* EU-betalkortsnummer

Finland
* Nationellt ID-nummer
* Pass-ID

Frankrike
* Nationellt ID-kort (CNI)
* Personnummer (INSEE)
* Pass-ID
* Körkorts-ID

Tyskland
* ID-kortsnummer
* Pass-ID
* Körkorts-ID

Grekland 
* Nationellt ID-kortsnummer

Hongkong
* ID-kort (HKID) nummer

Indien
* Permanent kontonummer (PAN)
* Unikt ID-nummer (Aadhaar)

Indonesien
* ID-kortnummer (KTP)

Irland
* PPS-nummer (Personal Public Service)

Israel
* Nationellt ID
* bankkontonummer (t.ex. checkkonton, sparkonton och debetkonton)

Italien
* Körkorts-ID

Japan
* Residentt registreringsnummer
* Uppehållskortnummer
* Körkorts-ID
* Socialförsäkringsnummer (SIN)
* Pass-ID
* bankkontonummer (t.ex. checkkonton, sparkonton och debetkonton)

Malaysia
* ID-kortsnummer

Nederländerna
* Medborgarens tjänst (BSN) nummer

Nya Zeeland
* Hälsoministeriets nummer

Norge
* ID-kortsnummer

Filippinerna
* Enhetligt nummer för multifunktions-ID

Polen
* ID-kortsnummer
* Nationellt ID (PESEL)
* Pass-ID

Portugal 
* Medborgare kortnummer

Saudiarabien
* Nationellt ID

Singapore
* NRIC-nummer (National Registration ID Card)

Sydafrika
* ID-nummer
* Organisationsnummer

Sydkorea
* Resident registreringsnummer

Spanien 
* Personnummer (SSN)

Sverige
* Nationellt ID
* Pass-ID

Taiwan 
* Nationellt ID
* Resident certifikat (ARC/TARC) nummer
* Pass-ID

Thailand
* Befolkningsidentifieringskod

Storbritannien
* Pass-ID
* Körkorts-ID
* Personnummer (NINO)
* Nationellt hälso- och sjukvårdsnummer (NHS)

USA
* Personnummer (SSN)
* Körkorts-ID
* Pass-ID
* Röstlängdsnummer
* Individuellt skatte-ID-nummer (ITIN)
* Nummer hos Drug Enforcement Agency (DEA)
* bankkontonummer (t.ex. checkkonton, sparkonton och debetkonton)
