---
title: Resurs modell för Azure Service Fabric-program | Microsoft Docs
description: Den här artikeln innehåller en översikt över att hantera ett Azure Service Fabric-program med Azure Resource Manager
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: dcffc1ba783b49343bf3380b62c3d4085f5aa347
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390084"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Vad är Service Fabric-programmets resurs modell?
Vi rekommenderar att Service Fabric-program distribueras till ditt Service Fabric-kluster via Azure Resource Manager. Den här metoden gör det möjligt att beskriva program och tjänster i JSON och distribuera dem i samma Resource Manager-mall som klustret. I stället för att distribuera och hantera program via PowerShell eller Azure CLI behöver du inte vänta tills klustret är klart. Programregistrering, etablering och distribution sker i ett enda steg. Detta är den bästa metoden för att hantera programmets livscykel i ditt kluster. Mer information finns i [metod tips](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

Vid behov hanterar du dina program som Resource Manager-resurser för att förbättra:
* Gransknings logg: resurs hanteraren granskar varje åtgärd och bevarar en detaljerad *aktivitets logg* som kan hjälpa dig att spåra ändringar som gjorts i dessa program och klustret.
* Rollbaserad åtkomst kontroll: hantering av åtkomst till kluster och program som distribueras i klustret kan göras via samma Resource Manager-mall.
* Azure Resource Manager (via Azure Portal) blir en One-avsluta-shoppa för hantering av kluster och viktiga program distributioner.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Service Fabric programmets livs cykel med Azure Resource Manager 
I det här dokumentet får du lära dig att:

> [!div class="checklist"]
> * Distribuera program resurser med Azure Resource Manager 
> * Uppgradera program resurser med Azure Resource Manager
> * Ta bort program resurser

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Distribuera program resurser med Azure Resource Manager  
Om du vill distribuera ett program och dess tjänster med hjälp av Azure Resource Manager-programmets resurs modell måste du paketera program kod, ladda upp paketet och sedan referera till paketets placering i en Azure Resource Manager mall som ett program klusterresursen. Om du vill ha mer information kan du Visa [Paketera ett program](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).
          
Skapa sedan en Azure Resource Manager mall, uppdatera parameter filen med programinformation och distribuera den på Service Fabric klustret. Se exemplen [här](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto 
Distribution av ett program från en Resource Manager-mall kräver ett lagrings konto för att mellanlagra program avbildningen. Du kan använda ett befintligt lagrings konto på nytt eller skapa ett nytt lagrings konto för att mellanlagra dina program. Om du vill använda ett befintligt lagrings konto kan du hoppa över det här steget. 

![skapar ett lagringskonto][CreateStorageAccount]

### <a name="configure-storage-account"></a>Konfigurera lagrings konto 
När lagrings kontot har skapats måste du skapa en BLOB-behållare där programmen kan mellanlagras. I Azure Portal navigerar du till det lagrings konto som du vill lagra dina program i. Välj bladet **blobbar** och klicka på knappen **Lägg till behållare** . Lägg till en ny behållare med offentlig åtkomst nivå för BLOB.
   
![Skapa BLOB][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Mellanlagra program i ett lagrings konto
Innan programmet kan distribueras måste det mellanlagras i Blob Storage. I den här självstudien kommer vi att skapa programpaketet manuellt, men det här steget kan automatiseras.  Om du vill ha mer information kan du Visa [Paketera ett program](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). I följande steg ska [röstnings exempel programmet](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) användas.

1. I Visual Studio högerklickar du på röstnings projektet och väljer paket.   
![Paket program][PackageApplication]  
2. Öppna **.\Service-Fabric-dotNet-quickstart\Voting\pkg\Debug** -katalogen som precis skapade och skicka innehållet till en fil som heter **röstning. zip** , till exempel att ApplicationManifest. xml finns i roten för zip-filen.  
![Zip-program @ no__t-1  
3. Byt namn på fil namns tillägget från. zip till **. sfpkg**.
4. I Azure Portal, i behållaren **appar** för ditt lagrings konto, klickar du på **överför** och överför **röstning. sfpkg**.  
![Upload app-paket @ no__t-1

Programmet har nu mellanlagrats. Nu är det dags att skapa Azure Resource Manager-mallen för att distribuera programmet.      
   
### <a name="create-the-azure-resource-manager-template"></a>Skapa Azure Resource Manager-mallen
Exempel programmet innehåller [Azure Resource Manager mallar](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) som kan användas för att distribuera programmet. Mallfilerna heter **UserApp. JSON** och **UserApp. Parameters. JSON**. 

> [!NOTE] 
> Filen **UserApp. Parameters. JSON** måste uppdateras med namnet på klustret.
>
>

| Parameter              | Beskrivning                                 | Exempel                                                      | Kommentarer                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Namnet på det kluster som du distribuerar till | SF-cluster123                                                |                                                              |
| Applicering            | Namnet på programmet                 | Förfarande                                                       |
| applicationTypeName    | Typ namnet för programmet           | VotingType                                                   | Måste matcha vad som finns i ApplicationManifest. XML                 |
| ApplicationTypeVersion | Versionen av program typen         | 1.0.0                                                        | Måste matcha vad som finns i ApplicationManifest. XML                 |
| ServiceName            | Namnet på tjänsten som tjänsten         | Röstning ~ VotingWeb                                             | Måste vara i formatet ApplicationName ~ ServiceType            |
| serviceTypeName        | Typ namnet för tjänsten                | VotingWeb                                                    | Måste matcha det som finns i ServiceManifest. XML                 |
| appPackageUrl          | Blob Storage-URL: en för programmet     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | URL: en för programpaketet i Blob Storage (proceduren för att ange detta beskrivs nedan) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Distribuera programmet 
Distribuera programmet genom att köra New-AzResourceGroupDeployment för att distribuera till resurs gruppen som innehåller klustret.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Uppgradera Service Fabric program med Azure Resource Manager
Program som redan har distribuerats till ett Service Fabric kluster kommer att uppgraderas av följande orsaker:

1. En ny tjänst läggs till i programmet. En tjänst definition måste läggas till i filen service-manifest. xml och Application-manifest. xml. Om du sedan vill visa en ny version av programmet måste du uppdatera program typ versionen från 1.0.0 till 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. En ny version av en befintlig tjänst läggs till i programmet. Detta omfattar program kod ändringar och uppdateringar av app Type-version och-namn.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Ta bort program resurser
Program som distribueras med hjälp av program resurs modellen i Azure Resource Manager kan tas bort från klustret med hjälp av nedanstående steg

1) Hämta resurs-ID för program med kommandot [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)  

    #### <a name="get-resource-id-for-application"></a>Hämta resurs-ID för program
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) Ta bort program resurserna med hjälp av [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Ta bort program resursen med dess resurs-ID
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Nästa steg
Hämta information om program resurs modellen:

* [Modellera ett program i Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric program-och tjänst manifest](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Se även
* [Bästa praxis](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Hantera program och tjänster som Azure-resurser](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
