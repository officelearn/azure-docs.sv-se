---
title: Azure Service Fabric-programresursmodell
description: Den här artikeln innehåller en översikt över hur du hanterar ett Azure Service Fabric-program med hjälp av Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682631"
---
# <a name="service-fabric-application-resource-model"></a>Resursmodell för tjänst fabric-program

Du har flera alternativ för att distribuera Azure Service Fabric-program i ditt Service Fabric-kluster. Vi rekommenderar att du använder Azure Resource Manager. Om du använder Resource Manager kan du beskriva program och tjänster i JSON och sedan distribuera dem i samma Resource Manager-mall som klustret. Till skillnad från att använda PowerShell eller Azure CLI för att distribuera och hantera program behöver du inte vänta på att klustret är klart om du använder Resource Manager. programregistrering, etablering och distribution kan alla ske i ett steg. Att använda Resource Manager är det bästa sättet att hantera programmets livscykel i klustret. Mer information finns i [Metodtips: Infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Genom att hantera dina program som resurser i Resource Manager kan du få förbättringar inom dessa områden:

* Granskningsspårning: Resource Manager granskar varje åtgärd och behåller en detaljerad aktivitetslogg. En aktivitetslogg kan hjälpa dig att spåra alla ändringar som gjorts i programmen och till klustret.
* Rollbaserad åtkomstkontroll: Du kan hantera åtkomst till kluster och program som distribueras i klustret med samma Resource Manager-mall.
* Hanteringseffektivitet: Med Hjälp av Resource Manager får du en enda plats (Azure-portalen) för hantering av kluster- och kritiska programdistributioner.

I det här dokumentet får du lära dig hur du:

> [!div class="checklist"]
>
> * Distribuera programresurser med hjälp av Resource Manager.
> * Uppgradera programresurser med hjälp av Resource Manager.
> * Ta bort programresurser.

## <a name="deploy-application-resources"></a>Distribuera programresurser

De åtgärder på hög nivå som du vidtar för att distribuera ett program och dess tjänster med hjälp av resurshanterarens programresursmodell är:
1. Paketera programkoden.
1. Ladda upp paketet.
1. Referera till paketets plats i en Resource Manager-mall som en programresurs. 

Mer information finns [i Paketera ett program](service-fabric-package-apps.md#create-an-sfpkg).

Sedan skapar du en Resource Manager-mall, uppdaterar parameterfilen med programinformation och distribuerar mallen i Service Fabric-klustret. [Utforska exempel](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Om du vill distribuera ett program från en Resource Manager-mall måste du ha ett lagringskonto. Lagringskontot används för att arrangera programavbildningen. 

Du kan återanvända ett befintligt lagringskonto eller skapa ett nytt lagringskonto för mellanlagring av dina program. Om du använder ett befintligt lagringskonto kan du hoppa över det här steget. 

![skapar ett lagringskonto][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Konfigurera ditt lagringskonto

När lagringskontot har skapats skapar du en blob-behållare där programmen kan mellanlagras. Gå till Azure Storage-kontot där du vill lagra dina program i Azure-portalen. Välj **Blobbar** > **Lägg till behållare**. 

Resurser i klustret kan skyddas genom att ställa in den offentliga åtkomstnivån till **privat**. Du kan bevilja åtkomst på flera sätt:

* Auktorisera åtkomst till blobbar och köer med hjälp av [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Bevilja åtkomst till Azure-blob och ködata med hjälp av [RBAC i Azure-portalen](../storage/common/storage-auth-aad-rbac-portal.md).
* Delegera åtkomst med hjälp av en [signatur för delad åtkomst](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

I exemplet i följande skärmbild används anonym läsåtkomst för blobbar.

![Skapa blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Arrangera programmet i ditt lagringskonto

Innan du kan distribuera ett program måste du iscensätta programmet i blob-lagring. I den här självstudien skapar vi programpaketet manuellt. Tänk på att det här steget kan automatiseras. Mer information finns i [Paketera ett program](service-fabric-package-apps.md#create-an-sfpkg). 

I den här [självstudien](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)använder vi exempelprogrammet Röstning .

1. Högerklicka på **röstningsprojektet** i Visual Studio och välj sedan **Paket**.

   ![Paketansökan][PackageApplication]  
1. Gå till katalogen *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug.* Zip innehållet i en fil som heter *Voting.zip*. *Filen ApplicationManifest.xml* ska vara roten i zip-filen.

   ![Zip-program][ZipApplication]  
1. Byt namn på filen om du vill ändra tillägget från .zip till *.sfpkg*.

1. I **appbehållaren** för ditt lagringskonto i Azure-portalen väljer du **Ladda upp**och överför sedan **Voting.sfpkg**. 

   ![Ladda upp appaket][UploadAppPkg]

Nu är programmet nu iscensatt och du kan skapa Resource Manager-mallen för att distribuera programmet.

### <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

Exempelprogrammet innehåller [Azure Resource Manager-mallar](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) som du kan använda för att distribuera programmet. Mallfilnamnen är *UserApp.json* och *UserApp.Parameters.json*.

> [!NOTE]
> *Filen UserApp.Parameters.json* måste uppdateras med namnet på klustret.
>
>

| Parameter              | Beskrivning                                 | Exempel                                                      | Kommentarer                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Namnet på det kluster som du distribuerar till | sf-kluster123                                                |                                                              |
| program            | Namnet på programmet                 | Röstning                                                       |
| applicationTypeName (applicationTypeName)    | Programmets typnamn           | VotingType (Röstningstyp)                                                   | Måste matcha ApplicationManifest.xml                 |
| applicationTypeVersion (applicationTypeVersion) | Versionen av programtypen         | 1.0.0                                                        | Måste matcha ApplicationManifest.xml                 |
| Tjänstnamn            | Namnet på tjänsten         | Röstning ~ VotingWeb                                             | Måste vara i formatet ApplicationName~ServiceType            |
| serviceTypeName        | Tjänstens typnamn                | VotingWeb                                                    | Måste matcha ServiceManifest.xml                 |
| appPackageUrl          | Programmets url för blob-lagring     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | URL:en för programpaketet i blob storage (proceduren för att ange URL beskrivs senare i artikeln) |

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

Kör cmdleten **New-AzResourceGroupDeployment** för att distribuera programmet till resursgruppen som innehåller klustret:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Uppgradera Service Fabric-programmet med Hjälp av Resource Manager

Du kan uppgradera ett program som redan har distribuerats till ett Service Fabric-kluster av något av följande skäl:

* En ny tjänst läggs till i programmet. En tjänstdefinition måste läggas till *i service-manifest.xml-* och *application-manifest.xml-filer* när en tjänst läggs till i programmet. Om du vill återspegla en ny version av ett program måste du också ändra programtypsversionen från 1.0.0 till 1.0.1 i [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json):

    ```json
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

* En ny version av en befintlig tjänst läggs till i programmet. Exempel på detta är ändringar av programkod och uppdateringar av apptypsversion och namn. För den här uppgraderingen uppdaterar du UserApp.Parameters.json så här:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Ta bort programresurser

Så här tar du bort ett program som har distribuerats med hjälp av programresursmodellen i Resource Manager:

1. Använd cmdleten [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) för att hämta resurs-ID:a

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Använd [cmdleten Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) för att ta bort programresurserna:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Nästa steg

Få information om programresursmodellen:

* [Modellera ett program i Service Fabric](service-fabric-application-model.md)
* [Program- och tjänstmanifest för Service Fabric](service-fabric-application-and-service-manifests.md)
* [Metodtips: Infrastruktur som kod](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Hantera program och tjänster som Azure-resurser](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
