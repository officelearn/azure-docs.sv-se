---
title: Namngivna entiteter för personlig information
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800166"
---
## <a name="personal-information-entity-types"></a>Enhets typer för personlig information:

### <a name="phone-number"></a>Telefonnummer

Telefonnummer. 

Språk:

* Offentlig för hands version: `English`

| Under typs namn           | Beskrivning                                           |
|------------------------|-------------------------------------------------------|
| Saknas                    | Telefonnummer, till exempel `+1 123-123-123`.          |
| EU-telefonnummer        | Telefonnummer som är speciellt för Europeiska unionen.         |
| Mobiltelefon nummer för EU | Mobiltelefon nummer som är speciella för Europeiska unionen. |

### <a name="eu-gps-coordinates"></a>EU GPS-koordinater

 GPS-koordinater för platser inom Europeiska unionen. 

Språk:

* Offentlig för hands version: `English`

| Under typs namn | Beskrivning                               |
|--------------|-------------------------------------------|
| Saknas          | GPS-koordinater inom Europeiska unionen |

### <a name="azure-information"></a>Azure-information

Identifierbar Azure-information inklusive autentiseringsinformation och anslutnings strängar. 

Språk:

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

Språk:

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
