---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086893"
---
## <a name="personal-information-entity-types"></a>Enhets typer för personlig information:

### <a name="person"></a>Person
Identifiera person namn i text.

Användning
* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                               | Tillgängligt från och med modell version |
|--------------|-----------------------------------------------------------|----------------------------------------|
| SAKNAS          | Identifierade namn, till exempel `Bill Gates`, `Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organisation  

Identifiera organisationer, organisationer, organ, företag, klubbar och andra grupper av människor.

Användning 

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                                                                       | Tillgängligt från och med modell version|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| SAKNAS          | organisationer, till exempel `Microsoft``NASA`, `National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefonnummer

Telefonnummer (endast telefonnummer till USA). 

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                    | Tillgängligt från och med modell version |
|--------------|------------------------------------------------|----------------------------------------|
| SAKNAS          | AMERIKANSKt telefonnummer, till exempel `(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>E-post

E-postadress. 

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                      | Tillgängligt från och med modell version |
|--------------|--------------------------------------------------|----------------------------------------|
| SAKNAS          | E-postadress, till exempel `support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

Internet-URL: er.

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                                          | Tillgängligt från och med modell version |
|--------------|------------------------------------------------------|----------------------------------------|
| SAKNAS          | URL: er till webbplatser, till exempel `https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP-adress

Internet Protocol adress

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                              | Tillgängligt från och med modell version |
|--------------|------------------------------------------|----------------------------------------|
| SAKNAS          | Nätverks adress till exempel `10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Kvantitet 

Numeriska kvantiteter

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                   | Tillgängligt från och med modell version |
|--------------|-------------------------------|----------------------------------------|
| Ålder          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Datum-och tid enheter

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                   | Tillgängligt från och med modell version |
|--------------|-------------------------------|----------------------------------------|
| Date         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU GPS-koordinater

 GPS-koordinater för platser inom Europeiska unionen. 

Användning

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                               | Tillgängligt från och med modell version |
|--------------|-------------------------------------------|----------------------------------------|
| SAKNAS          | GPS-koordinater inom Europeiska unionen | `2019-10-01`                           |

### <a name="azure-information"></a>Azure-information

Identifierbar Azure-information inklusive autentiseringsinformation och anslutnings strängar. 

* Tillgängligt från och med modell version `2019-10-01`.

Användning

* Offentlig för hands version: `English`

| Under typs namn                          | Beskrivning                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB auth-nyckel             | Auktoriseringsregel för en Azure DocumentDB-Server.                           |
| Anslutnings sträng för Azure IAAS Database | Anslutnings sträng för en Azure Infrastructure as a service-databas (IaaS). |
| Azure SQL-anslutningssträng           | Anslutnings sträng för en Azure SQL-databas.                                |
| Azure IoT-anslutningssträng           | Anslutnings sträng för Azure sakernas internet (IoT).                        |
| Lösen ord för Azure publicerings inställning        | Lösen ord för Azures publicerings inställningar.                                        |
| Azure Redis Cache anslutnings sträng   | Anslutnings sträng för en Azure-cache för Redis.                             |
| Azure SAS                             | Anslutnings sträng för Azure program vara som en tjänst (SAS).                     |
| Azure Service Bus anslutnings sträng   | Anslutnings sträng för en Azure Service Bus.                                |
| Azure Storage konto nyckel             | Konto nyckel för ett Azure Storage-konto.                                   |
| Azure Storage konto nyckel (allmän)   | Allmän konto nyckel för ett Azure Storage-konto.                           |
| SQL Server anslutnings sträng          | Anslutnings sträng för en SQL-Server.                                         |

### <a name="identification"></a>Fastställa

* Tillgängligt från och med modell version `2019-10-01`.

Användning

* Offentlig för hands version: `English`

#### <a name="financial-account-identification"></a>Identifiering av finansiella konton

| Under typs namn               | Beskrivning                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-routing-nummer        | ABA-flöde (American Banks Association).                  |
| SWIFT-kod                 | SWIFT-koder för information om betalnings instruktioner.                           |
| Kreditkort                | Kreditkorts nummer.                                                       |
| IBAN-kod                  | IBAN-koder för information om betalnings instruktioner.                            |

#### <a name="government-and-country-specific-identification"></a>Myndighets-och landsspecifika identifiering

Entiteterna nedan grupperas och listas efter land:

Argentina
* Nationellt identitets nummer (DNI)

Australien
* Skatte fil nummer 
* Driv Rutinens licens-ID
* Passport-ID
* Konto nummer för medicin
* bankkonto nummer (till exempel kontroll, besparingar och debiterings konton)

Belgien
* Nationellt nummer

Brasilien
* Juridiskt enhets nummer (CNPJ)
* CPF-nummer
* Nationellt ID-kort (RG)

Kanada
* Passport-ID
* Driv Rutinens licens-ID
* Hälso försäkrings nummer
* Personligt hälso-ID-nummer (PHIN)
* Person nummer
* bankkonto nummer (till exempel kontroll, besparingar och debiterings konton)

Chile
* Identitets korts nummer 

Kina
* Identitets korts nummer
* Nummer för Resident ID-kort (Kina)

Kroatien
* ID-kort nummer
* Personligt ID-nummer (OIB)

Tjeckien
* Nummer för nationellt ID-kort

Danmark
* Personligt ID-nummer

Europeiska unionen (EU)
* Nationellt ID-nummer
* Passport-ID
* Driv Rutinens licens-ID
* Person nummer (SSN) eller motsvarande ID
* EU: s skatte identifierings nummer (TIN)
* EU betalkort nummer

Finland
* Nationellt ID-nummer
* Passport-ID

Frankrike
* Nationellt ID-kort (CNI)
* Person nummer (INSEE)
* Passport-ID
* Driv Rutinens licens-ID

Tyskland
* ID-kort nummer
* Passport-ID
* Driv Rutinens licens-ID

Grekland 
* Nummer för nationellt ID-kort

Hongkong SAR
* ID Card-nummer (HKID)

Indien
* Permanent konto nummer (PAN)
* Unikt ID-nummer (Aadhaar)

Indonesien
* ID-kort nummer (KTP)

Irland
* Personligt offentlig tjänst (PPS)-nummer

Israel
* Nationellt ID
* bankkonto nummer (till exempel kontroll, besparingar och debiterings konton)

Italien
* Driv Rutinens licens-ID

Japan
* Inhemskt registrerings nummer
* Nummer för boende kort
* Driv Rutinens licens-ID
* Socialt försäkrings nummer (SIN)
* Passport-ID
* bankkonto nummer (till exempel kontroll, besparingar och debiterings konton)

Malaysia
* ID-kort nummer

Nederländerna
* BSN-nummer (medborgarnas service)

Nya Zeeland
* Hälso tillståndets ministerium

Norge
* ID-kort nummer

Filippinerna
* Enhetligt ID-nummer för flera syften

Polen
* ID-kort nummer
* National-ID (PESEL)
* Passport-ID

Portugal 
* Unionsmedborgare-kort nummer

Saudiarabien
* Nationellt ID

Singapore
* Nummer för National registration ID Card (NRIC)

Sydafrika
* ID-nummer
* Inhemskt registrerings nummer

Sydkorea
* Inhemskt registrerings nummer

Spanien 
* Person nummer (SSN)

Sverige
* Nationellt ID
* Passport-ID

Taiwan 
* Nationellt ID
* Nummer för residentt certifikat (ARC/TARC)
* Passport-ID

Thailand
* Identifierings kod för population

Storbritannien
* Passport-ID
* Driv Rutinens licens-ID
* Nationellt försäkrings nummer (NINO)
* NHS-nummer (National Hälsotjänst)

USA
* Person nummer (SSN)
* Driv Rutinens licens-ID
* Passport-ID
* Nummer för val av rulle
* Individuellt skatte-ID-nummer (ITIN)
* Nummer för verk ställande av narkotika (DIETANOLAMIN)
* bankkonto nummer (till exempel kontroll, besparingar och debiterings konton)
