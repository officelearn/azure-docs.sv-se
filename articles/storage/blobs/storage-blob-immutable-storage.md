---
title: Oåterkalleligt lagrings utrymme för Azure Storage blobbar | Microsoft Docs
description: Azure Storage erbjuder mask (Skriv en gång, Läs många) stöd för blob-lagring (objekt) som gör det möjligt för användare att lagra data i ett icke-redigerbart tillstånd för ett visst intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 9caa63972c58defe2e8e2b33b6c2d29b15c7ce84
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168377"
---
# <a name="store-business-critical-data-in-azure-blob-storage-immutably"></a>Lagra affärs kritiska data i Azure Blob Storage immutably 

Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många). Det här läget gör att data inte kan raderas och inte kan ändras för ett användardefinierat intervall. BLOB-objekt kan skapas och läsas, men inte ändras eller tas bort, under lagrings intervallets längd. Oföränderlig lagring är aktiverat för Generell användning v2-och Blob Storage-konton i alla Azure-regioner.

## <a name="overview"></a>Översikt

Med oföränderlig lagring får sjukvårds organisationer, finansiella institutioner och relaterade branscher – särskilt Broker-handlare organisationer – för att lagra data på ett säkert sätt. Det kan också användas i alla scenarier för att skydda viktiga data mot ändringar eller borttagningar. 

Vanliga program innehåller:

- **Efterlevnad**: oföränderlig lagring för Azure Blob Storage hjälper organisationers adress SEC 17a-4 (f), CFTC 1.31 (d), FINRA och andra bestämmelser. Ett tekniskt informations dokument från Cohasset Associates beskriver hur oföränderligt lagrings utrymme som de här regleringsmyndigheterna kan hämtas via [Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/compliance/compliance-overview) innehåller detaljerad information om våra certifieringar för efterlevnad.

- **Kvarhållning av säkra dokument**: oföränderligt lagrings utrymme för Azure Blob Storage garanterar att data inte kan ändras eller tas bort av någon användare, inklusive användare med administratörs behörighet för kontot.

- **Juridiskt bevarande**: oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra känslig information som är viktig för tvister eller företags användning i ett Manipulerings bevis för önskad varaktighet tills spärren tas bort. Den här funktionen är inte begränsad till juridiska användnings fall, men kan även betraktas som ett händelsebaserade undantag eller ett företags lås, där behovet av att skydda data baserat på händelse utlösare eller företags princip krävs.

Oåterkalleligt lagrings utrymme stöder följande:

- **[Stöd för tidsbaserad bevarande princip](#time-based-retention)** : användare kan ange principer för att lagra data för ett angivet intervall. När en tidsbaserad bevarande princip anges kan blobbar skapas och läsas, men inte ändras eller tas bort. När kvarhållningsperioden har upphört att gälla kan blobbar tas bort men inte skrivas över.

- **[Support för juridiskt undantag](#legal-holds)** : om kvarhållningsintervallet inte är känt kan användarna ange juridiska undantag för att lagra data immutably tills det juridiska undantaget har rensats.  När en princip för juridiskt undantag anges kan blobbar skapas och läsas, men inte ändras eller tas bort. Varje juridiskt undantag är associerat med en användardefinierad alfanumerisk tagg (t. ex. ett ärende-ID, händelse namn osv.) som används som en ID-sträng. 

- **Stöd för alla BLOB-nivåer**: Worm-principer är oberoende av Azure Blob Storage-nivån och gäller för alla nivåer: frekvent, låg frekvent och Arkiv lag ring. Användare kan överföra data till den mest kostnads optimerade nivån för sina arbets belastningar samtidigt som de bibehåller data oföränderlighets.

- **Konfiguration av container nivå**: användare kan konfigurera tidsbaserade bevarande principer och juridiska undantags taggar på behållar nivån. Genom att använda enkla inställningar för container nivå kan användare skapa och låsa tidsbaserade bevarande principer, utöka kvarhållningsintervall, ställa in och rensa juridiska undantag med mera. Dessa principer gäller för alla blobar i behållaren, både befintliga och nya.

- **Stöd för gransknings loggning**: varje behållare innehåller en princip Gransknings logg. Det visar upp till sju tidsbaserade kvarhållning-kommandon för låsta tidsbaserade bevarande principer och innehåller användar-ID, kommando typ, tidsstämplar och kvarhållningsintervall. För juridiska undantag innehåller loggen användar-ID, kommando typ, tidsstämplar och juridiska undantags taggar. Den här loggen behålls för principens livs längd i enlighet med rikt linjerna för s 17a-4 (f). [Azure aktivitets loggen](../../azure-monitor/platform/activity-logs-overview.md) visar en mer omfattande logg över alla kontroll Plans aktiviteter. När du aktiverar [Azure Diagnostic-loggar](../../azure-monitor/platform/resource-logs-overview.md) behålls och visas data Plans åtgärder. Det är användarens ansvar att lagra dessa loggar på ett beständigt sätt, vilket kan krävas för regler eller andra användnings områden.

## <a name="how-it-works"></a>Hur det fungerar

Oföränderlig lagring för Azure Blob Storage stöder två typer av mask-eller oföränderliga principer: tidsbaserad kvarhållning och juridiska undantag. När en tidsbaserad bevarande princip eller ett juridiskt undantag tillämpas på en behållare flyttas alla befintliga blobar till ett oföränderligt WORM-tillstånd på mindre än 30 sekunder. Alla nya blobbar som överförs till behållaren flyttas också till det oföränderliga läget. När alla blobbar har flyttats till det oföränderliga läget bekräftas den oföränderliga principen och alla Skriv-och borttagnings åtgärder för befintliga och nya objekt i den oföränderliga behållaren är inte tillåtna.

Borttagning av behållare och konton tillåts inte heller om det finns blobbar som skyddas av en princip som inte kan ändras. Det går inte att ta bort container om det finns minst en blob med en låst tidsbaserad bevarande princip eller ett juridiskt undantag. Borttagningen av lagringskontot misslyckas om det finns minst en WORM-container med bevarande av juridiska skäl eller en blob med ett aktivt kvarhållningsintervall. 

### <a name="time-based-retention"></a>Tidsbaserad kvarhållning

> [!IMPORTANT]
> En tidsbaserad bevarande princip måste vara *låst* för att blobben ska vara i ett inkompatibelt läge (Write and delete Protected) för SEC 17a-4 (f) och annan myndighets efterlevnad. Vi rekommenderar att du låser principen inom rimlig tid, vanligt vis mindre än 24 timmar. Det ursprungliga läget för en tillämpad tidsbaserad bevarande princip *låses upp*, så att du kan testa funktionen och göra ändringar i principen innan du låser den. Även om det *upplåsta* läget ger oföränderlighets skydd rekommenderar vi inte att du använder det *upplåsta* läget för något annat ändamål än kortsiktiga funktions försök. 

När en tidsbaserad bevarande princip tillämpas på en behållare kommer alla blobar i behållaren att stanna kvar i det oföränderliga läget under den *gällande* kvarhållningsperioden. Den effektiva kvarhållningsperioden för befintliga blobbar är lika med skillnaden mellan tiden för skapandet av bloben och det användardefinierade kvarhållningsintervallet.

För nya blobbar är den effektiva kvarhållningsperioden lika med det kvarhållningsintervall som angetts av användaren. Eftersom användarna kan utöka kvarhållningsintervallet, använder oföränderlig lagring det senaste värdet för det användardefinierade kvarhållningsintervallet för att beräkna den effektiva kvarhållningsperioden.

> [!TIP]
> **Exempel:** En användare skapar en tidsbaserad bevarande princip med ett kvarhållningsintervall på fem år.
>
> Den befintliga blobben i behållaren, _testblob1_, skapades ett år sedan. Den effektiva kvarhållningsperioden för _testblob1_ är fyra år.
>
> En ny BLOB, _testblob2_, har nu laddats upp till behållaren. Den effektiva kvarhållningsperioden för den här nya blobben är fem år.

En upplåst tidsbaserad bevarande princip rekommenderas endast för funktions testning och en princip måste vara låst för att vara kompatibel med s 17a-4 (f) och annan myndighets efterlevnad. När en tidsbaserad bevarande princip är låst går det inte att ta bort principen och maximalt 5 tillägg till den gällande kvarhållningsperioden tillåts. Mer information om hur du ställer in och låser tidsbaserade bevarande principer finns i avsnittet [komma igång](#getting-started) .

### <a name="legal-holds"></a>Bevarande av juridiska skäl

När du ställer in ett juridiskt undantag förblir alla befintliga och nya blobbar i det oföränderliga läget tills det juridiska undantaget är avmarkerat. Mer information om hur du ställer in och rensar juridiska undantag finns i avsnittet [komma igång](#getting-started) .

En behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång. Alla blobbar i den behållaren förblir i tillståndet oföränderliga tills alla juridiska undantag har rensats, även om deras gällande kvarhållningsperiod har upphört att gälla. En BLOB förblir i ett oföränderligt tillstånd tills den gällande kvarhållningsperioden upphör att gälla, även om alla juridiska undantag har rensats.

I följande tabell visas de typer av BLOB-åtgärder som är inaktiverade för olika scenarier. Mer information finns i [Azure Blob service API-](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) dokumentationen.

|Scenario  |BLOB-tillstånd  |BLOB-åtgärder tillåts inte  |
|---------|---------|---------|
|Effektivt kvarhållningsintervall för blobben har ännu inte gått ut och/eller bevarande av juridiska skäl har angetts     |Oåterkallelig: både ta bort- och skrivskyddad         | Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ta bort behållare, ta bort BLOB, ange BLOB-metadata, sätt sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis kopia av BLOB, tilläggs block         |
|Effektivt kvarhållningsintervall på blobben har upphört att gälla     |Skrivskyddad endast (ta bort tillåts)         |Lägg till BLOB<sup>1</sup>, sätt block<sup>1</sup>, list block lista<sup>1</sup>, ange BLOB-metadata, placerings sida, ange BLOB-egenskaper, ögonblicks bilds-BLOB, stegvis kopia-BLOB, tilläggs block         |
|Alla juridiska undantag rensas och ingen tidsbaserad bevarande princip har angetts på behållaren     |Föränderlig         |Ingen         |
|Ingen mask policy skapas (tidsbaserad kvarhållning eller juridiskt undantag)     |Föränderlig         |Ingen         |

<sup>1</sup> programmet tillåter dessa åtgärder att skapa en ny BLOB en gång. Alla efterföljande överskrivnings åtgärder på en befintlig BLOB-sökväg i en oföränderlig container är inte tillåtna.

## <a name="supported-values"></a>Värden som stöds

### <a name="time-based-retention"></a>Tidsbaserad kvarhållning
- För ett lagrings konto är det maximala antalet behållare med låsta tidsbaserade, oföränderliga principer som är 1 000.
- Det minsta kvarhållningsintervallet är 1 dag. Det maximala värdet är 146 000 dagar (400 år).
- För en behållare är det maximala antalet redigeringar som utökar ett kvarhållningsintervall för låsta tidsbaserade, oföränderliga principer 5.
- För en behållare behålls högst 7 tidsbaserade logg gransknings loggar för en låst princip.

### <a name="legal-hold"></a>Juridiskt bevarande
- För ett lagrings konto är det maximala antalet behållare med en juridisk undantags inställning 1 000.
- För en behållare är det maximala antalet juridiska undantags etiketter 10.
- Den minsta längden för en juridiskt Hold-tagg är 3 alfanumeriska tecken. Den maximala längden är 23 alfanumeriska tecken.
- För en behållare behålls det maximala antalet gransknings loggar för den här principen.

## <a name="pricing"></a>Priser

Det kostar inget extra att använda den här funktionen. Oföränderliga data priss ätts på samma sätt som vanliga, föränderligt data. Pris information om Azure Blob Storage finns på sidan för [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Komma igång
Oföränderligt lagrings utrymme är bara tillgängligt för Generell användning v2-och Blob Storage-konton. Dessa konton måste hanteras via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Information om hur du uppgraderar ett befintligt Generell användning v1-lagrings konto finns i [uppgradera ett lagrings konto](../common/storage-account-upgrade.md).

De senaste versionerna av [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)och [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) stöder oföränderlig lagring för Azure Blob Storage. [Support för klient bibliotek](#client-libraries) tillhandahålls också.

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Skapa en ny container eller välj en befintlig container för lagring av de blobbar som ska behållas i det oföränderliga tillståndet.
 Behållaren måste vara i ett GPv2-eller Blob Storage-konto.
2. Välj **åtkomst princip** i behållar inställningarna. Välj sedan **+ Lägg till princip** under **oföränderligt Blob Storage**.

    ![Behållar inställningar i portalen](media/storage-blob-immutable-storage/portal-image-1.png)

3. Om du vill aktivera tidsbaserad kvarhållning väljer du **tidsbaserad kvarhållning** på den nedrullningsbara menyn.

    !["Tidsbaserad kvarhållning" som valts under "princip typ"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Ange kvarhållningsintervall i dagar (giltiga värden är 1 till 146000 dagar).

    ![Rutan uppdatera kvarhållningsperiod till](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Principens ursprungliga tillstånd är olåst så att du kan testa funktionen och göra ändringar i principen innan du låser den. Att låsa principen är viktigt för att följa regler som SEC 17a-4.

5. Lås principen. Högerklicka på ellipsen ( **...** ) och följande meny visas med ytterligare åtgärder:

    !["Lås princip" på menyn](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Välj **Lås princip** och bekräfta låset. Principen är nu låst och kan inte tas bort, endast tillägg för kvarhållningsintervallet kommer att tillåtas. BLOB-borttagningar och åsidosättningar är inte tillåtna. 

    ![Bekräfta "Lås princip" på menyn](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Om du vill aktivera juridiska undantag väljer du **+ Lägg till princip**. Välj **juridiskt undantag** i den nedrullningsbara menyn.

    !["Juridiskt bevarande" på menyn under "princip typ"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Skapa ett juridiskt undantag med en eller flera taggar.

    ![Rutan taggnamn under princip typen](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Ta bort ett juridiskt undantag genom att helt enkelt ta bort den använda taggen för juridiskt undantag.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Funktionen ingår i följande kommando grupper: `az storage container immutability-policy` och `az storage container legal-hold`. Kör `-h` på dem för att se kommandona.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Modulen AZ. Storage stöder oföränderligt lagrings utrymme.  Aktivera funktionen genom att följa dessa steg:

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Ta bort alla tidigare installationer av Azure PowerShell.
3. Installera Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

I avsnittet [exempel PowerShell-kod](#sample-powershell-code) längre fram i den här artikeln visas funktions användningen.

---

## <a name="client-libraries"></a>Klientbibliotek

Följande klient bibliotek stöder oföränderlig lagring för Azure Blob Storage:

- [.NET-klient biblioteks version 7.2.0 – för hands version och senare](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node. js-klient biblioteks version 4.0.0 och senare](https://www.npmjs.com/package/azure-arm-storage)
- [Python-klient biblioteks version 2.0.0 version Candidate 2 och senare](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java-klient bibliotek](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Kan du ge dokumentation om WORM-kompatibilitet?**

Ja. För att dokumentera efterlevnaden har Microsoft bevarat en ledande oberoende utvärderings verksamhet som specialiserar sig på hantering av registrering och informations styrning, Cohassete Associates, för att utvärdera Azure-oföränderliga Blob Storage och dess efterlevnad av krav som gäller till finans Services-branschen. Cohasset validerade att Azure-oföränderligt Blob Storage, när den används för att bevara tidsbaserade blobbar i ett WORM-tillstånd, uppfyller relevanta lagrings krav för CFTC Rule 1.31 (c)-(d), FINRA-regel 4511 och SEC-regel 17a-4. Microsoft riktade sig på den här uppsättningen regler eftersom de representerar de mest förenklade vägledningarna globalt för poster som kvarhålls för finansiella institutioner. Cohasset-rapporten finns i [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage). Kontakta Azure-supporten om du vill begära en skrivelse från Microsoft angående WORM-efterlevnad.

**Gäller funktionen bara block-blobbar, eller till sid-och bifogade blobbar.**

Oåterkalleligt lagrings utrymme kan användas med valfri Blob-typ som anges på behållar nivån, men vi rekommenderar att du använder mask för behållare som huvudsakligen lagrar block-blobbar. Till skillnad från block-blobbar måste eventuella nya sid-blobar och bifogade blobbar skapas utanför en WORM-behållare och sedan kopieras i. När du har kopierat dessa blobbar till en WORM-behållare tillåts inga ytterligare *tillägg* till en tilläggs-BLOB eller ändringar av en sid-blob. Därför rekommenderar vi att du ställer in en mask-princip på en behållare som lagrar virtuella hård diskar (sid-blobar) för alla aktiva Virtual Machines rekommenderas inte eftersom den kommer att låsa den virtuella dator disken.

**Måste jag skapa ett nytt lagrings konto för att använda den här funktionen?**

Nej, du kan använda oföränderligt lagrings utrymme med befintliga eller nyligen skapade Generell användning v2-eller Blob Storage-konton. Den här funktionen är avsedd för användning med block-blobbar i GPv2-och Blob Storage-konton. Generell användning v1-lagrings konton stöds inte, men de kan enkelt uppgraderas till Generell användning v2. Information om hur du uppgraderar ett befintligt Generell användning v1-lagrings konto finns i [uppgradera ett lagrings konto](../common/storage-account-upgrade.md).

**Kan jag använda både en juridisk princip för kvarhållning och tidsbaserad lagring?**

Ja, en behållare kan ha både ett juridiskt undantag och en tidsbaserad bevarande princip på samma gång. Alla blobbar i den behållaren förblir i tillståndet oföränderliga tills alla juridiska undantag har rensats, även om deras gällande kvarhållningsperiod har upphört att gälla. En BLOB förblir i ett oföränderligt tillstånd tills den gällande kvarhållningsperioden upphör att gälla, även om alla juridiska undantag har rensats.

**Finns det bara juridiska undantags principer för rättsliga förfaranden eller andra användnings scenarier?**

Nej, juridiskt bevarande är bara den allmänna termen som används för en icke-baserad bevarande princip. Den behöver inte bara användas för tvister som rör åtgärder. Principer för juridiskt bevarande är användbara för att inaktivera Skriv över och ta bort för att skydda viktiga Enterprise WORM-data, där kvarhållningsperioden är okänt. Du kan använda den som en företags princip för att skydda dina verksamhets kritiska mask arbets belastningar eller använda den som en princip för en mellanlagrings princip innan en anpassad händelse utlösare kräver att en tidsbaserad bevarande princip används. 

**Kan jag ta bort en _låst_ tidsbaserad bevarande princip eller ett juridiskt undantag?**

Endast upplåsta tidsbaserade bevarande principer kan tas bort från en behållare. När en tidsbaserad bevarande princip är låst kan den inte tas bort. endast tillägg för gällande kvarhållningsperiod är tillåtna. Juridiska undantags taggar kan tas bort. När alla juridiska taggar tas bort tas det juridiska undantaget bort.

**Vad händer om jag försöker ta bort en container med en *låst* tidsbaserade bevarandeprincip eller bevarande av juridiska skäl?**

Det går inte att ta bort container om det finns minst en blob med en låst tidsbaserad bevarande princip eller ett juridiskt undantag. Åtgärden för att ta bort behållare lyckas bara om det inte finns någon BLOB med ett aktivt kvarhållningsintervall och det inte finns några juridiska undantag. Du måste ta bort Blobbarna innan du kan ta bort behållaren.

**Vad händer om jag försöker ta bort ett lagringskonto med en WORM-container som har en *låst* tidsbaserad bevarandeprincip eller ett bevarande av juridiska skäl?**

Borttagningen av lagringskontot misslyckas om det finns minst en WORM-container med bevarande av juridiska skäl eller en blob med ett aktivt kvarhållningsintervall. Du måste ta bort alla mask behållare innan du kan ta bort lagrings kontot. Information om borttagning av behållare finns i föregående fråga.

**Kan jag flytta data över olika blob-nivåer (frekvent, lågfrekvent, kall) när blobben är i oförändrat tillstånd?**

Ja, du kan använda kommandot Set BLOB Tier för att flytta data mellan BLOB-nivåerna samtidigt som data hålls i ett kompatibelt tillstånd. Oföränderligt lagrings utrymme stöds på BLOB-nivåer för frekvent, låg frekvent och arkiv.

**Vad händer om jag inte betalat och mitt kvarhållningsintervall inte har gått ut?**

Vid utebliven betalning tillämpas normala data bevarande principer enligt vad som anges i avtals villkoren i avtalet med Microsoft.

**Finns det en utvärderingsversion eller en respitperiod för att bara testa funktionen?**

Ja. När en tidsbaserad bevarande princip först skapas är den i ett *låst* läge. I det här läget kan du göra önskade ändringar i kvarhållningsintervallet, till exempel ökning eller minskning och till och med ta bort principen. När principen är låst förblir den låst tills kvarhållningsintervallet upphör att gälla. Den här låsta principen förhindrar borttagning och ändring av kvarhållningsintervallet. Vi rekommenderar starkt att du bara använder det *upplåsta* läget för utvärderings syfte och låser principen inom en 24-timmarsperiod. Dessa metoder hjälper dig att följa s 17a-4 (f) och andra bestämmelser.

**Kan jag använda mjuk borttagning tillsammans med oföränderliga BLOB-principer?**

Ja. [Mjuk borttagning för Azure Blob Storage](storage-blob-soft-delete.md) gäller för alla behållare i ett lagrings konto, oavsett en juridisk undantags-eller tidsbaserad bevarande princip. Vi rekommenderar att du aktiverar mjuk borttagning för ytterligare skydd innan eventuella mask principer som inte kan användas och bekräftas. 

**Var finns funktionen?**

Oåterkalleligt lagrings utrymme är tillgängligt i Azures offentliga, Kina och myndighets regioner. Om oföränderlig lagring inte är tillgängligt i din region kan du kontakta support och e-azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Exempel på PowerShell-kod

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande exempel på PowerShell-skript är för referens. Det här skriptet skapar ett nytt lagrings konto och en behållare. Det visar sedan hur du ställer in och rensar juridiska undantag, skapar och låser en tidsbaserad bevarande princip (kallas även för en oföränderlighets-princip) och utökar kvarhållningsintervallet.

Konfigurera och testa Azure Storage-kontot:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container using the context
New-AzStorageContainer -Name $container -Context $account.Context

# Get a container
$container = Get-AzStorageContainer -Name $container -Context $account.Context

# List containers
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Ange och rensa juridiska undantag:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Skapa eller uppdatera oföränderlighets-principer:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Hämta oföränderlighets-principer:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Lås oföränderlighets-principer (tilläggs-Force för att stänga prompten):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Utöka oföränderlighets-principer:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Ta bort en olåst oföränderlighets-princip (Lägg till tvång för att stänga meddelandet):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
