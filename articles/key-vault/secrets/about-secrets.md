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
ms.openlocfilehash: eabfa03aa70f54a967fe256f694ef59ad0fe7ebe
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685443"
---
# <a name="about-azure-key-vault-secrets"></a>Om Hemligheter för Azure Key Vault

Key Vault ger säker lagring av hemligheter, till exempel lösenord och databasanslutningssträngar.

Ur en utvecklares perspektiv accepterar och returnerar Key Vault-API:er hemliga värden som strängar. Internt lagrar och hanterar Key Vault hemligheter som sekvenser av oktetter (8-bitars byte), med en maximal storlek på 25 k byte vardera. Key Vault-tjänsten ger inte semantik för hemligheter. Den accepterar bara data, krypterar den, lagrar den och returnerar en hemlig identifierare ("id"). Identifieraren kan användas för att hämta hemligheten vid ett senare tillfälle.  

För data som är mycket känsliga bör klienter överväga ytterligare skyddslager för data. Ett exempel är kryptering av data med hjälp av en separat skyddsnyckel före lagring i Key Vault.  

Key Vault stöder också ett contentType-fält för hemligheter. Klienter kan ange innehållstypen för en hemlighet som hjälper till att tolka hemliga data när de hämtas. Den maximala längden på det här fältet är 255 tecken. Det finns inga fördefinierade värden. Den föreslagna användningen är som en ledtråd för att tolka hemliga data. En implementering kan till exempel lagra både lösenord och certifikat som hemligheter och sedan använda det här fältet för att skilja. Det finns inga fördefinierade värden.  

## <a name="secret-attributes"></a>Hemliga attribut

Utöver de hemliga uppgifterna kan följande attribut anges:  

- *exp:* IntDate, valfritt, standard är **för evigt**. Attributet *exp* (expiration time) identifierar utgångstiden på eller efter vilken de hemliga data inte ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här fältet är endast i **informationssyfte** eftersom det informerar användare av nyckelvalvstjänsten om att en viss hemlighet inte får användas. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde.   
- *nbf*: IntDate, valfritt, standard är **nu**. Attributet *nbf* (inte före) identifierar den tid före vilken de hemliga uppgifterna INTE ska hämtas, utom i [vissa situationer](#date-time-controlled-operations). Det här **informational** fältet är endast i informationssyfte. Dess värde MÅSTE vara ett tal som innehåller ett intdate-värde. 
- *aktiverad:* booleska, valfria, standard är **sant**. Det här attributet anger om de hemliga data som kan hämtas. Det aktiverade attributet används tillsammans med *nbf* och *exp* när en åtgärd inträffar mellan *nbf* och *exp,* det kommer bara att tillåtas om aktiverad är inställd på **true**. Operationer utanför *nbf-* och *exp-fönstret* tillåts automatiskt, utom i [vissa situationer](#date-time-controlled-operations).  

Det finns ytterligare skrivskyddade attribut som ingår i alla svar som innehåller hemliga attribut:  

- *skapad*: IntDate, valfritt. Attributet skapad anger när den här versionen av hemligheten skapades. Det här värdet är null för hemligheter som skapats före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.  
- *uppdaterad*: IntDate, valfritt. Det uppdaterade attributet anger när den här versionen av hemligheten uppdaterades. Det här värdet är null för hemligheter som senast uppdaterades före tillägget av det här attributet. Dess värde måste vara ett tal som innehåller ett intdate-värde.

### <a name="date-time-controlled-operations"></a>Datum-tidsstyrda operationer

En hemlighet s **få** operation kommer att fungera för ännu inte giltiga och utgångna hemligheter, utanför *nbf* / *exp* fönstret. Ringa en hemlighet's **get** operation, för en ännu inte giltig hemlighet, kan användas för teständamål. Hämtar **(få**ting) en utgången hemlighet, kan användas för återställningsåtgärder.

## <a name="secret-access-control"></a>Åtkomstkontroll till hemlighet

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

## <a name="secret-tags"></a>Hemliga taggar  
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

## <a name="storage-account-access-control"></a>Åtkomstkontroll för lagringskonto

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

## <a name="next-steps"></a>Nästa steg

- [Om Key Vault](../general/overview.md)
- [Om nycklar, hemligheter och certifikat](../general/about-keys-secrets-certificates.md)
- [Om nycklar](../keys/about-keys.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
