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
ms.date: 09/18/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: c23646bca6109d27e57b2f928363e65c83c634eb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031160"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Självstudie: Lär dig hur du uppgraderar ett Service Fabric-program med Visual Studio

Den här självstudien är del fyra i en serie och visar hur du uppgraderar ett nät för Azure Service Fabric-program direkt från Visual Studio. Uppgraderingen innehåller både en uppdatering och en konfigurationsuppdatering. Du kan se att stegen för att uppgradera och publicera från Visual Studio är desamma.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Uppgradera en Service Fabric-nät tjänst med hjälp av Visual Studio

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa ett Service Fabric-nät app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Felsöka ett Service Fabric-nät app som körs i det lokala utvecklingsklustret](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuera ett Service Fabric-nät app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Uppgradera ett Service Fabric-nät app
> * [Rensa Service Fabric blandar resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har distribuerat att göra-app, följer du anvisningarna i [publicera ett Service Fabric-nät webbprogram](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Uppgradera en Service Fabric-nät tjänst med hjälp av Visual Studio

Den här artikeln visar hur du uppgraderar en mikrotjänst inom ett program oberoende av varandra.  I det här exemplet ska vi ändra den `WebFrontEnd` -tjänsten för att visa en aktivitetskategori. Vi kommer sedan att uppgradera den distribuerade tjänsten.

## <a name="modify-the-config"></a>Ändra konfigurationen

Uppgraderingar kan vara på grund av ändringar i koden, konfigurationsändringar eller båda.  För att införa en ändrad, öppna den `WebFrontEnd` projektets `service.yaml` fil (vilket är den **tjänstresurser** nod).

I den `resources:` ändrar `cpu:` mellan 0,5 1,0 i förväntan att klientdelen kraftigt används. Det bör nu se ut så här:

```xml
              ...
              resources:
                requests:
                  cpu: 1.0
                  memoryInGB: 1
              ...
```

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

Oavsett om du gör en koduppgradering av eller ett config-uppgradering (i det här fallet vi gör båda), uppgradera din nät för Service Fabric-app på Azure Högerklicka på **todolistapp** i Visual Studio och välj **publicera ...**

Därefter visas dialogrutan **Publish Service Fabric Application** (Publicera Service Fabric-program).

![Visual Studio, dialogruta för Service Fabric Mesh-projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Välj Azure-konto och Azure-prenumeration. Se till att **plats** är inställd på den plats som du använde när du ursprungligen publicerades att göra-app till Azure. Den här artikeln används **USA, östra**.

Se till att **resursgrupp** är inställd på den resursgrupp som du använde när du ursprungligen publicerades att göra-app till Azure.

Se till att **Azure Container Registry** har angetts till azure container registry-namn som du skapade när du ursprungligen publicerades att göra-app till Azure.

I dialogrutan Publicera trycker du på den **publicera** knappen för att uppgradera att göra-app på Azure.

Du kan övervaka förloppet för uppgraderingen genom att välja den **Service Fabric-verktyg** i Visual Studio **utdata** fönster. När uppgraderingen har slutförts, den **Service Fabric-verktyg** utdata visar du IP-adress och port för ditt program i form av en URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Öppna en webbläsare och navigera till webbadressen för att se webbplatsen köras i Azure. Du bör nu se en webbsida som innehåller en kategori-kolumn.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:
> [!div class="checklist"]
> * Så här uppgraderar du en röra för Service Fabric-app med hjälp av Visual Studio

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Rensa Service Fabric blandar resurser](service-fabric-mesh-tutorial-cleanup-resources.md)