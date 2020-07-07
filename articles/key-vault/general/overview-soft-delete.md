---
title: Azure Key Vault mjuk borttagning | Microsoft Docs
description: Med mjuk borttagning i Azure Key Vault kan du återställa borttagna nyckel valv och Key Vault-objekt, till exempel nycklar, hemligheter och certifikat.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 347f8a4cf1fb95849bcf1008e91d17878f3d01f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82598529"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Översikt av mjuk borttagning för Azure Key Vault

Key Vault funktionen för mjuk borttagning kan återställa de borttagna valven och valv objekt, som kallas mjuk borttagning. Mer specifikt åtgärdar vi följande scenarier:

- Stöd för rekonstruerbar borttagning av ett nyckel valv
- Stöd för rekonstruerbar borttagning av Key Vault-objekt (t. ex. nycklar, hemligheter, certifikat)

## <a name="supporting-interfaces"></a>Stöd för gränssnitt

Funktionen mjuk borttagning är inlednings vis tillgänglig via [rest](/rest/api/keyvault/)-, [CLI](soft-delete-cli.md)-, [PowerShell](soft-delete-powershell.md)-och [.net/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) -gränssnitten, samt [arm-mallar](https://docs.microsoft.com/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Scenarier

Azure Key Vaults är spårade resurser som hanteras av Azure Resource Manager. Azure Resource Manager anger också ett väldefinierat beteende för borttagning, vilket kräver att en lyckad BORTTAGNINGs åtgärd innebär att resursen inte är tillgänglig längre. Funktionen för mjuk borttagning tar bort återställningen av det borttagna objektet, oavsett om borttagningen var oavsiktlig eller avsiktlig.

1. I ett typiskt scenario kan en användare oavsiktligt ha tagit bort ett nyckel valv eller ett nyckel valvs objekt. om det nyckel valvet eller Key Vault-objektet skulle återställas till en fördefinierad period, kan användaren ångra borttagningen och återställa sina data.

2. I ett annat scenario kan en falsk användare försöka ta bort ett nyckel valv eller ett nyckel valvs objekt, till exempel en nyckel i ett valv, för att orsaka ett verksamhets avbrott. Att separera borttagningen av nyckel valvet eller Key Vault-objektet från den faktiska borttagningen av underliggande data kan användas som ett säkerhets mått av, t. ex. begränsa behörigheter för data borttagning till en annan, betrodd roll. Den här metoden kräver ett effektivt kvorum för en åtgärd som annars kan resultera i en omedelbar data förlust.

### <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

När mjuk borttagning är aktiverat behålls resurser som har marker ATS som borttagna resurser för en angiven period (90 dagar som standard). Tjänsten tillhandahåller ytterligare en mekanism för att återställa det borttagna objektet, i princip ångra borttagningen.

När du skapar ett nytt nyckel valv är mjuk borttagning aktiverat som standard. Du kan skapa ett nyckel valv utan mjuk borttagning via [Azure CLI](soft-delete-cli.md) eller [Azure PowerShell](soft-delete-powershell.md). När mjuk borttagning har Aktiver ATS för ett nyckel valv kan det inte inaktive ras

Standardkvarhållning är 90 dagar, men när nyckel valvet skapas är det möjligt att ange lagrings princip intervallet till ett värde mellan 7 och 90 dagar via Azure Portal. Bevarande principen rensa skydd använder samma intervall. När du har angett går det inte att ändra lagrings princip intervallet.

Du kan inte återanvända namnet på ett nyckel valv som har varit mjuk-Borttaget tills kvarhållningsperioden har passerat.

### <a name="purge-protection"></a>Rensa skydd 

Rensnings skyddet är ett valfritt Key Vault beteende och är **inte aktiverat som standard**. Rensnings skydd kan bara aktive ras när mjuk borttagning har Aktiver ATS.  Den kan aktive ras via [CLI](soft-delete-cli.md#enabling-purge-protection) eller [PowerShell](soft-delete-powershell.md#enabling-purge-protection).

När rensnings skyddet är på kan inte ett valv eller ett objekt i det borttagna läget rensas förrän kvarhållningsperioden har passerat. Borttagnings bara valv och objekt kan fortfarande återställas, vilket säkerställer att bevarande principen kommer att följas. 

Standardkvarhållning är 90 dagar, men det är möjligt att ange ett värde mellan 7 och 90 dagar via Azure Portal för bevarande princip. När lagrings princip intervallet har angetts och sparats kan det inte ändras för det valvet. 

### <a name="permitted-purge"></a>Tillåten rensning

Det går att ta bort, rensa och ta bort ett nyckel valv via en POST-åtgärd på proxy-resursen och kräver särskilda privilegier. I allmänhet kommer bara prenumerations ägaren att kunna rensa ett nyckel valv. POST-åtgärden utlöser omedelbar och oåterkalleligt borttagning av det valvet. 

Undantag är:
- När Azure-prenumerationen har marker ATS som *borttagnings*bar. I det här fallet kan endast tjänsten utföra den faktiska borttagningen och göra det som en schemalagd process. 
- När `--enable-purge-protection flag` är aktive rad i själva valvet. I det här fallet kommer Key Vault att vänta i 90 dagar från när det ursprungliga hemliga objektet markerades för borttagning för att ta bort objektet permanent.

### <a name="key-vault-recovery"></a>Återställning av nyckel valv

När du tar bort ett nyckel valv skapar tjänsten en proxy-resurs under prenumerationen och lägger till tillräckligt många metadata för återställning. Proxykonfigurationen är ett lagrat objekt som är tillgängligt på samma plats som det borttagna nyckel valvet. 

### <a name="key-vault-object-recovery"></a>Nyckel valvs objekt återställning

När du tar bort ett nyckel valv objekt, till exempel en nyckel, kommer tjänsten att placera objektet i ett borttaget tillstånd, vilket gör att det inte går att komma åt några hämtnings åtgärder. I det här läget kan Key Vault-objektet bara visas, återställas eller framtvingas/tas bort permanent. 

Samtidigt schemalägger Key Vault borttagningen av underliggande data som motsvarar det borttagna nyckel valvet eller Key Vault-objektet som ska köras efter ett förutbestämt kvarhållningsintervall. DNS-posten som motsvarar valvet behålls också under lagrings periodens varaktighet.

### <a name="soft-delete-retention-period"></a>Kvarhållningsperiod för mjuk borttagning

Mjuka, borttagna resurser bevaras under en angiven tids period, 90 dagar. Under det mjuka borttagnings intervallet gäller följande:

- Du kan visa alla nyckel valv och Key Vault-objekt i läget för mjuk borttagning för din prenumeration samt åtkomst borttagning och återställnings information om dem.
    - Endast användare med särskilda behörigheter kan lista borttagna valv. Vi rekommenderar att användarna skapar en anpassad roll med dessa särskilda behörigheter för att hantera borttagna valv.
- Det går inte att skapa ett nyckel valv med samma namn på samma plats. Det går inte att skapa ett Key Vault-objekt i ett angivet valv om det nyckel valvet innehåller ett objekt med samma namn och som är i ett borttaget tillstånd 
- Endast en särskilt privilegie rad användare kan återställa ett Key Vault-eller Key Vault-objekt genom att utfärda ett Restore-kommando på motsvarande proxy-resurs.
    - Användaren, som är medlem i den anpassade rollen, som har behörighet att skapa ett nyckel valv under resurs gruppen kan återställa valvet.
- Endast en särskilt privilegie rad användare kan tvinga bort ett nyckel valv eller ett nyckel valvs objekt genom att utfärda ett DELETE-kommando på motsvarande proxyvärd.

Om inte ett nyckel valv eller Key Vault-objekt återställs i slutet av kvarhållningsintervallet, utför tjänsten en rensning av det mjuka borttagna nyckel valvet eller Key Vault-objektet och dess innehåll. Det går inte att ändra schemat för resurs borttagning.

### <a name="billing-implications"></a>Debiterings konsekvenser

I allmänhet finns det bara två åtgärder som är tillgängliga när ett objekt (ett nyckel valv eller en nyckel eller en hemlighet) är i Borttaget läge: "Rensa" och "Återställ". Det går inte att utföra alla andra åtgärder. Därför kan inga åtgärder utföras, även om objektet finns, men ingen användning görs, så ingen faktura. Det finns dock följande undantag:

- åtgärderna "Rensa" och "Återställ" räknas mot normala nyckel valvs åtgärder och debiteras.
- Om objektet är en HSM-nyckel gäller värdet "HSM Protected Key" per nyckel version per månad om en nyckel version har använts under de senaste 30 dagarna. Efter det, eftersom objektet är i Borttaget läge kan inga åtgärder utföras mot det, så ingen avgift kommer att gälla.

## <a name="next-steps"></a>Nästa steg

Följande två guider ger de vanligaste användnings scenarierna för att använda mjuk borttagning.

- [Så här använder du Key Vault mjuk borttagning med PowerShell](soft-delete-powershell.md) 
- [Så här använder du Key Vault mjuk borttagning med CLI](soft-delete-cli.md)

