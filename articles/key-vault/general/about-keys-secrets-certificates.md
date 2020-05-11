---
title: Om Azure Key Vault nycklar, hemligheter och certifikat – Azure Key Vault
description: Översikt över Azure Key Vault REST-gränssnittet och information om utvecklare för nycklar, hemligheter och certifikat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005878"
---
# <a name="about-keys-secrets-and-certificates"></a>Om nycklar, hemligheter och certifikat

Azure Key Vault gör det möjligt för Microsoft Azure program och användare att lagra och använda flera typer av hemliga/viktiga data:

- Kryptografiska nycklar: stöder flera nyckel typer och algoritmer och möjliggör användning av HSM (Hardware Security modules) för nycklar med hög värde. Mer information finns i [om nycklar](../keys/about-keys.md).
- Hemligheter: ger säker lagring av hemligheter, t. ex. lösen ord och databas anslutnings strängar. Mer information finns i [om hemligheter](../secrets/about-secrets.md).
- Certifikat: stöder certifikat, som bygger på nycklar och hemligheter och lägger till en automatiserad förnyelse funktion. Mer information finns i [om certifikat](../certificates/about-certificates.md).
- Azure Storage: kan hantera nycklar för ett Azure Storage konto åt dig. Internt Key Vault kan ange (Sync) nycklar med ett Azure Storage konto och återskapa (rotera) nycklarna med jämna mellanrum. Mer information finns i [Hantera lagrings konto nycklar med Key Vault](../secrets/overview-storage-keys.md).

Mer allmän information om Key Vault finns i [om Azure Key Vault](overview.md).

## <a name="data-types"></a>Datatyper

Se JOSE-specifikationerna för relevanta data typer för nycklar, kryptering och signering.  

-   **algoritm** – en algoritm som stöds för en nyckel åtgärd, till exempel RSA1_5  
-   **chiffertexten-värde** -cipher text-oktetter, kodade med Base64URL  
-   **sammandrag-värde** – utdata från en hash-algoritm kodas med hjälp av Base64URL  
-   **nyckel typ** – en av de nyckel typer som stöds, till exempel RSA (Rivest-Shamir-Adleman).  
-   **klartext – värde** – oformaterade oktetter, kodade med Base64URL  
-   **Signature-värde** – utdata från en Signeringsalgoritm som är kodad med Base64URL  
-   **base64URL** – a BASE64URL [RFC4648] kodat binärt värde  
-   **boolesk** -antingen sant eller falskt  
-   **Identitet** – en identitet från Azure Active Directory (AAD).  
-   **IntDate** – ett JSON-standardvärde som representerar antalet sekunder från 1970-01-01T0:0: 0Z UTC tills angivet UTC-datum/tid. Se RFC3339 för information om datum/tider, i allmänhet och UTC i synnerhet.  

## <a name="objects-identifiers-and-versioning"></a>Objekt, identifierare och versions hantering

Objekt som lagras i Key Vault versioner skapas när en ny instans av ett objekt skapas. Varje version tilldelas en unik identifierare och URL. När ett objekt skapas första gången får den ett unikt versions-ID och markerat som den aktuella versionen av objektet. Om du skapar en ny instans med samma objekt namn får det nya objektet ett unikt versions-ID, vilket gör att den blir den aktuella versionen.  

Objekt i Key Vault kan åtgärdas genom att specifing en version eller genom att utesluta version för åtgärder på den aktuella versionen av objektet. Till exempel kan en nyckel med namnet `MasterKey`, som utför åtgärder utan specifing en version, orsaka att systemet använder den senaste tillgängliga versionen. Att utföra åtgärder med den versions bara identifierade identifieraren gör att systemet använder den aktuella versionen av objektet.  

Objekt identifieras unikt inom Key Vault med hjälp av en URL. Inga två objekt i systemet har samma URL, oavsett Geo-plats. Den fullständiga URL: en till ett objekt kallas för objekt-ID. URL: en består av ett prefix som identifierar Key Vault, objekt typ, användardefinierat objekt namn och en objekt version. Objekt namnet är Skift läges okänsligt och oföränderligt. Identifierare som inte innehåller objekt versionen kallas för bas identifierare.  

Mer information finns i [autentisering, begär Anden och svar](authentication-requests-and-responses.md)

En objekt identifierare har följande allmänna format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Där:  

|||  
|-|-|  
|`keyvault-name`|Namnet på ett nyckel valv i Microsoft Azure Key Vault-tjänsten.<br /><br /> Key Vault namn väljs av användaren och är globalt unika.<br /><br /> Key Vault namn måste vara en 3-24-tecken sträng som bara innehåller 0-9, a-z, A-Z och-.|  
|`object-type`|Objektets typ, "nycklar", "hemligheter" eller "certifikat".|  
|`object-name`|Ett `object-name` är ett användardefinierat namn för och måste vara unikt inom en Key Vault. Namnet måste vara en 1-127-tecken sträng, med början på en bokstav och endast innehålla 0-9, a-z, A-Z och-.|  
|`object-version`|En `object-version` är en systemgenererad 32 tecken Strängs identifierare som kan användas för att adressera en unik version av ett objekt.|  

## <a name="next-steps"></a>Nästa steg

- [Om nycklar](../keys/about-keys.md)
- [Om hemligheter](../secrets/about-secrets.md)
- [Om certifikat](../certificates/about-certificates.md)
- [Autentisering, begär Anden och svar](../general/authentication-requests-and-responses.md)
- [Utvecklarguide för Key Vault](../general/developers-guide.md)
