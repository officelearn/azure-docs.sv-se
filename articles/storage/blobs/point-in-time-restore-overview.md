---
title: Återställning av tidpunkt för block-blobar (för hands version)
titleSuffix: Azure Storage
description: Återställning av tidpunkter för block-blobar ger skydd mot oavsiktlig borttagning eller skada genom att göra det möjligt att återställa ett lagrings konto till dess tidigare tillstånd vid en viss tidpunkt.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 60f83fae6e7e685a1065d1c01327a004d9bb2864
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675660"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Återställning av tidpunkt för block-blobar (för hands version)

Återställning vid tidpunkt ger skydd mot oavsiktlig borttagning eller skada genom att göra det möjligt att återställa block BLOB-data till ett tidigare tillstånd. Återställning på plats-till-tid är användbart i scenarier där en användare eller ett program oavsiktligt tar bort data eller där ett program fel skadar data. Vid tidpunkts återställning aktive ras även testnings scenarier som kräver att en data uppsättning återställs till ett känt tillstånd innan ytterligare tester körs.

Information om hur du aktiverar återställning av tidpunkter för ett lagrings konto finns i [Aktivera och hantera tidpunkts återställning för block-blobar (för hands version)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Så här fungerar återställning av tidpunkt

Om du vill aktivera återställning vid tidpunkter skapar du en hanterings princip för lagrings kontot och anger en kvarhållningsperiod. Under kvarhållningsperioden kan du återställa block-blobbar från det aktuella läget till ett tillstånd vid en tidigare tidpunkt.

Om du vill initiera en tidpunkts återställning anropar du åtgärden [Återställ BLOB-intervall](/rest/api/storagerp/storageaccounts/restoreblobranges) och anger en återställnings punkt i UTC-tid. Du kan ange lexicographical-intervall för behållare och blob-namn som ska återställas, eller utelämna intervallet för att återställa alla behållare i lagrings kontot. Upp till 10 lexicographical-intervall stöds per återställnings åtgärd.

Azure Storage analyserar alla ändringar som har gjorts i de angivna Blobbarna mellan den begärda återställnings punkten, anges i UTC-tid och för tillfället. Återställnings åtgärden är Atomic, så den slutförs fullständigt i återställningen av alla ändringar, eller så Miss lyckas den. Om det finns blobbar som inte kan återställas, Miss lyckas åtgärden och Läs-och skriv åtgärder till de berörda behållarna återupptas.

Endast en återställnings åtgärd kan köras på ett lagrings konto i taget. En återställnings åtgärd kan inte avbrytas när den pågår, men en andra återställnings åtgärd kan utföras för att ångra den första åtgärden.

Åtgärden **Återställ BLOB-intervall** returnerar ett återställnings-ID som unikt identifierar åtgärden. Om du vill kontrol lera statusen för en tidpunkts återställning anropar du åtgärden **Hämta återställnings status** med det återställnings-ID som returnerades från åtgärden **Återställ BLOB-intervall** .

Tänk på följande begränsningar för återställnings åtgärder:

- Ett block som har överförts via funktionen för att [blockera eller](/rest/api/storageservices/put-block) [blockera från URL](/rest/api/storageservices/put-block-from-url), men som inte har allokerats via [list block](/rest/api/storageservices/put-block-list), är inte en del av en blob och återställs därför inte som en del av en återställnings åtgärd.
- Det går inte att återställa en blob med ett aktivt lån. Om en blob med ett aktivt lån ingår i det intervall med blobbar som ska återställas, Miss Miss kan återställnings åtgärden samköras.
- Ögonblicks bilder skapas eller tas inte bort som en del av en återställnings åtgärd. Endast bas-bloben återställs till sitt tidigare tillstånd.
- Om en BLOB har flyttats mellan frekventa och låg frekventa nivåer under perioden mellan den aktuella tidpunkten och återställnings punkten, återställs blobben till den tidigare nivån. En blob som har flyttats till Arkiv nivån kommer dock inte att återställas.

> [!IMPORTANT]
> När du utför en återställnings åtgärd, Azure Storage blockera data åtgärder på Blobbarna i de intervall som återställs under drift tiden. Läs-, skriv-och borttagnings åtgärder blockeras på den primära platsen. Därför kan åtgärder som att Visa behållare i Azure Portal inte utföras som förväntat medan återställnings åtgärden pågår.
>
> Läs åtgärder från den sekundära platsen kan fortsätta under återställnings åtgärden om lagrings kontot är geo-replikerat.

> [!CAUTION]
> Återställning vid tidpunkt stöder bara återställnings åtgärder på block-blobbar. Det går inte att återställa åtgärder på behållare. Om du tar bort en behållare från lagrings kontot genom att anropa åtgärden [ta bort behållare](/rest/api/storageservices/delete-container) under för hands versionen av tidpunkten för återställning av tidpunkt, kan den behållaren inte återställas med en återställnings åtgärd. Ta bort enskilda blobbar under för hands versionen, i stället för att ta bort en behållare, om du kanske vill återställa dem.

### <a name="prerequisites-for-point-in-time-restore"></a>Krav för återställning av punkt-till-tid

Vid återställning från tidpunkt krävs att följande Azure Storage funktioner är aktiverade:

- [Mjuk borttagning](soft-delete-overview.md)
- [Ändra feed (förhands granskning)](storage-blob-change-feed.md)
- [Blob-version (för hands version)](versioning-overview.md)

Aktivera de här funktionerna för lagrings kontot innan du aktiverar tidpunkts återställning. Se till att du registrerar dig för för hands versionerna av ändra feed och blob-versioner innan du aktiverar dem.

### <a name="retention-period-for-point-in-time-restore"></a>Kvarhållningsperiod för återställning av tidpunkt

När du aktiverar tidpunkts återställning för ett lagrings konto anger du en kvarhållningsperiod. Block-blobbar i ditt lagrings konto kan återställas under kvarhållningsperioden.

Kvarhållningsperioden börjar när du aktiverar tidpunkts återställning. Tänk på att du inte kan återställa blobbar till ett tillstånd innan kvarhållningsperioden börjar. Om du till exempel har aktiverat återställning av tidpunkt på plats den 1 maj med en kvarhållning på 30 dagar kan du sedan återställa till högst 15 dagar. Den 1 juni kan du återställa data från mellan 1 och 30 dagar.

Kvarhållningsperioden för återställning av plats-i-tid måste vara minst en dag under den angivna kvarhållningsperioden för mjuk borttagning. Om till exempel den mjuka borttagnings perioden har angetts till 7 dagar kan den kvarhållna tiden för återställning av återställnings perioden vara mellan 1 och 6 dagar.

### <a name="permissions-for-point-in-time-restore"></a>Behörigheter för återställning till tidpunkt

För att initiera en återställnings åtgärd måste klienten ha Skriv behörighet till alla behållare i lagrings kontot. Om du vill bevilja behörighet att auktorisera en återställnings åtgärd med Azure Active Directory (Azure AD) tilldelar du rollen **lagrings konto deltagare** rollen som säkerhets objekt på lagrings kontots, resurs gruppens eller prenumerationens nivå.

## <a name="about-the-preview"></a>Om för hands versionen

Återställning av punkt-i-tid stöds endast för generella v2-lagrings konton. Endast data i nivåerna frekvent och låg frekvent åtkomst kan återställas med återställning vid tidpunkter.

Följande regioner har stöd för återställning av tidpunkt vid tid i för hands versionen:

- Kanada, centrala
- Kanada, östra
- Frankrike, centrala

För hands versionen innehåller följande begränsningar:

- Det finns inte stöd för att återställa Premium block-blobar.
- Det finns inte stöd för att återställa blobar på arkivnivå. Om till exempel en blog på frekvent nivå flyttades till en arkivnivå för två dagar sedan, och en återställningsåtgärd återställer till en punkt för tre dagar sedan, återställs inte bloben till frekvent nivå.
- Det finns inte stöd för att återställa Azure Data Lake Storage Gen2 enkla och hierarkiska namn områden.
- Det finns inte stöd för att återställa lagrings konton med kund tillhandahållna nycklar.

> [!IMPORTANT]
> För hands versionen av tidpunkt för återställning är endast avsedd för användning utan produktion. Service nivå avtal (service avtal) för produktions tjänster är inte tillgängliga för närvarande.

### <a name="register-for-the-preview"></a>Registrera dig för för hands versionen

Om du vill registrera dig för för hands versionen kör du följande kommandon:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Kontrol lera registrerings status

Registrering för återställning av tidpunkt är automatisk och bör ta mindre än 10 minuter. Kör följande kommandon för att kontrol lera status för registreringen:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>Priser och fakturering

Fakturering för tidpunkts återställning beror på mängden data som bearbetas för att utföra återställnings åtgärden. Mängden data som bearbetas baseras på antalet ändringar som inträffat mellan återställnings punkten och aktuell tidpunkt. Om du till exempel antar en relativt konstant ändrings takt för att blockera BLOB-data i ett lagrings konto skulle en återställnings åtgärd som går tillbaka i tid 1 dag kosta 1/10 av en återställning som går tillbaka om 10 dagar.

Om du vill uppskatta kostnaden för en återställnings åtgärd granskar du loggen för ändrings flöden för att beräkna den mängd data som ändrades under återställnings perioden. Om till exempel kvarhållningsperioden för ändrings flöde är 30 dagar och storleken på ändrings flödet är 10 MB, så kostar det ett tredje tredjedel av priset som anges för ett LRS-konto i den regionen. Att återställa till en punkt som är 27 dagar tidigare skulle kosta cirka nio tiondelar av priset i listan.

Mer information om priser för återställning av tidpunkter finns i [blocking BLOB-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Ställ frågor eller ge feedback

Om du vill ställa frågor om för hands versionen av tidpunkt för återställning eller lämna feedback kontaktar du Microsoft på pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Nästa steg

- [Aktivera och hantera tidpunkts återställning för block-blobar (för hands version)](point-in-time-restore-manage.md)
- [Ändra stöd för feed i Azure Blob Storage (för hands version)](storage-blob-change-feed.md)
- [Aktivera mjuk borttagning för blobbar](soft-delete-enable.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
