---
title: Metoder för att skapa certifikat
description: Sätt att skapa ett certifikat i Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7eb4d80933e06b9eceb072bee93e2b127cfa9ffe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727430"
---
# <a name="certificate-creation-methods"></a>Metoder för att skapa certifikat

 Ett Key Vault (KV)-certifikat kan antingen skapas eller importeras till ett nyckelvalv. När ett KV certifikat skapas den privata nyckeln skapas i nyckelvalvet och aldrig exponeras för certifikatets ägare. Här följer några sätt att skapa ett certifikat i Key Vault:  

-   **Skapa ett självsignerat certifikat:** Detta skapar ett offentligt / privat nyckelpar och associera det med ett certifikat. Certifikatet ska signeras av en egen nyckel.  

-    **Skapa ett nytt certifikat manuellt:** Detta skapar ett offentligt / privat nyckelpar och generera ett X.509-certifikat som förfrågan. Signering begäran kan signeras av din registreringsutfärdare eller certifikatutfärdare. Signerade x509 certifikat kan sammanfogas med den väntande nyckeln koppla för att slutföra KV certifikatet i Key Vault. Den här metoden kräver flera steg, tillhandahåller men du med ökad säkerhet eftersom den privata nyckeln skapas i och begränsade till Key Vault. Detta förklaras i diagrammet nedan.  

![Skapa ett certifikat med en egen certifikatutfärdare](media/certificate-authority-1.png)  

Med följande motsvarar grön bokstäver stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som internt börjar genom att skapa en nyckel i ditt nyckelvalv.
2. Key Vault återgår till programmet en begäran om certifikatsignering (CSR)
3. Ditt program skickar CSR till vald CA: N.
4. Vald CA svarar med en X509 certifikat.
5. Programmet har nya certifikat skapat med en sammanslagning av X509 certifikat från Certifikatutfärdaren.

-   **Skapa ett certifikat med en känd utfärdare-provider:** Den här metoden måste du göra en gång för att skapa en utfärdare-objektet. När ett utfärdare-objekt skapas i du nyckelvalv, kan referera till dess namn i principen för KV certifikatet. En begäran om att skapa sådana KV certifikat skapar ett nyckelpar i valvet och kommunicera med utfärdaren provider-tjänsten med hjälp av informationen i det refererade utfärdare-objektet för att hämta en x509 certifikat. X509 certifikatet hämtas från tjänsten utfärdare och sammanfogas med nyckelpar för att slutföra KV-certifikat.  

![Skapa ett certifikat med en certifikatutfärdare för Key Vault inlett ett samarbete](media/certificate-authority-2.png)  

Med följande motsvarar grön bokstäver stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat som internt börjar genom att skapa en nyckel i ditt nyckelvalv.
2. Key Vault skickar och SSL certifikatbegäran till Certifikatutfärdaren.
3. Programmet söker, i en loop och wait-process för Key Vault för slutförande av certifikat. Skapandet av certifikat har slutförts när Key Vault får Certifikatutfärdarens svar med x509 certifikat.
4. CA: N som svarar på Key Vault begäran av SSL-certifikat med en X509 SSL-certifikat.
5. Din nya certifikatet har skapats med en sammanslagning av X509 certifikatet för Certifikatutfärdaren.

## <a name="asynchronous-process"></a>Asynkron åtgärd
Skapandet av KV certifikat är en asynkron process. Den här åtgärden skapar en certifikatbegäran för KV och returnera ett http-statuskod 202 (accepterad). Status för begäran kan spåras genom att avsöka den väntande objekt som skapats med den här åtgärden. Fullständig URI för väntande objekt returneras i LOCATION-huvudet.  

När en begäran om att skapa ett certifikat för KV är klar status för väntande objekt ändras till ”slutfört” från ”inprogress” och en ny version av KV certifikatet kommer att skapas. Detta blir den aktuella versionen.  

## <a name="first-creation"></a>Skapa en första
 När ett KV certifikat skapas för första gången, skapas också en adresserbara nyckel och hemlighet med samma namn som den för den certifikatet. Om namnet redan används, misslyckas åtgärden med en HTTP-statuskod 409 (konflikt).
Få deras attribut från certifikatattribut KV adresserbara nyckel och hemlighet. Den adresserbara nyckel och hemlighet som skapats på detta sätt är markerade som hanterade nycklar och hemligheter, vars livstid hanteras av Key Vault. Hanterade nycklar och hemligheter är skrivskyddade. Obs! Om ett KV certifikat upphör att gälla eller är inaktiverad, blir motsvarande nyckel och hemlighet inte fungerar alls.  

 Om det här är den första åtgärden att skapa ett KV certifikat krävs en princip.  En princip kan också anges med efterföljande skapa åtgärder för att ersätta den för Principresursen. Om en princip anges används Principresursen på tjänsten för att skapa en nästa version av KV certifikat. Observera att när en begäran om att skapa en nästa version är pågående, aktuella KV certifikatet och motsvarande adresserbara nyckel och hemlighet, förblir oförändrade.  

## <a name="self-issued-certificate"></a>Egen utfärdat certifikat
 Om du vill skapa en egen utfärdat certifikat, ange Utfärdarens namn som ”Self” i certifikatprincip som visas i följande kodavsnitt från certifikatprincip.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Om Utfärdarens namn anges anges Utfärdarens namn till ”okänt”. När utfärdare är ”okänt”, har certifikatets ägare att manuellt hämta en x509 certifikat från utfärdaren av hans/hennes val och sedan Sammanfoga den offentliga x509 certifikat med key vault-certifikat väntande objekt att slutföra skapandet av certifikat.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Samarbetade för CA-Providers
Skapandet av certifikat kan vara slutförd manuellt eller med en ”Self” utfärdare. Key Vault samarbetar också med vissa utfärdare-leverantörer för att underlätta skapandet av certifikat. Följande typer av certifikat kan beställas för key vault med dessa partner utfärdare providers.  

|Leverantör|Certifikattyp|  
|--------------|----------------------|  
|DigiCert|Key Vault erbjuder OV eller EV SSL-certifikat med DigiCert|
|GlobalCert|Key Vault erbjuder OV eller EV SSL-certifikat med GlobalSign|

 En certifikatutfärdare är en entitet som representeras i Azure Key Vault (KV) som en CertificateIssuer resurs. Används för att ange information om källan för ett certifikat för KV; Utfärdarens namn, leverantör, autentiseringsuppgifter och andra administrativa uppgifter.

Observera att när en beställning görs med utfärdaren-providern, det kan respektera eller Åsidosätt x509 certifikattillägg och giltighetsperioden baserat på vilken typ av certifikat.  

 Auktorisering: Kräver certifikat/skapa-behörighet.

## <a name="see-also"></a>Se även
 - [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
 - [Övervaka och hantera processen för att skapa certifikat](create-certificate-scenarios.md)
