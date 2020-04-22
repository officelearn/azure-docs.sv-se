---
title: Om Azure Key Vault nycklar, hemligheter och certifikat - Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och utvecklarinformation för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726447"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat

Med Azure Key Vault kan Microsoft Azure-program och användare lagra och använda flera typer av hemliga/viktiga data:

- Kryptografiska nycklar: Stöder flera nyckeltyper och algoritmer och möjliggör användning av HSM (Hardware Security Modules) för nycklar med högt värde. Mer information finns i [Om nycklar](../keys/about-keys.md).
- Hemligheter: Ger säker lagring av hemligheter, till exempel lösenord och databasanslutningssträngar. Mer information finns i [Om hemligheter](../secrets/about-secrets.md).
- Certifikat: Stöder certifikat som bygger ovanpå nycklar och hemligheter och lägger till en automatisk förnyelsefunktion. Mer information finns i [Om certifikat](../certificates/about-certificates.md).
- Azure Storage: Kan hantera nycklar för ett Azure Storage-konto åt dig. Internt kan Key Vault lista (synkronisera) nycklar med ett Azure Storage-konto och återskapa (rotera) nycklarna regelbundet. Mer information finns i [Hantera lagringskontonycklar med Key Vault](../secrets/overview-storage-keys.md).

Mer allmän information om Key Vault finns i [Om Azure Key Vault](overview.md).

## <a name="data-types"></a>Datatyper

Se JOSE-specifikationerna för relevanta datatyper för nycklar, kryptering och signering.  

-   **algoritm** - en algoritm som stöds för en nyckelåtgärd, till exempel RSA1_5  
-   **ciphertext-värde** - chiffertextoktetter, kodade med Base64URL  
-   **digest-värde** - utdata från en hash-algoritm, kodad med Base64URL  
-   **nyckeltyp** - en av de nyckeltyper som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartextvärde** - oktett i klartext, kodade med Base64URL  
-   **signaturvärde** - utdata från en signaturalgoritm, kodad med Base64URL  
-   **base64URL** - ett Base64URL [RFC4648] kodat binärt värde  
-   **boolesk** - antingen sant eller falskt  
-   **Identity** - en identitet från Azure Active Directory (AAD).  
-   **IntDate** - ett JSON-decimalvärde som representerar antalet sekunder från 1970-01-01T0:0:0Z UTC fram till angivet UTC-datum/-tid. Se RFC3339 för mer information om datum/tider, i allmänhet och UTC i synnerhet.  

## <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versionshantering

Objekt som lagras i Key Vault versions när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt först skapas får det en unik versionsidentifierare och markeras som den aktuella versionen av objektet. Skapandet av en ny instans med samma objektnamn ger det nya objektet en unik versionsidentifierare, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas med den aktuella identifieraren eller en versionsspecifik identifierare. Om du till exempel anger `MasterKey`en nyckel med namnet, gör det att utföra åtgärder med den aktuella identifieraren att systemet använder den senaste tillgängliga versionen. Om du utför åtgärder med den versionsspecifika identifieraren används systemet den specifika versionen av objektet.  

Objekt identifieras unikt i Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett geografisk plats. Den fullständiga URL:en till ett objekt kallas objektidentifieraren. URL:en består av ett prefix som identifierar Nyckelvalvet, objekttypen, objektets namn och en objektversion. Objektnamnet är skiftlägesokänsligt och oföränderligt. Identifierare som inte innehåller objektversionen kallas basidentifierare.  

Mer information finns i [Autentisering, begäranden och svar](authentication-requests-and-responses.md)

En objektidentifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet på ett nyckelvalv i Tjänsten Microsoft Azure Key Vault.<br /><br /> Key Vault-namn väljs av användaren och är globalt unika.<br /><br /> Key Vault-namnet måste vara en 3-24 teckensträng som endast innehåller 0-9, a-z, A-Z och -.|  
|`object-type`|Typen av objekt, "nycklar", "hemligheter" eller "certifikat".|  
|`object-name`|En `object-name` är ett användarnamn för och måste vara unikt i ett Nyckelvalv. Namnet måste vara en 1-127 teckensträng som innehåller endast 0-9, a-z, A-Z och -.|  
|`object-version`|En `object-version` är en systemgenererad 32 teckensträngidentifierare som eventuellt används för att adressera en unik version av ett objekt.|  

## <a name="next-steps"></a>Nästa steg

- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begäranden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)