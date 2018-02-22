---
title: Skapa och publicera en Marketplace-objektet i Azure-stacken | Microsoft Docs
description: Skapa och publicera en Marketplace-objektet i Azure-stacken.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5ac91dac3cb446abaf07492d8b6ec8aa0c120ef4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Skapa och publicera ett Marketplace-objekt

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="create-a-marketplace-item"></a>Skapa ett Marketplace-objekt
1. [Hämta](http://www.aka.ms/azurestackmarketplaceitem) verktyget Azure-galleriet Paketeraren och exempel Azure Stack Marketplace-objektet.
2. Öppna exempel Marketplace-objektet och Byt namn på den **SimpleVMTemplate** mapp. (Till exempel använda samma namn som din Marketplace-objekt – **Contoso.TodoList**.) Den här mappen innehåller:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Skapa en Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) eller välj en mall från GitHub. Marketplace-objektet använder den här mallen för att skapa en resurs.
4. Kontrollera att resursen kan distribueras genom att testa mallen med Microsoft Azure Stack-API: er.
5. Om din mall är beroende av en avbildning av virtuell dator, följ instruktionerna för att [lägga till en avbildning av virtuell dator i Azure stapel](azure-stack-add-vm-image.md).
6. Spara mallen Azure Resource Manager i den **/Contoso.TodoList/DeploymentTemplates/** mapp.
7. Välj ikoner och text för Marketplace-objektet. Lägga till ikoner till den **ikoner** mapp, och lägga till text i den **resurser** filen i den **strängar** mapp. Använd liten, Medium, Large och Wide Namngivningskonventionen för ikoner. Se [Marketplace-objektet gränssnittsreferens](#reference-marketplace-item-ui) en detaljerad beskrivning.
   
   > [!NOTE]
   > Alla fyra ikonstorlekar (small, medium, stora bred) krävs för att skapa Marketplace-objektet på rätt sätt.
   > 
   > 
8. I den **manifest.json** fil, ändra **namn** till namnet på din Marketplace-objektet. Ändra **publisher** till ditt namn eller företag.
9. Under **artefakter**, ändra **namn** och **sökväg** Azure Resource Manager-mallen som du med rätt information.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Ersätt **Mina Marketplace-objekt** med en lista över kategorier där Marketplace-objektet ska visas.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. För ytterligare redigeringar manifest.json, referera till [referens: Marketplace objektet manifest.json](#reference-marketplace-item-manifestjson).
12. För att paketera mappar till en .azpkg-fil, öppna en kommandotolk och kör följande kommando:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Den fullständiga sökvägen till paketet utdata måste finnas. Om sökvägen för utdata är C:\MarketPlaceItem\yourpackage.azpkg, finnas exempelvis mappen C:\MarketPlaceItem.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publicera ett Marketplace-objekt
1. Använd PowerShell eller Azure Lagringsutforskaren för att överföra din Marketplace-objektet (.azpkg) till Azure Blob storage. Du kan överföra till lokal lagring i Azure Stack eller överför till Azure Storage. (Det är en tillfällig plats för paketet.) Kontrollera att blob är allmänt tillgänglig.
2. Kontrollera att PowerShell-sessionen har ställts in med dina administratörsautentiseringsuppgifter för tjänsten på den virtuella klientdatorn i Microsoft Azure Stack-miljö. Du hittar anvisningar för hur du autentiserar PowerShell i Azure-stacken i [distribuera en mall med PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Använd den **Lägg till AzureRMGalleryItem** PowerShell-cmdlet för att publicera Marketplace-objektet till Azure-stacken. Exempel:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parameter | Beskrivning |
   | --- | --- |
   | Prenumerations-ID |Admin prenumerations-ID. Du kan hämta den med hjälp av PowerShell. Om du föredrar att hämta i portalen går du till providern prenumerationen och kopiera prenumerations-ID. |
   | GalleryItemUri |BLOB-URI för gallery-paket som redan har överförts till lagring. |
   | Apiversion |Ange som **2015-04-01**. |
4. Gå till portalen. Du kan nu se Marketplace-objektet i portalen--som operatör eller som en användare.
   
   > [!NOTE]
   > Paketet kan ta flera minuter innan den visas.
   > 
   > 
5. Marketplace-objektet har nu sparats på Azure Marketplace för stacken. Du kan välja att ta bort den från din lagringsplats för Blob.
6. Du kan ta bort en Marketplace-objektet med hjälp av den **ta bort AzureRMGalleryItem** cmdlet. Exempel:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > Marketplace-Gränssnittet kan indikera ett fel när du tar bort ett objekt. Åtgärda felet genom att klicka på **inställningar** i portalen. Markera **ignorera ändringar** under **Portal anpassning**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Referens: Marketplace objektet manifest.json
### <a name="identity-information"></a>Identitetsinformation
| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| Namn |X |Sträng |[A-Za-z0-9]+ | |
| Utgivare |X |Sträng |[A-Za-z0-9]+ | |
| Version |X |Sträng |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadata
| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| Visningsnamn |X |Sträng |Rekommendation av 80 tecken |Portalen kanske inte visar dina objektnamnet avslutas om det är längre än 80 tecken. |
| PublisherDisplayName |X |Sträng |Rekommendation 30 tecken |Portalen kanske inte visar utgivarens namn utan problem om det är längre än 30 tecken. |
| PublisherLegalName |X |Sträng |Maximalt 256 tecken | |
| Sammanfattning |X |Sträng |60 och 100 tecken | |
| LongSummary |X |Sträng |140 256 tecken |Inte ännu är tillämpliga i Azure-stacken. |
| Beskrivning |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 till 5 000 tecken | |

### <a name="images"></a>Avbildningar
Marketplace använder följande ikoner:

| Namn | Bredd | Höjd | Anteckningar |
| --- | --- | --- | --- |
| Wide |255 px |115 px |Krävs alltid |
| Stor |115 px |115 px |Krävs alltid |
| Medel |90 px |90 px |Krävs alltid |
| Liten |40 px |40 px |Krävs alltid |
| Skärmbild |533 px |32 bildpunkter |Valfri |

### <a name="categories"></a>Kategorier
Varje Marketplace-objektet ska vara taggade med en kategori som identifierar där objektet visas på portalens användargränssnitt. Du kan välja något av de befintliga kategorierna i Azure-stacken (beräkning, Data + lagring, etc.) eller välj en ny.

### <a name="links"></a>Länkar
Varje Marketplace-objekt kan innehålla olika länkar till ytterligare innehåll. Länkarna har angetts som en lista med namn och URI: er.

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| Visningsnamn |X |Sträng |Högst 64 tecken | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Ytterligare egenskaper
Utöver föregående metadata innehåller Marketplace författare anpassade nyckel/värde-par data i följande format:

| Namn | Krävs | Typ | Villkor | Beskrivning |
| --- | --- | --- | --- | --- |
| Visningsnamn |X |Sträng |Högst 25 tecken | |
| Värde |X |Sträng |Maximalt 30 tecken | |

### <a name="html-sanitization"></a>HTML-rensning
För alla fält som gör att HTML tillåts följande element och attribut:

H1, h2, h3, h4, h5, p, ol, ul, li, en [target | href], br, starkt, em, b, jag

## <a name="reference-marketplace-item-ui"></a>-Referens: UI Marketplace-objektet
Ikoner och text för Marketplace-objekt som visas i Azure Stack-portalen är som följer.

### <a name="create-blade"></a>Bladet Skapa
![Bladet Skapa](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Marketplace-objektet informationsbladet
![Marketplace-objektet informationsbladet](media/azure-stack-marketplace-item-ui-reference/image3.png)

