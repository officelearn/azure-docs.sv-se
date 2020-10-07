---
title: Identifiering av entiteter
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779515"
---
Den här enhets kategorin innehåller finansiell information och officiella formulär för identifiering. Tillgängligt från och med modell version `2019-10-01` . Under typer visas nedan. 

### <a name="financial-account-identification"></a>Identifiering av finansiella konton

| Under typs namn               | Beskrivning                                                                |
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

Följande entiteter är grupperade och listade efter land:

Argentina
* DNI-nummer (Argentina National Identity)

Österrike
* ID-kort för Österrike
* Skatte identifierings nummer för Österrike
* Österrike Value Added Tax (moms) nummer

Australien
* Bank konto nummer för Australien
* Australiensiskt företags nummer
* Australiensiskt företags nummer
* Enhets licens nummer för Australien
* Australien, medicin konto nummer
* Australien Passport-nummer
* Australien, skatte fil nummer

Belgien
* Belgien National Number
* Tilläggs skatte nummer för Belgien

Brasilien 
* Brasilien juridisk enhets nummer (CNPJ)
* Brasilien CPF-nummer
* Brasilien National ID-kort (RG)

Bulgarien
* Bulgarien, enhetligt civil nummer

Kanada
* Bank konto nummer för Kanada
* Licens nummer för Kanadas driv rutin
* Kanada Hälsotjänst nummer
* Canada Passport-nummer
* Canada personal Health Identification Number (PHIN)
* Kanadas sociala försäkrings nummer

Chile
* Identitets korts nummer 

Kina
* PRC-nummer (Folkrepubliken Kina)

Kroatien
* Nummer för Kroatien identitets kort
* Nationellt ID-kort nummer för Kroatien
* OIB-nummer (Kroatien personal Identification)

Cypern
* Kort nummer för Cypern identitet
* Identifierings nummer för Cypern skatt

Tjeckien
* Tjeckiska personal Identity Number

Danmark
* Danmark personal identifikations nummer

Estland
* Estland personal identifikations kod

Europeiska unionen (EU)
* EU betalkort nummer
* Licens nummer för EU-drivrutin
* EU: s nationella identifierings nummer
* EU Passport-nummer
* EU: s socialförsäkrings nummer (SSN) eller motsvarande ID
* EU: s skatte identifierings nummer (TIN)

Finland
* Europeiskt hälso försäkrings nummer för Finland
* Nationella ID för Finland
* Nummer för Finland Passport

Frankrike
* Fransk driv Rutins licens nummer
* Franskt hälso försäkrings nummer
* Franskt nationellt ID-kort (CNI)
* Franskt Passport-nummer
* Frankrike, socialförsäkrings nummer (INSEE)
* Frankrikes skatte identifierings nummer (Numéro SPI)
* Frankrike mervärdes skatte nummer

Tyskland
* Tysk kör korts nummer
* Kort nummer för Tyskland-identitet
* Tyskt Passport-nummer
* Skatte identifikations nummer för Tyskland
* Mervärdes skatte nummer för Tyskland

Grekland 
* Kort nummer för nationella Grekland-ID
* Skatte identifikations nummer för Grekland

Hongkong
* HKID-nummer (Hong Kong Identity Card)

Ungern
* Ungern National Identification Number
* Ungern skatte identifierings nummer
* Ungern mervärdes skatte nummer

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
* Italiensk räkenskaps kod
* Italienskt mervärdes skatte nummer

Japan
* Japanskt bank konto nummer
* Japansk driv Rutins licens nummer
* Japanska mina siffror, personliga
* Japanskt mitt nummer företag
* Japan, inhemskt registrerings nummer
* Japanskt boende korts nummer
* Japan, sociala försäkrings nummer (SIN)
* Japanskt Passport-nummer

Lettland
* Personlig kod för Lettland

Litauen
* Information om Litauen-personliga kod

Luxemburg
* Luxemburg National Identification Number (fysiska personer)
* Luxemburg National Identification Number (icke-fysiska personer)

Malaysia
* Kort nummer för Malaysia-ID

Malta
* Nummer för Malta-identitets kort
* Nummer på Malta skatte identifikation

Nederländerna
* Nederländskt BSN-nummer (Nederländernas unionsmedborgare)
* Nederländernas skatte identifierings nummer
* Nederländernas mervärdes skatte nummer

Nya Zeeland
* Nya Zeeland bank konto nummer
* Ny Zeelands driv Rutins licens nummer
* Nya Zeeland, inre intäkts nummer
* New Zeelands ministeriet för hälso tillstånd
* Nya Zeeland, sociala välfärd-nummer

Norge
* ID-nummer för Norge

Filippinerna
* Filippinerna-enhetligt ID-nummer för flera syften

Polen
* Kort för Polen-identitet
* Polen National ID (PESEL)
* Polen Passport-nummer
* Polen REGON-nummer
* Skatte identifierings nummer för Polen

Portugal 
* Kort nummer för Portugal, unionsmedborgare
* Skatte identifikations nummer för Portugal

Rumänien
* Rumänien, personlig numerisk kod (CNP)

Ryssland
* Ryskt Passport-nummer (inhemskt)
* Ryskt Passport-nummer (internationellt)

Saudiarabien
* Nationellt ID för Saudiarabien

Singapore
* NRIC-nummer (National registration ID Card) i Singapore

Slovakien 
* Privat Slovakien-nummer

Slovenien
* Skatte identifierings nummer för Slovenien
* Slovenien Unique Master medborgarnas nummer

Sydafrika
* FN-nummer för Sydafrika

Sydkorea
* Sydkorea, inhemskt registrerings nummer

Spanien 
* Spansk DNI
* Spaniens person nummer (SSN)
* Spaniens skatte identifierings nummer

Sverige
* National Sverige-ID
* Sverige Passport-nummer
* Svensk skatte identifierings nummer

Schweiz
* Schweiziska socialförsäkrings nummer AHV

Taiwan 
* Nationella ID för Taiwan
* Inhemskt Taiwan-certifikat (ARC/TARC)
* Taiwanesiskt Passport-nummer

Thailand
* Identifierings kod för Thai-population

Turkiet
* Turkisk National Identification Number

Ukraina
* Ukraina Passport-nummer (inhemsk)
* Ukraina Passport-nummer (internationell)

Storbritannien
* engelska Kör korts nummer
* engelska Nummer för val av rulle
* engelska NHS-nummer (National Hälsotjänst)
* engelska Nationellt försäkrings nummer (NINO)
* engelska Passport-nummer
* engelska Unikt Taxpayer Reference Number

USA
* Amerikanskt socialförsäkrings nummer (SSN)
* Amerikansk kör korts nummer
* Amerikanskt Passport-nummer
* Amerikanskt individuellt Taxpayer Identification Number (ITIN)
* Nummer för USA: s narkotika verk ställande byrå (DIETANOLAMIN)
* Konto nummer för amerikansk bank
