---
title: Självstudie – uppgradering ett nät för Azure Service Fabric-program | Microsoft Docs
description: Lär dig hur du uppgraderar ett Service Fabric-program med Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: tylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: 0f6ede488ae118f8df00febda3c53eabb73f2030
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890236"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Självstudie: Lär dig hur du uppgraderar ett Service Fabric-program med Visual Studio

Den här självstudien är del fyra i en serie och visar hur du uppgraderar ett nät för Azure Service Fabric-program direkt från Visual Studio. Uppgraderingen innehåller både en uppdatering och en konfigurationsuppdatering. Du ser att stegen för att uppgradera och publicera från Visual Studio är desamma.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Uppgradera en Service Fabric-nät tjänst med hjälp av Visual Studio

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uppgradera ett Service Fabric-nät app
> * [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har distribuerat att göra-appen följer du anvisningarna i [Publicera ett Service Fabric Mesh-webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uppgradera en Service Fabric-nät tjänst med hjälp av Visual Studio

Den här artikeln visar hur du uppgraderar en mikrotjänst inom ett program. I det här exemplet ändrar vi den `WebFrontEnd` -tjänsten för att visa en aktivitetskategori och öka hur mycket Processorkraft som tilldelats den. Vi kommer sedan att uppgradera den distribuerade tjänsten.

## <a name="modify-the-config"></a>Ändra konfigurationen

När du skapar en app med Service Fabric-nät, Visual studio lägger till en **parameters.yaml** fil för varje distributionsmiljö (i molnet och lokala). Du kan definiera parametrar och värden som sedan kan refereras från nät *.yaml filer, till exempel service.yaml eller network.yaml i dessa filer.  Visual Studio har vissa variabler för dig, t.ex hur mycket CPU som kan användas av tjänsten.

Uppdaterar vi den `WebFrontEnd_cpu` parametern för att uppdatera cpu-resurser till `1.5` i förväntan som den **WebFrontEnd** virtuellt används av tjänsten.

1. I den **todolistapp** projektet, under **miljöer** > **molnet**öppnar den **parameters.yaml** fil. Ändra den `WebFrontEnd_cpu`, värde att `1.5`. Parameternamnet inleds med namnet på tjänsten `WebFrontEnd_` som bästa praxis att skilja den från parametrar med samma namn som gäller för olika tjänster.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Öppna den **WebFrontEnd** projektets **service.yaml** filen under **WebFrontEnd** > **tjänstresurser**.

    Observera att det i `resources:` avsnittet `cpu:` är inställd på `"[parameters('WebFrontEnd_cpu')]"`. Om projektet håller på att skapas för molnet, värdet för `'WebFrontEnd_cpu` hämtas från den **miljöer** > **molnet** > **parameters.yaml** fil och kommer att `1.5`. Om projektet håller på att skapas för att köras lokalt, värdet hämtas från den **miljöer** > **lokala** > **parameters.yaml** filen, och ”0,5” blir.

> [!Tip]
> Parameterfil som är en peer-filens profile.yaml används för att ange värden för profile.yaml filen som standard.
> Till exempel miljöer > moln > parameters.yaml innehåller parametervärdena för miljöer > moln > profile.yaml.
>
> Du kan åsidosätta detta genom att lägga till följande i filen profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` exempelvis `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` eller `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Ändra modellen

För att införa en kodändring, lägger du till en `Category` egenskap enligt den `ToDoItem` klassen i den `ToDoItem.cs` filen.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Uppdatera sedan den `Load()` metod i samma fil att ange kategorin till en standard-sträng:

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

Den `WebFrontEnd` project är ett ASP.NET Core-program med en webbsida som visar att göra listobjekt. I den `WebFrontEnd` projektet öppnar `Index.cshtml` och Lägg till följande två rader anges nedan, för att visa aktivitetens kategori:

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

Skapa och köra appen för att kontrollera att du ser en ny kategori kolumn på webbsidan som visas aktiviteterna.

## <a name="upgrade-the-app-from-visual-studio"></a>Uppgradera appen från Visual Studio

Om du gör en koduppgradering av eller en config-uppgradering (i det här fallet vi gör båda) måste du uppgradera ditt Service Fabric-nät app i Azure genom att högerklicka på **todolistapp** i Visual Studio och välj sedan **publicera...**

Därefter visas dialogrutan **Publish Service Fabric Application** (Publicera Service Fabric-program).

Använd den **målprofilen** listrutan och välj profile.yaml-fil som ska användas för den här distributionen. Vi uppgraderar appen i molnet så vi väljer den **cloud.yaml** i listrutan som använder den `WebFrontEnd_cpu` värdet 1,0 som definierats i filen.

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Ange den **plats** till den plats som du använde när du ursprungligen publicerades att göra-app till Azure. Den här artikeln används **USA, östra**.

Ange **resursgrupp** till resursgruppen som du använde när du ursprungligen publicerades att göra-app till Azure.

Ange **Azure Container Registry** till azure container registry-namn som du skapade när du ursprungligen publicerades att göra-app till Azure.

I dialogrutan Publicera trycker du på den **publicera** knappen för att uppgradera att göra-app på Azure.

Övervaka förloppet för uppgraderingen genom att välja den **Service Fabric-verktyg** i Visual Studio **utdata** fönster. 

När avbildningen har skapats och push-överfört till Azure Container Registry, en **för status** länk kommer att visas i utdata som du kan klicka på för att övervaka distributionen i Azure-portalen.

När uppgraderingen har slutförts, den **Service Fabric-verktyg** utdata visar du IP-adress och port för ditt program i form av en URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen köras i Azure. Du bör nu se en webbsida som innehåller en kategori-kolumn.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:
> [!div class="checklist"]
> * Så här uppgraderar du en app med Service Fabric-nät med hjälp av Visual Studio

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)