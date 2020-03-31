---
title: Självstudiekurs- Uppgradera ett Azure Service Fabric Mesh-program
description: Den här självstudien är del fyra i en serie och visar hur du uppgraderar ett Azure Service Fabric Mesh-program direkt från Visual Studio.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351723"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Självstudiekurs: Lär dig hur du uppgraderar ett Service Fabric-program med Visual Studio

Den här självstudien är del fyra i en serie och visar hur du uppgraderar ett Azure Service Fabric Mesh-program direkt från Visual Studio. Uppgraderingen kommer att innehålla både en koduppdatering och en konfigurationsuppdatering. Du ser att stegen för att uppgradera och publicera inifrån Visual Studio är desamma.

I den här guiden får du lära du dig hur man:
> [!div class="checklist"]
> * Uppgradera en Service Fabric Mesh-tjänst med Visual Studio

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uppgradera en Service Fabric Mesh-app
> * [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Krav

Innan du börjar den här självstudien:

* Om du inte har distribuerat att göra-appen följer du anvisningarna i [Publicera ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uppgradera en Service Fabric Mesh-tjänst med Visual Studio

Den här artikeln visar hur du uppgraderar en mikrotjänst i ett program. I det här exemplet ändrar vi `WebFrontEnd` tjänsten för att visa en uppgiftskategori och öka mängden processor som den får. Sedan uppgraderar vi den distribuerade tjänsten.

## <a name="modify-the-config"></a>Ändra konfigurationen

När du skapar en Service Fabric Mesh-app lägger Visual studio till en **parameters.yaml-fil** för varje distributionsmiljö (moln och lokal). I dessa filer kan du definiera parametrar och deras värden som sedan kan refereras från dina Mesh *.yaml-filer som service.yaml eller network.yaml.  Visual Studio innehåller några variabler för dig, till exempel hur mycket CPU tjänsten kan använda.

Vi uppdaterar parametern `WebFrontEnd_cpu` för att uppdatera `1.5` cpu-resurserna till i väntan på att **WebFrontEnd-tjänsten** kommer att användas mer.

1. Öppna **filen parameters.yaml** under **Miljöer** > **Cloud**i **todolistapp-projektet.** Ändra `WebFrontEnd_cpu`värdet , `1.5`till . Parameternamnet föregås av tjänstnamnet `WebFrontEnd_` som en bästa praxis för att skilja det från parametrar med samma namn som gäller för olika tjänster.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Öppna **WebFrontEnd-projektets** **service.yaml-fil** under **WebFrontEnd** > **Service Resources**.

    Observera att `resources:` avsnittet `cpu:` i avsnittet `"[parameters('WebFrontEnd_cpu')]"`är inställt på . Om projektet byggs för molnet hämtas `'WebFrontEnd_cpu` värdet för från **filen Environments** > **Cloud** > **parameters.yaml** och blir `1.5`. Om projektet byggs för att köras lokalt hämtas värdet från filen **Environments** > **Local** > **parameters.yaml** och är "0,5".

> [!Tip]
> Som standard används parameterfilen som är peer i filen profile.yaml för att ange värdena för filen profile.yaml.
> Miljöer > Cloud > parameters.yaml tillhandahåller till exempel parametervärden för miljöer > Cloud > profile.yaml.
>
> Du kan åsidosätta detta genom att lägga till`parametersFilePath=”relative or full path to the parameters file”` följande `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` i filen profile.yaml: Till exempel eller`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Ändra modellen

Om du vill införa `Category` en kodändring lägger du till en egenskap i `ToDoItem` klassen i `ToDoItem.cs` filen.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Uppdatera sedan `Load()` metoden i samma fil för att ställa in kategorin på en standardsträng:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Ändra tjänsten

Projektet `WebFrontEnd` är ett ASP.NET Core-program med en webbsida som visar att göra-listobjekt. Öppna `WebFrontEnd` `Index.cshtml` och lägg till följande två rader nedan i projektet för att visa aktivitetens kategori:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Skapa och kör appen för att kontrollera att du ser en ny kategorikolumn på webbsidan som visar uppgifterna.

## <a name="upgrade-the-app-from-visual-studio"></a>Uppgradera appen från Visual Studio

Oavsett om du gör en koduppgradering eller en konfigurationsuppgradering (i det här fallet gör vi båda), uppgraderar du din Service Fabric Mesh-app på Azure genom att högerklicka på **todolistapp** i Visual Studio och välj sedan **Publicera...**

Därefter visas dialogrutan **Publish Service Fabric Application** (Publicera Service Fabric-program).

Använd listrutan **Målprofil** för att välja den profile.yaml-fil som ska användas för den här distributionen. Vi uppgraderar appen i molnet så att vi väljer **cloud.yaml** i `WebFrontEnd_cpu` listrutan, som kommer att använda värdet 1,0 som definieras i filen.

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Ange **platsen** till den plats som du använde när du ursprungligen publicerade att göra-appen till Azure. Denna artikel används **Östra USA**.

Ange **resursgrupp** till resursgruppen som du använde när du ursprungligen publicerade att göra-appen till Azure.

Ange **Azure Container Registry** till det azure-behållarregisternamn som du skapade när du ursprungligen publicerade att göra-appen till Azure.

I dialogrutan Publicera trycker du på knappen **Publicera** för att uppgradera att göra-appen på Azure.

Övervaka förloppet för uppgraderingen genom att välja fönstret Verktyg för **serviceinfrastruktur** i fönstret **Utdata** i Visual Studio. 

När avbildningen har skapats och vidare till Azure Container Registry visas en **För-statuslänk** i utdata som du kan klicka på för att övervaka distributionen i Azure-portalen.

När uppgraderingen är klar visar **Service Fabric Tools-utdata** IP-adressen och porten för ditt program i form av en URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen köras i Azure. Du bör nu se en webbsida som innehåller en kategorikolumn.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:
> [!div class="checklist"]
> * Så här uppgraderar du en Service Fabric Mesh-app med Visual Studio

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)