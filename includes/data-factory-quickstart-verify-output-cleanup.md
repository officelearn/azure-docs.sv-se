---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: c0fcdf1cf69c55f63288138bc7377a78295cb2be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "86544840"
---
## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Pipelinen skapar automatiskt utdatamappen i blobcontainern adftutorial. Filen emp.txt kopieras från indatamappen till utdatamappen. 

1. I Azure Portal på sidan behållare för **adftutorial** väljer du **Uppdatera** för att visa mappen utdata. 
    
    ![Uppdatera](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Välj **utdata** i mapplistan. 

3. Kontrollera att **emp.txt** har kopierats till utdatamappen. 

    ![Uppdatera](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan rensa de resurser som du skapade i snabbstarten på två sätt. Du kan ta bort den [Azure-resursgrupp](../articles/azure-resource-manager/management/overview.md) som innehåller alla resurser i resursgruppen. Om du vill behålla de andra resurserna intakta ska du bara ta bort den datafabrik du har skapat i den här självstudiekursen.

När du tar bort en resursgrupp tas alla resurser inklusive datafabriker i den bort. Kör följande kommando om du vill ta bort hela resursgruppen: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Det kan ta en stund att släppa en resurs grupp. Ha tålamod under processen

Om du bara vill ta bort datafabriken och inte hela resursgruppen kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```