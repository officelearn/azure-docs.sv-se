---
title: Metoder för att skapa certifikat
description: Sätt att skapa ett certifikat i Nyckelvalvet.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058339"
---
# <a name="certificate-creation-methods"></a>Metoder för att skapa certifikat

 Ett certifikat för Key Vault KV kan antingen skapas eller importeras till en nyckelvalvet. När ett certifikat för KV skapas den privata nyckeln skapas i nyckelvalvet och aldrig exponeras för certifikatets ägare. Följande är ett sätt att skapa ett certifikat i Nyckelvalvet:  

-   **Skapa ett självsignerat certifikat:** detta skapar ett privat-offentligt nyckelpar och associera det med ett certifikat. Certifikatet ska signeras av sin egen nyckel.  

-    **Skapa ett nytt certifikat manuellt:** detta skapar ett privat-offentligt nyckelpar och generera ett X.509-certifikat som förfrågan. Signering begäran kan signeras av registreringsutfärdare eller certifikatutfärdare (CA). Signerade x509 certifikat kan slås samman med väntande nyckeln koppla för att slutföra KV certifikatet i Nyckelvalvet. Den här metoden kräver fler steg, tillhandahåller men du med större säkerhet eftersom den privata nyckeln skapas i och begränsat till Nyckelvalvet. Detta förklaras i diagrammet nedan.  

![Skapa ett certifikat med en egen certifikatutfärdare](media/certificate-authority-1.png)  

Följande beskrivningar motsvarar grön bokstäver stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som internt börjar genom att skapa en nyckel i nyckelvalvet.
2. Returnerar Key Vault för ditt program för en begäran om certifikatsignering (CSR)
3. Programmet skickar Kundservicerepresentanten till vald Certifikatutfärdare.
4. Vald CA svarar med en X509 certifikat.
5. Programmet har slutförts nya certifikat skapas med en fusion X509 certifikat från Certifikatutfärdaren.

-   **Skapa ett certifikat med en känd utfärdaren provider:** den här metoden måste du göra en gång för att skapa ett utfärdaren-objekt. När en utfärdare-objektet har skapats i du nyckeln valvet, kan referera till dess namn i principen för KV certifikatet. En begäran om att skapa sådana KV certifikat skapar ett nyckelpar i valvet och kommunicera med tjänsten utfärdaren providern med hjälp av informationen i objektet som refereras till utfärdaren för att hämta x509 certifikat. X509 certifikat hämtas från tjänsten utfärdaren och kopplas till nyckelpar för att slutföra KV-certifikat.  

![Skapa ett certifikat med en Key Vault tillsammans certifikatutfärdare](media/certificate-authority-2.png)  

Följande beskrivningar motsvarar grön bokstäver stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som internt börjar genom att skapa en nyckel i nyckelvalvet.
2. Key Vault skickar och SSL certifikatbegäran till Certifikatutfärdaren.
3. Ditt program, i en loop och vänta process avsökningar efter ändrade ditt Nyckelvalv för slutförande av certifikat. Det certifikatet har skapats när Key Vault får Certifikatutfärdarens svar med x509 certifikat.
4. Certifikatutfärdaren besvarar Key Vault SSL certifikatbegäran med X509 SSL-certifikat.
5. Genereringen av din nya certifikat är klar med en sammanslagning av X509 certifikatet för Certifikatutfärdaren.

## <a name="asynchronous-process"></a>Asynkron åtgärd
Skapa KV är en asynkron åtgärd. Den här åtgärden skapar en KV certifikatbegäran och returnera ett http-statuskod 202 (accepterad). Status för begäran kan spåras genom att avsöka den väntande objekt som skapas av den här åtgärden. Fullständig URI för väntande objekt returneras med rubriken plats.  

När en begäran om att skapa ett certifikat för KV är klar status för väntande objekt ändras till ”slutfört” från ”inprogress” och kommer att skapas en ny version av KV certifikatet. Detta blir den aktuella versionen.  

## <a name="first-creation"></a>Skapa en första
 När ett certifikat för KV skapas för första gången, skapas även en adresserbara nyckel och Hemlig med samma namn som certifikatet. Om namnet redan används, misslyckas åtgärden med en HTTP-statuskoden 409 (konflikt).
Adresserbara nyckel och Hemlig kan du hämta deras attribut från KV certifikat attribut. Adresserbara nyckel och Hemlig som skapats på detta sätt är markerade som hanterade nycklar och hemligheter, vars livstid hanteras av Key Vault. Hanterade nycklar och hemligheter är skrivskyddade. Obs: Om en KV certifikatet upphör att gälla eller är inaktiverad, motsvarande nyckel och Hemlig kommer att bli oanvändbara.  

 Om det här är den första åtgärden för att skapa ett certifikat för KV krävs en princip.  En princip kan också anges med efterföljande skapa åtgärder för att ersätta den för Principresursen. Om en princip anges används Principresursen på tjänsten för att skapa en nästa version av KV certifikat. Observera att när en begäran om att skapa en nästa version är pågående, aktuella KV certifikatet och motsvarande adresserbara nyckel och Hemlig, förblir oförändrad.  

## <a name="self-issued-certificate"></a>Självutfärdat certifikat
 Om du vill skapa ett självutfärdat certifikat, ange Utfärdarens namn som ”Self” i certifikatprincip enligt följande kodavsnitt från certifikatprincip.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Om Utfärdarens namn anges anges Utfärdarens namn till ”okänt”. När utfärdare är ”okänt”, certifikatets ägare måste manuellt hämta en x509 certifikat från utfärdaren av hans/hennes val och merge offentliga x509 certifikat med nyckelvalv certifikatet väntande objekt att skapa certifikatet.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Tillsammans CA-Providers
Skapande av certifikat kan vara slutförd manuellt eller med hjälp av en ”Self” utfärdare. Key Vault samarbetar också med vissa utfärdaren leverantörer att förenkla skapandet av certifikat. Följande typer av certifikat kan beställas för nyckelvalv med dessa partner utfärdaren providers.  

|Leverantör|Certifikattyp|  
|--------------|----------------------|  
|DigiCert|Key Vault erbjuder OV eller EV SSL-certifikat med DigiCert|
|GlobalCert|Key Vault erbjuder OV eller EV SSL-certifikat med GlobalSign|

 Mer information, inklusive geografiska tillgängligheten för dessa providers utfärdare, se [certifikatutfärdare](/rest/api/keyvault/certificate-issuers.md).

Observera att när en order placeras med utfärdaren providern, den kan respektera eller Åsidosätt x509 certifikattillägg och giltighetsperioden baserat på vilken typ av certifikat.  

 Auktorisering: Kräver certifikat/skapa-behörighet.

 ## <a name="see-also"></a>Se även
 - [Om nycklar och hemligheter certifikat](about-keys-secrets-and-certificates.md)
 - [Övervaka och hantera certifikat skapas](create-certificate-scenarios.md)
