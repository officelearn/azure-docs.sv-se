---
title: Om Hemligheter för Azure Key Vault – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och utvecklarinformation för hemligheter.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424267"
---
# <a name="about-azure-key-vault-secrets"></a>Om Hemligheter för Azure Key Vault

Med Azure Key Vault kan Microsoft Azure-program och användare lagra och använda flera typer av hemliga data:

- Hemligheter: Ger säker lagring av hemligheter, till exempel lösenord och databasanslutningssträngar.

- Azure Storage: Kan hantera nycklar för ett Azure Storage-konto åt dig. Internt kan Key Vault lista (synkronisera) nycklar med ett Azure Storage-konto och återskapa (rotera) nycklarna regelbundet. 

Mer allmän information om Key Vault finns i [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

Följande avsnitt innehåller allmän information som gäller för implementeringen av Key Vault-tjänsten. 

### <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versionshantering

Objekt som lagras i Key Vault versions när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt först skapas får det en unik versionsidentifierare och markeras som den aktuella versionen av objektet. Skapandet av en ny instans med samma objektnamn ger det nya objektet en unik versionsidentifierare, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas med den aktuella identifieraren eller en versionsspecifik identifierare. Om du till exempel anger `MasterKey`en nyckel med namnet, gör det att utföra åtgärder med den aktuella identifieraren att systemet använder den senaste tillgängliga versionen. Om du utför åtgärder med den versionsspecifika identifieraren används systemet den specifika versionen av objektet.  

Objekt identifieras unikt i Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett geografisk plats. Den fullständiga URL:en till ett objekt kallas objektidentifieraren. URL:en består av ett prefix som identifierar Nyckelvalvet, objekttypen, objektets namn och en objektversion. Objektnamnet är skiftlägesokänsligt och oföränderligt. Identifierare som inte innehåller objektversionen kallas basidentifierare.  

Mer information finns i [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet på ett nyckelvalv i Tjänsten Microsoft Azure Key Vault.<br /><br /> Key Vault-namn väljs av användaren och är globalt unika.<br /><br /> Key Vault-namnet måste vara en 3-24 teckensträng som endast innehåller 0-9, a-z, A-Z och -.|  
|`object-type`|Typen av objekt, antingen "nycklar" eller "hemligheter".|  
|`object-name`|En `object-name` är ett användarnamn för och måste vara unikt i ett Nyckelvalv. Namnet måste vara en 1-127 teckensträng som innehåller endast 0-9, a-z, A-Z och -.|  
|`object-version`|En `object-version` är en systemgenererad 32 teckensträngidentifierare som eventuellt används för att adressera en unik version av ett objekt.|  

## <a name="key-vault-secrets"></a>Key Vault hemligheter 

### <a name="working-with-secrets"></a>Arbeta med hemligheter

Ur en utvecklares perspektiv accepterar och returnerar Key Vault-API:er hemliga värden som strängar. Internt lagrar och hanterar Key Vault hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25 k byte vardera. Key Vault-tjänsten ger inte semantik för hemligheter. Den accepterar bara data, krypterar den, lagrar den och returnerar en hemlig identifierare ("id"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För data som är mycket känsliga bör klienter överväga ytterligare skyddslager för data. Ett exempel är kryptering av data med hjälp av en separat skyddsnyckel före lagring i Key Vault.  

Key Vault stöder också ett contentType-fält för hemligheter. Klienter kan ange innehållstypen för en hemlighet som hjälper till att tolka hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som en ledtråd för att tolka hemliga data. En implementering kan till exempel lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att skilja. Det finns inga fördefinierade värden.  

### <a name="secret-attributes"></a>Hemliga attribut

Utöver de hemliga uppgifterna kan följande attribut anges:  

- *exp:* IntDate, valfritt, standard är **för evigt**. Attributet *exp* (expiration time) identifierar utgångstiden på eller efter vilken de hemliga data inte ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här fältet är endast i **informationssyfte** eftersom det informerar användare av nyckelvalvstjänsten om att en viss hemlighet inte får användas. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.   
- *nbf*: IntDate, valfritt, standard är **nu**. Attributet *nbf* (inte före) identifierar den tid före vilken de hemliga uppgifterna INTE ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här **informational** fältet är endast i informationssyfte. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde. 
- *aktiverad:* booleska, valfria, standard är **sant**. Det här attributet anger om de hemliga data som kan hämtas. Det aktiverade attributet används tillsammans med *nbf* och *exp* när en åtgärd inträffar mellan *nbf* och *exp,* det kommer bara att tillåtas om aktiverad är inställd på **true**. Operationer utanför *nbf-* och *exp-fönstret* tillåts automatiskt, utom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapad*: IntDate, valfritt. Attributet skapad anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.  
- *uppdaterad*: IntDate, valfritt. Det uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.

#### <a name="date-time-controlled-operations"></a>Datum-tidsstyrda operationer

En hemlighet s **få** operation kommer att fungera för ännu inte giltiga och utgångna hemligheter, utanför *nbf* / *exp* fönstret. Ringa en hemlighet's **get** operation, för en ännu inte giltig hemlighet, kan användas för teständamål. Hämtar **(få**ting) en utgången hemlighet, kan användas för återställningsåtgärder.

### <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

Åtkomstkontroll för hemligheter som hanteras i Key Vault finns på nivån för Key Vault som innehåller dessa hemligheter. Åtkomstkontrollprincipen för hemligheter skiljer sig från åtkomstkontrollprincipen för nycklar i samma Nyckelvalv. Användare kan skapa ett eller flera valv för att hålla hemligheter och måste upprätthålla scenariot lämplig segmentering och hantering av hemligheter.   

Följande behörigheter kan användas, per huvudmannabas, i åtkomstkontrollposten hemligheter i ett valv och noggrant spegla de åtgärder som tillåts för ett hemligt objekt:  

- Behörigheter för hemliga hanteringsåtgärder
  - *få:* Läs en hemlighet  
  - *lista*: Lista hemligheter eller versioner av en hemlighet som lagras i ett Nyckelvalv  
  - *set*: Skapa en hemlighet  
  - *ta bort*: Ta bort en hemlighet  
  - *återställa*: Återställa en borttagen hemlighet
  - *backup*: Säkerhetskopiera en hemlighet i ett nyckelvalv
  - *återställa:* Återställa en säkerhetskopierad hemlighet till ett nyckelvalv

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) en borttagen hemlighet

Mer information om hur du arbetar med hemligheter finns [i Hemliga åtgärder i REST-API-referensen för Key Vault REST](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Hemliga taggar  
Du kan ange ytterligare programspecifika metadata i form av taggar. Key Vault stöder upp till 15 taggar, som alla kan ha ett 256 teckennamn och ett 256 teckenvärde.  

>[!Note]
>Taggar kan läsas av en uppringare om de har *listan* eller *får* behörighet.

## <a name="azure-storage-account-key-management"></a>Hantering av nyckelhantering för Azure Storage-konton

Key Vault kan hantera Azure-lagringskontonycklar:

- Internt kan Key Vault lista (synkronisera) nycklar med ett Azure-lagringskonto. 
- Key Vault återskapar (roterar) tangenterna med jämna mellanrum.
- Nyckelvärden returneras aldrig som svar på den som ringer.
- Key Vault hanterar nycklar för både lagringskonton och klassiska lagringskonton.

Mer information finns i [Azure Key Vault Storage Account Keys](../secrets/overview-storage-keys.md))

### <a name="storage-account-access-control"></a>Åtkomstkontroll för lagringskonto

Följande behörigheter kan användas när en användare eller ett programhuvudnamn auktoriseras för att utföra åtgärder på ett hanterat lagringskonto:  

- Behörigheter för hanterade lagringskonto- och SaS-definitionsåtgärder
  - *hämta*: Hämtar information om ett lagringskonto 
  - *lista*: Lista lagringskonton som hanteras av ett Nyckelvalv
  - *uppdatering*: Uppdatera ett lagringskonto
  - *ta bort*: Ta bort ett lagringskonto  
  - *återställa*: Återställa ett borttaget lagringskonto
  - *säkerhetskopiering*: Säkerhetskopiera ett lagringskonto
  - *återställa:* Återställa ett säkerhetskopierat lagringskonto till ett Key Vault
  - *set*: Skapa eller uppdatera ett lagringskonto
  - *regeneratekey*: Återskapa ett angivet nyckelvärde för ett lagringskonto
  - *getsas*: Få information om en SAS-definition för ett lagringskonto
  - *listsas*: Lista SAS-definitioner för lagring för ett lagringskonto
  - *deletesas*: Ta bort en SAS-definition från ett lagringskonto
  - *setas*: Skapa eller uppdatera en ny SAS-definition/attribut för ett lagringskonto

- Behörigheter för privilegierade åtgärder
  - *rensa:* Rensa (permanent ta bort) ett hanterat lagringskonto

Mer information finns [i lagringskontoåtgärderna i REST-API-referensen för Key Vault REST](/rest/api/keyvault). Information om hur du upprättar behörigheter finns i [Arkiv - Skapa eller Uppdatera](/rest/api/keyvault/vaults/createorupdate) och [Valv - Uppdatera åtkomstprincip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Se även

- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
