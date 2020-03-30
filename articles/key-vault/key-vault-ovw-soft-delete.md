---
title: Mjuk borttagning av Azure Key Vault | Microsoft-dokument
description: Med mjukborttagning i Azure Key Vault kan du återställa borttagna nyckelvalv och nyckelvalvobjekt, till exempel nycklar, hemligheter och certifikat.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 9c72b2ea71da94215fc9193ffdf3906449ec5571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457380"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Översikt av mjuk borttagning för Azure Key Vault

Key Vaults mjuka borttagningsfunktion gör det möjligt att återställa de borttagna valven och valvobjekten, så kallade soft-delete. Specifikt tar vi upp följande scenarier:

- Stöd för borttagning av nyckelvalv som kan återställas
- Stöd för borttagning av nyckelvalvsobjekt (t.ex. nycklar, hemligheter, certifikat)

## <a name="supporting-interfaces"></a>Stödgränssnitt

Funktionen för mjuk borttagning är ursprungligen tillgänglig via gränssnitten [REST,](/rest/api/keyvault/) [CLI,](key-vault-soft-delete-cli.md) [PowerShell](key-vault-soft-delete-powershell.md) och [.NET/C#.](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)

## <a name="scenarios"></a>Scenarier

Azure Key Vaults spåras resurser som hanteras av Azure Resource Manager. Azure Resource Manager anger också ett väldefinierat beteende för borttagning, vilket kräver att en lyckad DELETE-åtgärd måste resultera i att resursen inte längre är tillgänglig. Funktionen för mjuk borttagning åtgärdar återställningen av det borttagna objektet, oavsett om borttagningen var oavsiktlig eller avsiktlig.

1. I det vanliga scenariot kan en användare av misstag ha tagit bort ett nyckelvalv eller ett nyckelvalvsobjekt. Om nyckelvalvet eller nyckelvalvsobjektet skulle kunna återställas under en förutbestämd period, kan användaren ångra borttagningen och återställa sina data.

2. I ett annat scenario kan en otillåten användare försöka ta bort ett nyckelvalv eller ett nyckelvalvsobjekt, till exempel en nyckel i ett valv, för att orsaka avbrott i verksamheten. Att separera borttagningen av nyckelvalvet eller nyckelvalvsobjektet från den faktiska borttagningen av underliggande data kan användas som en säkerhetsåtgärd genom att till exempel begränsa behörigheter för databorttagning till en annan, betrodd roll. Den här metoden kräver effektivt kvorum för en åtgärd som annars kan resultera i en omedelbar dataförlust.

### <a name="soft-delete-behavior"></a>Mjuk-delete-beteende

När mjuk borttagning är aktiverat behålls resurser som markerats som borttagna resurser under en angiven period (90 dagar som standard). Tjänsten tillhandahåller vidare en mekanism för att återställa det borttagna objektet, vilket i huvudsak ångrar borttagningen.

När du skapar ett nytt nyckelvalv är mjukborttagning aktiverat som standard. Du kan skapa ett nyckelvalv utan att ta bort [det mjukt](key-vault-soft-delete-cli.md) via Azure CLI eller Azure [Powershell](key-vault-soft-delete-powershell.md). När mjuk borttagning är aktiverat i ett nyckelvalv kan den inte inaktiveras

Standardlagringsperioden är 90 dagar, men när nyckelvalv skapas är det möjligt att ange bevarandeprincipintervallet till ett värde från 7 till 90 dagar via Azure-portalen. Principen för rensningsskydd använder samma intervall. När du har angett kan bevarandeprincipintervallet inte ändras.

Du kan inte återanvända namnet på ett nyckelvalv som har tagits bort mjukt tills kvarhållningsperioden har passerats.

### <a name="purge-protection"></a>Rensa skydd 

Rensa skydd är ett valfritt Key Vault-beteende och är **inte aktiverat som standard**. Den kan slås på via [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) eller [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

När rensningsskydd är aktiverat kan ett valv eller ett objekt i det borttagna tillståndet inte rensas förrän kvarhållningsperioden har passerats. Mjuka borttagna valv och objekt kan fortfarande återställas, vilket säkerställer att bevarandeprincipen följs. 

Standardlagringsperioden är 90 dagar, men det är möjligt att ange bevarandeprincipintervallet till ett värde från 7 till 90 dagar via Azure-portalen. När bevarandeprincipintervallet har angetts och sparats kan det inte ändras för det valvet. 

### <a name="permitted-purge"></a>Tillåten utrensning

Permanent ta bort, rensa, ett nyckelvalv är möjligt via en POST-åtgärd på proxyresursen och kräver särskilda privilegier. I allmänhet kan endast prenumerationsägaren rensa ett nyckelvalv. POST-åtgärden utlöser omedelbar och oåterkallelig radering av valvet. 

Undantag är:
- När Azure-prenumerationen har markerats som *oeklarbar*. I det här fallet kan endast tjänsten sedan utföra den faktiska borttagningen och gör det som en schemalagd process. 
- När flaggan --enable-purge-protection är aktiverad i själva valvet. I det här fallet väntar Key Vault i 90 dagar från det att det ursprungliga hemliga objektet har markerats för att borttagningen ska tas bort permanent.

### <a name="key-vault-recovery"></a>Återställning av nyckelvalv

När du tar bort ett nyckelvalv skapar tjänsten en proxyresurs under prenumerationen och lägger till tillräckligt med metadata för återställning. Proxyresursen är ett lagrat objekt som är tillgängligt på samma plats som det borttagna nyckelvalvet. 

### <a name="key-vault-object-recovery"></a>Återställning av nyckelvalvobjekt

När du tar bort ett nyckelvalvsobjekt, till exempel en nyckel, placerar tjänsten objektet i ett borttaget tillstånd, vilket gör det oåtkomligt för alla hämtningsåtgärder. I det här läget kan nyckelvalvsobjektet bara visas, återställas eller bort med kraft/permanent. 

Samtidigt schemalägger Key Vault borttagningen av de underliggande data som motsvarar det borttagna nyckelvalvet eller nyckelvalvsobjektet för körning efter ett förutbestämt kvarhållningsintervall. DNS-posten som motsvarar valvet behålls också under kvarhållningsintervallets varaktighet.

### <a name="soft-delete-retention-period"></a>Lagringsperiod för mjuk borttagning

Mjuka borttagna resurser behålls under en viss tidsperiod, 90 dagar. Under kvarhållningsintervallet för mjuk borttagning gäller följande:

- Du kan lista alla nyckelvalv och nyckelvalvsobjekt i mjukt borttagningsläge för din prenumeration samt åtkomstborttagning och återställningsinformation om dem.
    - Endast användare med särskilda behörigheter kan lista borttagna valv. Vi rekommenderar att våra användare skapar en anpassad roll med dessa särskilda behörigheter för hantering av borttagna valv.
- Det går inte att skapa ett nyckelvalv med samma namn på samma plats. På motsvarande sätt kan ett nyckelvalvsobjekt inte skapas i ett givet valv om nyckelvalvet innehåller ett objekt med samma namn och som är i ett borttaget tillstånd 
- Endast en särskilt privilegierad användare kan återställa ett nyckelvalv eller nyckelvalvsobjekt genom att utfärda ett återställningskommando på motsvarande proxyresurs.
    - Användaren, som är medlem i den anpassade rollen, som har behörighet att skapa ett nyckelvalv under resursgruppen kan återställa valvet.
- Endast en särskilt privilegierad användare kan med våld ta bort ett nyckelvalv eller nyckelvalvsobjekt genom att utfärda ett borttagningskommando på motsvarande proxyresurs.

Om inte ett nyckelvalv eller nyckelvalvsobjekt återställs utför tjänsten i slutet av kvarhållningsintervallet en rensning av det borttagna nyckelvalvet eller nyckelvalvsobjektet och dess innehåll. Det kan hända att resursborttagningen inte har schemaläggs.

### <a name="billing-implications"></a>Konsekvenser för fakturering

I allmänhet, när ett objekt (ett nyckelvalv eller en nyckel eller en hemlighet) är i borttaget tillstånd, finns det bara två åtgärder som är möjliga: "rensa" och "återställa". Alla andra åtgärder misslyckas. Därför, även om objektet finns, kan inga åtgärder utföras och därmed ingen användning kommer att ske, så ingen faktura. Det finns dock följande undantag:

- Rensnings- och återställningsåtgärder räknas in i normala nyckelvalvsåtgärder och faktureras.
- Om objektet är en HSM-nyckel tillkommer avgiften "HSM Protected key" per nyckelversion och månad om en nyckelversion har använts under de senaste 30 dagarna. Därefter, eftersom objektet är i borttaget tillstånd inga åtgärder kan utföras mot den, så ingen avgift kommer att gälla.

## <a name="next-steps"></a>Nästa steg

Följande två stödlinjer erbjuder de primära användningsscenarierna för att använda mjuk borttagning.

- [Så här använder du Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) 
- [Så här använder du Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md)

