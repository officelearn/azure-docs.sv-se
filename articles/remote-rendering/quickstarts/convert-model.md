---
title: Konvertera en modell
description: Snabbstart som visar konverteringsstegen för en anpassad modell.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b1bf2c365229a418870250a78a733f9870fb5bb0
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892384"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Snabbstart: Konvertera en modell för rendering

I [Snabbstart: Rendera en modell med Unity](render-model.md)lärde du dig hur du använder exempelprojektet Unity för att återge en inbyggd modell. Den här guiden visar hur du konverterar dina egna modeller.

Du lär dig följande:

> [!div class="checklist"]
>
> * Konfigurera ett Azure blob storage-konto för indata och utdata
> * Ladda upp och konvertera en 3D-modell för användning med Azure Remote Rendering
> * Inkludera den konverterade 3D-modellen i ett program för rendering

## <a name="prerequisites"></a>Krav

* Fullständig [snabbstart: Återge en modell med Unity](render-model.md)
* Installera Azure PowerShell [(dokumentation)](https://docs.microsoft.com/powershell/azure/)
  * Öppna ett PowerShell med administratörsrättigheter
  * Köra:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Översikt

Renderaren på servern kan inte arbeta direkt med källmodellformat som FBX eller GLTF. Istället kräver det att modellen är i ett eget binärt format.
Konverteringstjänsten förbrukar modeller från Azure blob-lagring och skriver konverterade modeller tillbaka till en medföljande Azure blob-lagringsbehållare.

Du behöver:

* En Azure-prenumeration
* Ett StorageV2-konto i din prenumeration
* En blob lagringsbehållare för din indatamodell
* En blob lagringsbehållare för dina utdata
* En modell att konvertera, se [exempelmodeller](../samples/sample-model.md)
  * Visa listan över [källformat som stöds](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Om du vill använda exempelkonverteringsskriptet måste du förbereda en indatamapp som innehåller modellen och alla externa beroenden (till exempel externa texturer eller geometri)

## <a name="azure-setup"></a>Installation av Azure

Om du inte har ett konto [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/)ännu, gå till , klicka på gratis konto alternativet, och följ instruktionerna.

När du har ett Azure-konto går du till [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home).

### <a name="storage-account-creation"></a>Skapa lagringskonto

Om du vill skapa blob-lagring behöver du först ett lagringskonto.
För att skapa en, klicka på knappen "Skapa en resurs":

![Azure - lägg till resurs](media/azure-add-a-resource.png)

På den nya skärmen väljer du **Lagring** på vänster sida och sedan **Lagringskonto - blob, fil, tabell, kö** från nästa kolumn:

![Azure - lägga till lagringsutrymme](media/azure-add-storage.png)

Om du klickar på den här knappen visas följande skärm med lagringsegenskaper som du kan fylla i:

![Installationsprogrammet för Azure](media/azure-setup1.png)

Fyll i formuläret på följande sätt:

* Skapa en ny resursgrupp från länken under listrutan och namnge den här **ARR_Tutorial**
* Ange ett unikt namn här för **lagringskontots namn.** **Det här namnet måste vara globalt unikt,** annars kommer det att finnas en uppmaning som informerar dig om att namnet är klart. Inom ramen för denna snabbstart, vi namn det **arrtutorialstorage**. Därför måste du ersätta den med ditt namn för alla förekomster i den här snabbstarten.
* Välj en **plats** nära dig. Använd helst samma plats som används för att ställa in renderingen i den andra snabbstarten.
* **Prestanda** inställd på Standard
* **Kontoslag** inställt på StorageV2 (allmänt ändamål v2)
* **Replikering** inställd på geo redundant lagring av läsåtkomst (RA-GRS)
* **Åtkomstnivå** inställd på Het

Ingen av egenskaperna på andra flikar behöver ändras, så du kan fortsätta med **"Granska + skapa"** och sedan följa stegen för att slutföra installationen.

Webbplatsen informerar dig nu om hur distributionen fortskrider och rapporterar "Din distribution är klar" så småningom. Klicka på knappen **"Gå till resurs"** för nästa steg:

![Skapandet av Azure Storage har slutförts](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Skapa blob-lagring

Därefter behöver vi två blob behållare, en för inmatning och en för utdata.

Från **knappen "Gå till resurs"** ovan kommer du till en sida med en panel till vänster som innehåller en listmeny. I den listan under kategorin **"Blob-tjänst"** klickar du på knappen **"Behållare":**

![Azure - lägg till behållare](./media/azure-add-containers.png)

Tryck på knappen **"+ Container"** för att skapa behållaren för **indatablobblagring.**
Använd följande inställningar när du skapar dem:
  
* Namn = arrinput
* Offentlig åtkomstnivå = Privat

När behållaren har skapats klickar du på **+ Behållare** igen och upprepar med dessa inställningar för **utdatabehållaren:**

* Namn = arroutput
* Offentlig åtkomstnivå = Privat

Du bör nu ha två blob lagringsbehållare:

![Installation av bloblagring](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Kör konverteringen

För att göra det enklare att anropa tjänsten för konvertering av tillgångar tillhandahåller vi ett verktygsskript. Den finns i mappen *Skript* och kallas **Conversion.ps1**.

I synnerhet detta skript

1. laddar upp alla filer i en viss katalog från lokal disk till behållaren för indatalagring
1. anropar [REST-API:et för tillgångskonvertering](../how-tos/conversion/conversion-rest-api.md) som hämtar data från behållaren för indatalagring och startar en konvertering som returnerar ett konverterings-ID
1. avsöka api:et för konverteringsstatus med det hämtade konverterings-ID:t tills konverteringsprocessen avslutas med lyckad eller misslyckad
1. hämtar en länk till den konverterade tillgången i utdatalagringen

Skriptet läser dess konfiguration från filen *Scripts\arrconfig.json*. Öppna JSON-filen i en textredigerare.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

Konfigurationen inom **gruppen accountSettings** (konto-ID och nyckel) ska fyllas i i analogt med autentiseringsuppgifterna i [snabbstarten Rendera en modell med Unity](render-model.md).

I gruppen **assetConversionSettings** kontrollerar du att **resursGroup,** **blobInputContainerName**och **blobOutputContainerName** visas ovan.
Observera att värdet **arrtutorialstorage** måste ersättas med det unika namn som du valde när lagringskontot skapades.

Ändra **localAssetDirectoryPath** så att den pekar på katalogen på disken som innehåller den modell som du tänker konvertera. Var noga med att korrekt fly\\backslashes (" ") i\\\\banan med dubbla omvänt snedstreck (" ").

Alla data från sökvägen som anges i **localAssetDirectoryPath** överförs till **blobInputContainerName-blobbehållaren** under en undersökväg som anges av **indataFolderPath**. Så i exempelkonfigurationen ovanför innehållet i\\katalogen "D: tmp\\robot" laddas upp till blob-behållaren "arrinput" av lagringskontot "arrtutorialstorage" under sökvägen "robotConversion". Redan befintliga filer kommer att skrivas över.

Ändra **indataAssetPath** till sökvägen till den modell som ska konverteras – sökvägen är relativ till localAssetDirectoryPath. Använd "/" i\\stället för " " som banavgränsare. Så för en "robot.fbx" fil som ligger\\direkt\\i "D: tmp robot" använd "robot.fbx".

När modellen har konverterats skrivs den tillbaka till lagringsbehållaren som anges av **blobOutputContainerName**. En undersökstjub kan anges genom att tillhandahålla **valfria utdataFolderPath**. I exemplet ovan kopieras den resulterande "robot.arrAsset" till utdatablobbbehållaren under "konverterad/robot".

Konfigurationsinställningen **outputAssetFileName** bestämmer namnet på den konverterade tillgången - parametern är valfri och utdatafilnamnet kommer att härledas från indatafilnamnet annars. 

Öppna en PowerShell, se till att du har installerat *Azure PowerShell* som nämns i [förutsättningarna](#prerequisites). Logga sedan in i din prenumeration med följande kommando och följ anvisningarna på skärmen:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Om din organisation har mer än en prenumeration kan du behöva ange argumenten SubscriptionId och Tenant. Hitta information i [Dokumentationen för Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

Ändra till `azure-remote-rendering\Scripts` katalogen och kör konverteringsskriptet:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Du bör se ungefär ![så här: Conversion.ps1](./media/successful-conversion.png)

Konverteringsskriptet genererar en *SAS-uri (Shared Access Signature)* för den konverterade modellen. Du kan nu kopiera den här URI:n som **modellnamn** till exempelappen snabbstart (se [Snabbstart: Återge en modell med Unity](render-model.md)).

![Ersätt modell i Unity](./media/replace-model-in-unity.png)

 Provet ska nu läsa in och återge din anpassade modell!

## <a name="optional-re-creating-a-sas-uri"></a>Valfritt: Återskapa en SAS-URI

SAS URI som skapas av konverteringsskriptet är endast giltigt i 24 timmar. Men när den har gått ut behöver du inte konvertera din modell igen. I stället kan du skapa en ny SAS i portalen enligt beskrivningen i nästa steg:

1. Gå till [Azure Portal](https://www.portal.azure.com)
1. Klicka på din **lagringskontoresurs:** ![Signaturåtkomst](./media/portal-storage-accounts.png)
1. På följande skärm klickar du på **Storage Explorer** på den vänstra panelen och hittar utdatamodellen *(.arrAsset-filen)* i lagringsbehållaren *för arroutput-blob.* Högerklicka på filen och välj **Hämta signatur** för delad ![åtkomst på snabbmenyn: Signaturåtkomst](./media/portal-storage-explorer.png)
1. En ny skärm öppnas där du kan välja ett utgångsdatum. Tryck på **Skapa**och kopiera URI:n som visas i nästa dialogruta. Den här nya URI:n ersätter den tillfälliga URI som skriptet skapade.

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna, ta en titt på våra tutorials för att få mer djupgående kunskap.

Om du vill lära dig information om modellkonvertering, kolla in [modellen konvertering REST API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Självstudiekurs: Ställa in ett Unity-projekt från grunden](../tutorials/unity/project-setup.md)
