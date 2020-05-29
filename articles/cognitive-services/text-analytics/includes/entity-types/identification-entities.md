---
title: Identifiering av entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140959"
---
Den här enhets kategorin innehåller finansiell information och officiella formulär för identifiering. Tillgängligt från och med modell version `2019-10-01` . Under typer visas nedan. 

### <a name="financial-account-identification"></a>Identifiering av finansiella konton

| Under typs namn               | Description                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-nummer        | ABA-flöde (American Banks Association).                  |
| SWIFT-kod                 | SWIFT-koder för information om betalnings instruktioner.                           |
| Kreditkort                | Kreditkorts nummer.                                                       |
| Internationellt bankkontonummer (IBAN)                  | IBAN-koder för information om betalnings instruktioner.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identifiering av myndigheter och land/region

> [!NOTE]
> Följande finansiella och landsspecifika entiteter returneras inte med `domain=phi` parametern:
> * Passport-nummer
> * Skatte-ID

Entiteterna nedan grupperas och listas efter land:

Argentina
* DNI-nummer (Argentina National Identity)

Australien
* Australien Passport-nummer
* Australien, skatte fil nummer
* Enhets licens nummer för Australien
* Australien, medicin konto nummer
* Bank konto nummer för Australien

Belgien
* Belgien National Number

Brasilien 
* Brasilien juridisk enhets nummer (CNPJ)
* Brasilien CPF-nummer
* Brasilien National ID-kort (RG)

Kanada
* Kanadas sociala försäkrings nummer
* Licens nummer för Kanadas driv rutin
* Bank konto nummer för Kanada
* Canada Passport-nummer
* Canada personal Health Identification Number (PHIN)
* Kanada Hälsotjänst nummer

Chile
* Identitets korts nummer 

Kina
* PRC-nummer (Folkrepubliken Kina)

Kroatien
* Nummer för Kroatien identitets kort
* OIB-nummer (Kroatien personal Identification)

Tjeckien
* Tjeckiska personal Identity Number

Danmark
* Danmark personal identifikations nummer

Europeiska unionen (EU)
* EU: s nationella identifierings nummer
* EU Passport-nummer
* Licens nummer för EU-drivrutin
* EU: s socialförsäkrings nummer (SSN) eller motsvarande ID
* EU: s skatte identifierings nummer (TIN)
* EU betalkort nummer

Finland
* Nationella ID för Finland
* Nummer för Finland Passport

Frankrike
* Franskt nationellt ID-kort (CNI)
* Frankrike, socialförsäkrings nummer (INSEE)
* Franskt Passport-nummer
* Fransk driv Rutins licens nummer

Tyskland
* Kort nummer för Tyskland-identitet
* Tyskt Passport-nummer
* Tysk kör korts nummer

Grekland 
* Kort nummer för nationella Grekland-ID

Hongkong
* HKID-nummer (Hong Kong Identity Card)

Indien
* Permanent konto nummer för Indien (PAN)
* Aadhaar-nummer (Indien Unique Identification)

Indonesien
* KTP-nummer (Indonesien Identity Card)

Irland
* Irland personal public service (PPS)-nummer

Israel
* Israel National ID
* Israel bank konto nummer

Italien
* Italien-driv Rutinens licens-ID

Japan
* Japan, inhemskt registrerings nummer
* Japanskt boende korts nummer
* Japansk driv Rutins licens nummer
* Socialt försäkrings nummer (SIN)
* Japanskt Passport-nummer
* Japanskt bank konto nummer

Malaysia
* Kort nummer för Malaysia-ID

Nederländerna
* Nederländskt BSN-nummer (Nederländernas unionsmedborgare)

Nya Zeeland
* New Zeelands ministeriet för hälso tillstånd

Norge
* ID-nummer för Norge

Filippinerna
* Filippinerna-enhetligt ID-nummer för flera syften

Polen
* Kort för Polen-identitet
* Polen National ID (PESEL)
* Polen Passport

Portugal 
* Kort nummer för Portugal, unionsmedborgare

Saudiarabien
* Nationellt ID för Saudiarabien

Singapore
* NRIC-nummer (National registration ID Card) i Singapore

Sydafrika
* FN-nummer för Sydafrika

Sydkorea
* Sydkorea, inhemskt registrerings nummer

Spanien 
* Spaniens person nummer (SSN)

Sverige
* National Sverige-ID
* Sverige Passport-nummer

Taiwan 
* Nationella ID för Taiwan
* Inhemskt Taiwan-certifikat (ARC/TARC)
* Taiwanesiskt Passport-nummer

Thailand
* Identifierings kod för Thai-population

Storbritannien
* Passport-ID
* engelska Kör korts nummer
* engelska Nationellt försäkrings nummer (NINO)
* engelska National Hälsotjänst Number
* engelska Nummer för val av rulle
* USA/STORBRITANNIEN Passport-nummer

USA
* Amerikanskt socialförsäkrings nummer (SSN)
* Amerikansk kör korts nummer
* USA/STORBRITANNIEN Passport-nummer
* Amerikanskt individuellt Taxpayer Identification Number (ITIN)
* Nummer för verk ställande av narkotika (DIETANOLAMIN)
* Konto nummer för amerikansk bank
