---
title: Azure hanterade program i Marketplace | Microsoft Docs
description: "Beskriver Azure hanterade program som är tillgängliga via Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Azure hanterade program i Marketplace

 MSPs ISV: er och systemintegrerare (SIs) kan använda Azure hanterade program att erbjuda sina lösningar för alla Azure Marketplace-kunder. Sådana lösningar minska underhållet och underhåll kostnader för kunder. Utgivare kan sälja infrastruktur- och programvara på marknadsplatsen. De kan koppla tjänster och operativa stöd till hanterade program. Mer information finns i [hanteras Programöversikt](managed-application-overview.md).

Den här artikeln beskrivs hur en MSP, ISV eller SI kan publicera ett program till Marketplace och göra den tillgänglig för kunder.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Krav för att publicera ett hanterat program

Förutsättningar för att lista i Marketplace:

* Tekniska

    *  Information om grundläggande struktur och syntaxen för Azure Resource Manager-mallar finns [Azure Resource Manager-mallar](resource-group-authoring-templates.md).
    *  Om du vill visa fullständig mallen lösningar finns [Azure Quickstart mallar](https://azure.microsoft.com/en-us/documentation/templates/) eller [Quickstart mallen databasen](https://github.com/azure/azure-quickstart-templates).
    *  Information om hur du skapar gränssnittet för kunder som distribuerar ditt program via Marketplace finns [skapa en fil för användaren gränssnittsdefinition](managed-application-createuidefinition-overview.md).

* Sökassistent (affärsbehov)

    *   Ditt företag eller dess dotterbolag måste finnas i ett land där försäljning stöds av Marketplace.
    *   Produkten måste ha licens på ett sätt som är kompatibel med fakturering modeller som stöds av Marketplace.
    *   Du är ansvarig för teknisk support för kunder på ett kommersiellt rimliga sätt. Stöd kan vara fria, betald, eller via community stöd.
    *   Du är ansvarig för att licensiera programmet och eventuella beroenden för programvara från tredje part.
    *   Du måste ange innehåll som uppfyller villkoren för ditt erbjudande ska visas i Marketplace och i Azure-portalen.
    *   Du måste acceptera villkoren i avtalet för utgivaren och Azure Marketplace deltagande principer.
    *   Du måste acceptera att följa den användningsvillkoren och sekretesspolicyn för Microsoft certifierade programavtalet för Microsoft Azure.

## <a name="create-a-new-azure-application-offer"></a>Skapa ett nytt erbjudande för Azure-program

Efter att du uppfyller kraven som är du redo att skapa erbjudandet hanterade program. Låt oss ta en snabb överblick över ett erbjudande och en SKU.

### <a name="offer"></a>Erbjudande

Erbjudande för ett hanterat program motsvarar en klass av produkten erbjudande från en utgivare. Om du har en ny typ av lösning/program som du vill ska vara tillgängliga i Marketplace kan konfigurera du den som ett nytt erbjudande. Ett erbjudande är en samling av SKU: er. Varje erbjudande visas som sin egen enhet i Marketplace.

### <a name="sku"></a>SKU

En SKU är den minsta köpbara enheten ett erbjudande. Du kan använda en SKU inom samma produkten klass (erbjudandet) för att skilja mellan:

* Olika funktioner som stöds.
* Om erbjudandet är hanterad eller ohanterad.
* Fakturering modeller som stöds.

En SKU visas under överordnade erbjudandet på Marketplace. Det verkar som sin egen köpbara enhet i Azure-portalen.

### <a name="set-up-an-offer"></a>Konfigurera ett erbjudande

1. Logga in på den [molnpartnerportalen](https://cloudpartner.azure.com/).

2. I navigeringsfönstret till vänster, Välj **+ nytt erbjudande** > **Azure program**.

    ![Nytt erbjudande](./media/managed-application-author-marketplace/newOffer.png)

3. Fylla i formulär som visas till vänster i den **Editor** vyn. Obligatoriska fält är markerade med en röd asterisk (*).

    ![Inställningar för erbjudande](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    Fyra huvudsakliga formulär används för att skapa ett hanterat program:

    a. Inställningar för erbjudande

    b. SKU: er

    c. Marketplace

    d. Support

Formulären beskrivs mer detaljerat i följande avsnitt.

## <a name="offer-settings-form"></a>Erbjudande inställningar för formulär
Använd den här grundläggande formuläret för att ange inställningar för erbjudande.

1. Fyll i den **erbjuder inställningar** formuläret. Olika områden är:

    a. **Erbjudande-ID**: den unika identifieraren identifierar erbjudandet i en profil för utgivaren. Detta ID visas i produkten URL: er, Resource Manager-mallar och fakturering rapporter. Det kan bara består av gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett streck. Det är begränsat till högst 50 tecken. När ett erbjudande lanseras är i det här fältet låst.

    b. **Publicerings-ID**: Använd den här listrutan för att välja publisher-profil som du vill publicera det här erbjudandet under. När ett erbjudande lanseras är i det här fältet låst.

    c. **Namnet**: det här visningsnamnet för ditt erbjudande visas i Marketplace och i portalen. Det kan ha högst 50 tecken. Innehåller ett okänt varumärke för produkten. Inkludera inte ditt företagsnamn, om det inte är hur släpps. Om du marknadsföring erbjudandet på din egen webbplats, måste du kontrollera att namnet är exakt hur den visas på webbplatsen.

2. Välj **spara** att spara ditt arbete. 

## <a name="skus-form"></a>SKU: er formulär
Nästa steg är att lägga till SKU: er för ditt erbjudande.

1. Välj **SKU: er** > **nya SKU**. 

    ![Välj ny SKU](./media/managed-application-author-marketplace/newOffer_skus.png)

2. Ange en **SKU ID**. En SKU-ID är en unik identifierare för SKU: N i ett erbjudande. Detta ID visas i produkten URL: er, Resource Manager-mallar och fakturering rapporter. Det kan bara består av gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett streck och är begränsat till högst 50 tecken. När ett erbjudande lanseras är i det här fältet låst. Du kan ha flera SKU: er i ett erbjudande. Du behöver en SKU för varje avbildning som du planerar att publicera.

3. Fyll i den **SKU information** avsnitt i följande format:

    ![Ange nya SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    Fylla i följande fält:
    
    a. **Rubrik**: Ange en rubrik för denna SKU. Den här rubriken visas i galleriet för det här objektet.

    b. **Översikt över**: Ange en kort sammanfattning för denna SKU. Den här texten visas under rubriken.

    c. **Beskrivning**: Ange en detaljerad beskrivning av SKU: N.

    d. **SKU-typen**: de tillåtna värdena är **hanterat program** och **Lösningsmallar**. Det här fallet markerar **hanterat program**.

4. Fyll i den **Paketinformation** avsnitt i följande format:

    ![Paket](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    Fylla i följande fält:

    a. **Aktuell Version**: Ange en version för det paket som du överför. Det bör vara i formatet `{number}.{number}.{number}{number}`.

    b. **Välj en paketfil**: det här paketet innehåller följande filer som är komprimerade till en ZIP-fil:
    * **applianceMainTemplate.json**: distribution mallfilen som används för att distribuera lösningen/application. Information om hur du skapar distributionen mallfilerna finns [skapa din första Azure Resource Manager-mallen](resource-manager-create-first-template.md).
    * **appliancecreateUIDefinition.json**: den här filen används av Azure-portalen för att generera användargränssnittet som används för att etablera den här lösningen/application. Mer information finns i [Kom igång med CreateUiDefinition](managed-application-createuidefinition-overview.md).
    * **mainTemplate.json**: den här mallen innehåller Microsoft.Solution/appliances resursen. Filen mainTemplate innehåller följande egenskaper:

        *  **typ**: Använd **Marketplace** för hanterade program på Marketplace.
        *  **ManagedResourceGroupId**: den här resursgruppen i kundens prenumeration är där alla resurser som definierats i applianceMainTemplate.json har distribuerats.
        *  **PublisherPackageId**: denna sträng som unikt identifierar paketet. Ange ett värde i formatet `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.

Hämta den **erbjuder ID** och **Publicerings-ID** från publishing portal som visas i följande bild:

![Erbjudande-ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
Hämta den **SKU ID**, enligt följande bild:

![SKU-ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
Hämta paketet **Version**, enligt följande bild:

![Paketversion](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  Baserat på i föregående exempel kan värdet för **PublisherPackageId** är `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`.

  Exempel på mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

Det här paketet innehåller alla andra kapslade mallar eller skript som krävs för att kunna etablera det här programmet. MainTemplate.json, applianceMainTemplate.json och applianceCreateUIDefinition.json filer måste finnas i rotmappen.

* **Tillstånd**: den här egenskapen definierar vilka som får åtkomst och vilken åtkomstnivå till resurserna i kundernas prenumerationer. Utgivaren kan använda den för att hantera program för kundens räkning.
* **PrincipalId**: den här egenskapen är Azure Active Directory (Azure AD)-ID för en användare, grupp eller ett program som har beviljats vissa behörigheter för resurser i kundens prenumeration. Rolldefinitionen beskriver behörigheten. 
* **Rolldefinitionen**: den här egenskapen är en lista med alla inbyggda rollbaserad åtkomstkontroll (RBAC) roller tillhandahålls av Azure AD. Du kan välja den roll som är mest lämpligt att använda för att hantera resurserna för kundens räkning.

Du kan lägga till flera tillstånd. Vi rekommenderar att du skapar en grupp i AD-användare och ange dess ID i **PrincipalId**. På så sätt kan du lägga till fler användare i användargruppen utan att behöva uppdatera SKU: N.

Läs mer om hur RBAC [komma igång med RBAC på Azure portal](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Marketplace-formulär

Marketplace-formulär uppmanar för fält som visas på den [Azure Marketplace](https://azuremarketplace.microsoft.com) och på den [Azure-portalen](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Prenumerations-ID: N för förhandsgranskning

Ange en lista över Azure-prenumeration ID: N som kan komma åt erbjudandet när den har publicerats. Du kan använda dessa vitt visas prenumerationer för att testa förhandsgranskade erbjudandet innan du gör den live. Du kan sammanställa en lista för tillåten av upp till 100 prenumerationer i partnerportalen.

### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till fem kategorier i listan som erbjudandet bäst kan associeras med. Dessa kategorier som används för att mappa erbjudandet till produktkategorier som är tillgängliga i den [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Sammanfattning av det hanterade programmet visar följande fält:

![Marketplace-sammanfattning](./media/managed-application-author-marketplace/publishvm10.png)

Den **översikt över** för det hanterade programmet visar följande fält:

![Marketplace-översikt](./media/managed-application-author-marketplace/publishvm11.png)

Den **planer + priser** för det hanterade programmet visar följande fält:

![Marketplace-planer](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

Sammanfattning av det hanterade programmet visar följande fält:

![Översikt över Portal](./media/managed-application-author-marketplace/publishvm12.png)

Översikt för det hanterade programmet visar följande fält:

![Portalen översikt](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Logotypen riktlinjer

Följ dessa riktlinjer för en logotyp som du överför i molnpartnerportalen:

*   Azure designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i din logotyp.
*   Färger med portalen är vit och svart. Använd inte färgerna som bakgrundsfärg för din logotyp. Använd en färg som gör din logotyp framträdande i portalen. Vi rekommenderar enkla primära färger. *Om du använder en genomskinlig bakgrund, se till att logotyp och texten inte vitt, svart eller blå.*
*   Använd inte en toning bakgrund på logotypen.
*   Placera inte text på logotypen, inte ens företaget eller varumärke. Utseendet och känslan logotypens bör platt och undvika toningar.
*   Kontrollera att logotypen inte har sträckts ut.

#### <a name="hero-logo"></a>Hjälte-logotyp

Logotypen hjälte är valfritt. Utgivaren kan du inte överföra en hjälte logotyp. När ikonen hjälte har överförts kan inte tas bort. Partnern måste följa Marketplace riktlinjer för hjälte ikoner som helst.

Följ dessa riktlinjer för ikonen hjälte logotyp:

*   Visningsnamn för utgivaren, plan rubrik och erbjudandet lång sammanfattning visas i vitt. Därför inte använda en ljusare ikonen hjälte bakgrunden. En svart, vit eller genomskinlig bakgrund är inte tillåten för hjälte ikoner.
*   När erbjudandet visas utgivaren visar namn, planera rubrik, erbjudandet lång sammanfattning och **skapa** knappen inbäddad programmässigt i hjälte logotypen. Därför inte ange valfri text när du utformar hjälte logotypen. Lämna tomt utrymme till höger eftersom texten programmässigt ingår i detta utrymme. Det tomma utrymmet för texten som ska vara 415 x 100 bildpunkter till höger. Det är förskjutning med 370 bildpunkter från vänster.

    ![Hjälte logotypen exempel](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Stöd för formulär

Fyll i den **stöder** formuläret med stöd för kontakter från ditt företag. Den här informationen kan vara engineering customer support kontakter.

## <a name="publish-an-offer"></a>Publicera ett erbjudande

När du fyller i alla avsnitt markerar **publicera** att starta processen som gör erbjudandet tillgängliga för kunder.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [hanteras Programöversikt](managed-application-overview.md).
* Information om hur du använder ett hanterat program från Marketplace finns [använda Azure hanterade program i Marketplace](managed-application-consume-marketplace.md).
* Information om hur du publicerar ett program för Tjänstkatalog hanteras finns [skapa och publicera en applikation för Tjänstkatalog hanteras](managed-application-publishing.md).
* Information om att använda ett Tjänstkatalogen hanterade program, se [använder ett Tjänstkatalogen hanterade program](managed-application-consumption.md).
