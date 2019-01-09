---
title: Skapa och publicera ett Marketplace-objekt i Azure Stack | Microsoft Docs
description: Skapa och publicera ett Marketplace-objekt i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.openlocfilehash: b2520864589558a530f4f9dbfed83a9f3b8c727a
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104105"
---
# <a name="create-and-publish-a-marketplace-item"></a>Skapa och publicera ett Marketplace-objekt

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="create-a-marketplace-item"></a>Skapa ett Marketplace-objekt

1. Ladda ned den [Azure-galleriet Packager verktyget](https://www.aka.ms/azurestackmarketplaceitem) och exemplet Azure Stack Marketplace-objekt.
2. Öppna exemplet Marketplace-objekt och Byt namn på den **SimpleVMTemplate** mapp. Använd samma namn som din Marketplace-objekt; till exempel **Contoso.TodoList**. Den här mappen innehåller:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Skapa en Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) eller välj en mall från GitHub. Marketplace-objekt använder den här mallen för att skapa en resurs.

    > [!Note]  
    > Aldrig hårt koda alla hemligheter som produktnycklar, lösenord eller kund identifierbar information i Azure Resource Manager-mallen. Mall för JSON-filerna är tillgängliga utan att använda autentisering när publicerat i galleriet. Store alla hemligheter i [Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) och anropa dem från i mallen.

4. Testa mallen med Microsoft Azure Stack-API: er för att kontrollera att resursen kan distribueras.
5. Om din mall är beroende av en avbildning av virtuell dator, följer du anvisningarna för att [lägga till en avbildning av virtuell dator i Azure Stack](azure-stack-add-vm-image.md).
6. Spara din Azure Resource Manager-mall i den **/Contoso.TodoList/DeploymentTemplates/** mapp.
7. Välj ikoner och text för Marketplace-objekt. Lägg till ikoner i den **ikoner** mapp, och Lägg till text till den **resurser** fil i den **strängar** mapp. Använd den **små**, **medel**, **stora**, och **wide** namnkonventionen för ikoner. Se den [Marketplace-objekt gränssnittsreferens](#reference-marketplace-item-ui) för en detaljerad beskrivning av dessa storlekar.

   > [!NOTE]
   > Alla fyra ikonstorlekar (liten, medel, stor eller hela) krävs för att skapa Marketplace-objekt på rätt sätt.
   >
   >
8. I den **Manifest.json** filen, ändra **namn** till namnet på din Marketplace-objekt. Även ändra **publisher** till ditt namn eller organisation.
9. Under **artefakter**, ändra **namn** och **sökväg** till rätt information för Azure Resource Manager-mall som du har lagt till:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Ersätt **Mina Marketplace-objekt** med en lista med de kategorier som var din Marketplace-objekt ska visas:

   ```json
   "categories":[
   "My Marketplace Items"
   ],
   ```

11. Alla ytterligare ändringar till Manifest.json finns [referens: Marketplace-objekt manifest.json](#reference-marketplace-item-manifestjson).

12. Om du vill paketera mapparna i en .azpkg-fil, öppna en kommandotolk och kör följande kommando:

   ```shell
   AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
   ```

    > [!NOTE]
    > Den fullständiga sökvägen till utdata-paketet måste finnas. Exempelvis om sökvägen för utdata är C:\MarketPlaceItem\yourpackage.azpkg, måste mappen C:\MarketPlaceItem finnas.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publicera ett Marketplace-objekt

1. Använda PowerShell eller Azure Storage Explorer för att överföra dina Marketplace-objekt (.azpkg) till Azure Blob storage. Du kan ladda upp till lokal lagring i Azure Stack eller överför till Azure-lagring. vilket är en tillfällig plats för paketet. Kontrollera att bloben är allmänt tillgänglig.
2. Kontrollera att din PowerShell-session har ställts in med dina autentiseringsuppgifter för tjänsten på den virtuella klientdatorn i Microsoft Azure Stack-miljön. Du hittar anvisningar om hur du autentiserar PowerShell i Azure Stack i [distribuera en mall med PowerShell](user/azure-stack-deploy-template-powershell.md).
3. När du använder [PowerShell 1.3.0](azure-stack-powershell-install.md) eller senare, kan du använda den **Lägg till AzsGalleryItem** PowerShell-cmdlet för att publicera Marketplace-objekt till Azure Stack. Innan du använder PowerShell 1.3.0, använder du cmdlet **Lägg till AzureRMGalleryitem** i stället för **Lägg till AzsGalleryItem**. Till exempel när du använder PowerShell 1.3.0 eller senare:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   ```

   | Parameter | Beskrivning |
   | --- | --- |
   | prenumerations-ID |Administratören prenumerations-ID. Du kan hämta den med hjälp av PowerShell. Om du vill hämta den i portalen, gå till providerprenumeration och kopiera prenumerations-ID. |
   | GalleryItemUri |BLOB-URI för din gallery-paket som har redan överförts till lagring. |
   | Apiversion |Ange som **2015-04-01**. |

4. Gå till portalen. Du kan nu se Marketplace-objekt i portalen som en operatör eller som en användare. Paketet kan ta flera minuter innan den visas.

5. Marketplace-objekt sparades nu på Azure Stack Marketplace. Du kan välja att ta bort den från din Blob storage-plats.
    > [!Caution]  
    > Alla standard-galleriet artefakter och artefakterna anpassat galleri är nu tillgängliga utan autentisering under följande webbadresser:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Du kan ta bort ett Marketplace-objekt med hjälp av den **Remove-AzureRMGalleryItem** cmdlet. Exempel:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   ```

   > [!NOTE]
   > Marketplace-Användargränssnittet kan indikera ett fel när du har tagit bort ett objekt. Åtgärda felet genom att klicka på **inställningar** i portalen. Välj **ta bort ändringar** under **Portal anpassning**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Referens: Marketplace-objekt manifest.json

### <a name="identity-information"></a>ID-information

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| Namn |X |Sträng |[A-Za-z0-9]+ | |
| Utgivare |X |Sträng |[A-Za-z0-9]+ | |
| Version |X |Sträng |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadata

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sträng |Rekommendation på 80 tecken |Portalen kanske inte visar dina objektnamn utan problem om det är mer än 80 tecken. |
| PublisherDisplayName |X |Sträng |Rekommendationen 30 tecken |Portalen kanske inte visar utgivarens namn utan problem om det är längre än 30 tecken. |
| PublisherLegalName |X |Sträng |Maximalt 256 tecken | |
| Sammanfattning |X |Sträng |60 och 100 tecken | |
| Underordnat LongSummary |X |Sträng |140 till 256 tecken |Inte ännu är tillämpliga i Azure Stack. |
| Beskrivning |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 5 000 tecken | |

### <a name="images"></a>Avbildningar

Marketplace använder följande ikoner:

| Namn | Bredd | Höjd | Anteckningar |
| --- | --- | --- | --- |
| Brett |255 px |115 px |Krävs alltid |
| Stor |115 px |115 px |Krävs alltid |
| Medel |90 bildpunkter |90 bildpunkter |Krävs alltid |
| Liten |40 bildpunkter |40 bildpunkter |Krävs alltid |
| Skärmbild |533 px |32 bildpunkter |Valfri |

### <a name="categories"></a>Kategorier

Varje Marketplace-objekt bör taggas med en kategori som identifierar där objektet visas på portalens användargränssnitt. Du kan välja något av de befintliga kategorierna i Azure Stack (beräkning, Data + lagring, o.s.v.) eller välja ett nytt.

### <a name="links"></a>Länkar

Varje Marketplace-objekt kan innehålla olika länkar till ytterligare innehåll. Länkarna har angetts som en lista med namn och URI: er:

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sträng |Högst 64 tecken | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Ytterligare egenskaper

Utöver föregående metadata, kan Marketplace författare ge anpassad nyckel/värde-par data i följande format:

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| DisplayName |X |Sträng |Högst 25 tecken | |
| Värde |X |Sträng |Maximalt 30 tecken | |

### <a name="html-sanitization"></a>Gemensamt för HTML

För alla fält som gör att HTML, tillåts följande element och attribut:

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referens: Marketplace-objekt UI

Ikoner och text för Marketplace-objekt som visas i Azure Stack-portalen är som följer.

### <a name="create-blade"></a>Bladet Skapa

![Bladet Skapa](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Informationsblad för Marketplace-objekt

![Informationsblad för Marketplace-objekt](media/azure-stack-marketplace-item-ui-reference/image3.png)
