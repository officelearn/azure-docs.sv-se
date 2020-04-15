---
title: Azure MultiFaktorautentiseringsdatahemvist
description: Läs om vilka personliga och organisatoriska data som Azure Multi-Factor Authentication lagrar om dig och dina användare och vilka data som finns kvar i ursprungslandet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309804"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Databes residens och kunddata för Azure Multi-Factor-autentisering

Kunddata lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst som Office 365 och Azure. Om du vill ha information om var dina kunddata lagras kan du använda avsnittet [Var finns dina data i](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft Trust Center.

Molnbaserad Azure Multi-Factor Authentication och Azure Multi-Factor Authentication Server process och lagra en viss mängd personuppgifter och organisationsdata. I den här artikeln beskrivs vad och var data lagras.

Följande multifaktorautentiseringsaktiviteter kommer för närvarande från amerikanska datacenter utom när det anges:

* Tvåfaktorsautentisering med telefonsamtal eller SMS kommer vanligtvis från amerikanska datacenter och dirigeras av globala leverantörer.
    * Användares autentiseringsbegäranden för allmänt bruk från andra regioner som Europa eller Australien bearbetas för närvarande av datacenter i den regionen. Andra händelser som återställning av lösenord för självbetjäning, Azure B2C-händelser eller hybridscenarier med NPS-tillägg eller AD FS-kort bearbetas för närvarande av amerikanska datacenter.
* Push-meddelanden med Microsoft Authenticator-appen kommer från amerikanska datacenter. Dessutom kan enhetsleverantörsspecifika tjänster också spela in från olika regioner.
* OATH-koder valideras vanligtvis för närvarande i USA.
    * Återigen bearbetas användarautentiseringshändelser för allmänt bruk som har sitt ursprung i andra regioner, till exempel Europa eller Australien, av datacenter i den regionen. Ytterligare händelser bearbetas för närvarande av amerikanska datacenter.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Personuppgifter som lagras av Azure Multi-Factor Authentication

Personuppgifter är information på användarnivå som är kopplad till en viss person. Följande datalager innehåller personuppgifter:

* Blockerade användare
* Förbi användare
* Ändringsbegäranden för Microsoft Authenticator-enhetstoken
* Aktivitetsrapporter för multifaktorautentisering
* Microsoft Authenticator-aktiveringar

Denna information sparas i 90 dagar.

Azure Multi-Factor Authentication loggar inte personliga data som användarnamn, telefonnummer eller IP-adress, men det finns en *UserObjectId* som identifierar multifaktorautentiseringsförsök för användare. Loggdata lagras i 30 dagar.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

För offentliga Azure-moln, exklusive Azure B2C-autentisering, NPS-tillägg och Windows Server 2016 eller 2019 AD FS-kort, lagras följande personuppgifter:

| Händelsetyp                           | Typ av datalager |
|--------------------------------------|-----------------|
| OATH-token                           | I multifaktorautentiseringsloggar     |
| Enkelriktad SMS                          | I multifaktorautentiseringsloggar     |
| Röstsamtal                           | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras |
| Meddelande om Microsoft Authenticator | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras<br />Ändra begäranden när Microsoft Authenticator-enhetstoken ändras |

> [!NOTE]
> Datalagret för aktivitetsautentisering för multifaktorautentisering lagras i USA för alla moln, oavsett region som bearbetar autentiseringsbegäran. Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet och Microsoft Government Cloud har sina egna oberoende datalager åtskilda från datalager i offentliga molnområde, men dessa data lagras alltid i USA.

För Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure Operated by 21Vianet, Azure B2C-autentisering, NPS Extension och Windows Server 2016 eller 2019 AD FS-adapter lagras följande personuppgifter:

| Händelsetyp                           | Typ av datalager |
|--------------------------------------|-----------------|
| OATH-token                           | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering |
| Enkelriktad SMS                          | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering |
| Röstsamtal                           | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras |
| Meddelande om Microsoft Authenticator | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras<br />Ändra begäranden när Microsoft Authenticator-enhetstoken ändras |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication Server

Om du distribuerar och kör Azure Multi-Factor Authentication Server lagras följande personuppgifter:

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda Multifaktor authentication Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MultiFaktor Authentication Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

| Händelsetyp                           | Typ av datalager |
|--------------------------------------|-----------------|
| OATH-token                           | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering |
| Enkelriktad SMS                          | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering |
| Röstsamtal                           | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras |
| Meddelande om Microsoft Authenticator | I multifaktorautentiseringsloggar<br />Datalagerrapportlagret för aktivitetsrapport för multifaktorautentisering<br />Blockerade användare om bedrägerier rapporteras<br />Ändra begäranden när Microsoft Authenticator-enhetstoken ändras |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Organisationsdata som lagras av Azure Multi-Factor Authentication

Organisationsdata är information på klientnivå som kan exponera konfigurations- eller miljöinställningar. Klientinställningar från följande Azure portal Multifaktorautentiseringssidor kan lagra organisationsdata, till exempel tröskelvärden för utelåsning eller nummerpresenttröskelinformation för inkommande telefonautentiseringsbegäranden:

* Kontoutelåsning
* Bedrägerivarning
* Meddelanden
* Inställningar för telefonsamtal

Och för Azure Multi Factor Authentication Server kan följande Azure-portalsidor innehålla organisationsdata:

* Serverinställningar

* En gång bypass
* Cachelagringsregler
* Status för autentiseringsserver för flera faktorer

## <a name="log-data-location"></a>Logga dataplats

Var logginformation lagras beror på vilken region de bearbetas i. De flesta geografiska områden har inbyggda Azure Multi-Factor Authentication-funktioner, så loggdata lagras i samma region som bearbetar multifaktorautentiseringsbegäran. I geografiska områden utan inbyggt Azure Multi-Factor Authentication-stöd, de är betjänas av antingen USA eller Europa geografiska områden och loggdata lagras i samma region som bearbetar multifaktorautentisering begäran.

Vissa grundläggande autentiseringsloggdata lagras endast i USA. Microsoft Azure Germany och Microsoft Azure Operated by 21Vianet lagras alltid i sina respektive moln. Microsoft Government Cloud-loggdata lagras alltid i USA.

## <a name="next-steps"></a>Nästa steg

Mer information om vilken användarinformation som samlas in av molnbaserad Azure Multi-Factor Authentication och Azure Multi-Factor Authentication Server finns i [azure multifaktorautentisering användardatainsamling](howto-mfa-reporting-datacollection.md).
