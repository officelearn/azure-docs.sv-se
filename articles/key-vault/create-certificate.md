---
title: Metoder för att skapa certifikat
description: Olika sätt att skapa ett certifikat i Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: c27cde85952ca6d982accddad59eceae76e3f1e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194466"
---
# <a name="certificate-creation-methods"></a>Metoder för att skapa certifikat

 Ett KV-certifikat (Key Vault) kan antingen skapas eller importeras till ett nyckelvalv. När ett KV-certifikat skapas skapas den privata nyckeln i nyckelvalvet och exponeras aldrig för certifikatägaren. Följande är sätt att skapa ett certifikat i Key Vault:  

-   **Skapa ett självsignerat certifikat:** Detta skapar ett offentligt-privat nyckelpar och associerar det med ett certifikat. Certifikatet signeras med en egen nyckel.  

-    **Skapa ett nytt certifikat manuellt:** Detta skapar ett offentligt-privat nyckelpar och genererar en X.509-begäran om signering av certifikat. Signeringsbegäran kan undertecknas av din registreringsmyndighet eller certifikatutfärdare. Det signerade x509-certifikatet kan slås samman med det väntande nyckelparet för att slutföra KV-certifikatet i Key Vault. Även om den här metoden kräver fler steg ger den dig större säkerhet eftersom den privata nyckeln skapas i och begränsas till Key Vault. Detta förklaras i diagrammet nedan.  

![Skapa ett certifikat med din egen certifikatutfärdaren](media/certificate-authority-1.png)  

Följande beskrivningar motsvarar de gröna bokstavsstegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat, vilket börjar internt med skapandet av en nyckel i ditt nyckelvalv.
2. Key Vault returnerar en begäran om certifikatsignering till ditt program
3. Ditt program skickar CSR till den certifikatutfärdare som du har valt.
4. Din valda certifikatutfärdare svarar med ett X509-certifikat.
5. Programmet slutför skapandet av det nya certifikatet med en sammanslagning av X509-certifikatet från certifikatutfärdaren.

-   **Skapa ett certifikat med en känd utfärdarleverantör:** Den här metoden kräver att du gör en engångsuppgift för att skapa ett utfärdarobjekt. När ett utfärdarobjekt har skapats i ditt nyckelvalv kan dess namn refereras i principen för KV-certifikatet. En begäran om att skapa ett sådant KV-certifikat skapar ett nyckelpar i valvet och kommunicerar med utfärdarleverantörens tjänst med hjälp av informationen i det refererade utfärdarobjektet för att få ett x509-certifikat. X509-certifikatet hämtas från utfärdartjänsten och slås samman med nyckelparet för att slutföra skapandet av KV-certifikat.  

![Skapa ett certifikat med en certifikatutfärdaren för Key Vault-partner](media/certificate-authority-2.png)  

Följande beskrivningar motsvarar de gröna bokstavsstegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat, vilket börjar internt med skapandet av en nyckel i ditt nyckelvalv.
2. Key Vault skickar en TLS/SSL-certifikatbegäran till certifikatutfärdaren.
3. Programmet avsöker, i en loopa-och-vänta-process, ditt nyckelvalv för slutförande av certifikatet. Skapandet av certifikat är klar när Key Vault tar emot certifikatutfärdarens svar med X.509-certifikat.
4. Certifikatutfärdaren svarar på Key Vaults TLS/SSL-certifikatbegäran med ett TLS/SSL X.509-certifikat.
5. Det nya certifikatet slutförs med sammanslagningen av TLS/SSL X.509-certifikatet för certifikatutfärdaren.

## <a name="asynchronous-process"></a>Asynkron process
KV-certifikatskapande är en asynkron process. Den här åtgärden skapar en KV-certifikatbegäran och returnerar en http-statuskod för 202 (Accepterad). Status för begäran kan spåras genom att avsöka det väntande objektet som skapats av den här åtgärden. Den fullständiga URI för det väntande objektet returneras i PLATS-huvudet.  

När en begäran om att skapa ett KV-certifikat slutförs ändras statusen för det väntande objektet till "slutförd" från "inprogress", och en ny version av KV-certifikatet skapas. Detta kommer att bli den aktuella versionen.  

## <a name="first-creation"></a>Första skapelsen
 När ett KV-certifikat skapas för första gången skapas också en adresserbar nyckel och hemlighet med samma namn som certifikatet. Om namnet redan används misslyckas åtgärden med en http-statuskod på 409 (konflikt).
Den adresserbara nyckeln och hemligheten hämtar sina attribut från KV-certifikatattributen. Den adresserbara nyckeln och hemligheten som skapas på det här sättet markeras som hanterade nycklar och hemligheter, vars livstid hanteras av Key Vault. Hanterade nycklar och hemligheter är skrivskyddade. Om ett KV-certifikat upphör att gälla eller inaktiveras blir motsvarande nyckel och hemlighet obrukbar.  

 Om detta är den första åtgärden för att skapa ett KV-certifikat krävs en princip.  En princip kan också levereras med successiva skapa åtgärder för att ersätta principresursen. Om en princip inte anges används principresursen för tjänsten för att skapa en nästa version av KV-certifikatet. Observera att medan en begäran om att skapa en nästa version pågår, förblir det aktuella KV-certifikatet och motsvarande adresserbar nyckel och hemlighet oförändrade.  

## <a name="self-issued-certificate"></a>Självutfärdat certifikat
 Om du vill skapa ett självutfärdat certifikat anger du utfärdarnamnet som "Själv" i certifikatprincipen enligt följande utdrag från certifikatprincipen.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Om utfärdarnamnet inte anges anges utfärdarnamnet till "Okänd". När utfärdaren är "Okänd" måste certifikatägaren manuellt hämta ett x509-certifikat från utfärdaren som han/hon väljer och sedan slå samman det offentliga x509-certifikatet med det viktiga valvcertifikatet som väntar på objekt för att slutföra certifikatet.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partnerleverantörer
Skapandet av certifikat kan slutföras manuellt eller med hjälp av en "Self"-utfärdare. Key Vault samarbetar också med vissa emittentleverantörer för att förenkla skapandet av certifikat. Följande typer av certifikat kan beställas för nyckelvalv med dessa partnerutfärdare.  

|Leverantör|Certifikattyp|  
|--------------|----------------------|  
|DigiCert|Key Vault erbjuder OV- eller EV SSL-certifikat med DigiCert|
|GlobalSign|Key Vault erbjuder OV- eller EV SSL-certifikat med GlobalSign|

 En certifikatutfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan till ett KV-certifikat. utfärdarens namn, leverantör, autentiseringsuppgifter och annan administrativ information.

Observera att när en order görs hos utfärdaren kan den uppfylla eller åsidosätta x509-certifikattilläggen och certifikatets giltighetsperiod baserat på typen av certifikat.  

 Auktorisering: Kräver certifikat/skapa behörighet.

## <a name="see-also"></a>Se även
 - [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
 - [Övervaka och hantera processen för att skapa certifikat](create-certificate-scenarios.md)
