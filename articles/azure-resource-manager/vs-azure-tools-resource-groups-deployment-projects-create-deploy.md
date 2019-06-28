---
title: Skapa och distribuera grupprojekt i Visual Studio Azure-resurs
description: Använd Visual Studio för att skapa ett Azure-resursgruppsprojekt och distribuera resurserna till Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: 8677d906375853bdde5c192c86dacc7479f2e31e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311325"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Skapa och distribuera Azure-resursgrupper via Visual Studio

Med Visual Studio kan du skapa ett projekt som distribuerar din infrastruktur och kod till Azure. Du kan till exempel distribuera Webbvärden, webbplatsen och koden för webbplatsen. Visual Studio har många olika startmallar som du kan använda för att distribuera vanliga scenarier. I den här artikeln får distribuera du en webbapp.  

Den här artikeln visar hur du använder [Visual Studio 2019 eller senare med Azure utvecklings- och ASP.NET-arbetsbelastningarna](/visualstudio/install/install-visual-studio?view=vs-2019). Om du använder Visual Studio 2017, dina åsikter är i stort sett desamma.

## <a name="create-azure-resource-group-project"></a>Skapa ett projekt för en Azure-resursgrupp

I det här avsnittet ska du skapa ett Azure-resursgrupp-projekt med en **webbapp** mall.

1. I Visual Studio väljer **filen**, **New**, och **projekt**. Välj den **Azure-resursgrupp** projektmall och **nästa**.

    ![Skapa projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Ge projektet ett namn. Andra standard inställningar är troligtvis inte skadad, men granska dem att göra de arbetar för din miljö. Välj **Skapa** när du är klar.

    ![Skapa projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Välj den mall som du vill distribuera till Azure Resource Manager. Observera att det finns många olika alternativ beroende på vilken typ av projekt som du vill distribuera. I den här artikeln väljer du den **webbapp** mall och **OK**.

    ![Välja en mall](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Mallen som du väljer är bara en startpunkt. Du kan lägga till och ta bort resurser som passar ditt scenario.

1. Visual Studio skapar ett distributionsprojekt för resurs för webbappen. Titta på noden i distributionsprojektet för att se filerna för ditt projekt.

    ![Visa noder](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Eftersom du har valt mallen för Webbappar kan se du följande filer:

   | Filnamn | Beskrivning |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Ett PowerShell-skript som kör PowerShell-kommandon för distribution till Azure Resource Manager. Visual Studio använder PowerShell-skript för att distribuera mallen. |
   | WebSite.json |Resource Manager-mallen som definierar infrastrukturen som du vill distribuera till Azure, och de parametrar som du kan ange under distributionen. Den definierar även beroendena mellan resurserna så att resurserna distribueras i rätt ordning av Resource Manager. |
   | WebSite.parameters.json |En parameterfil med värden som krävs av mallen. Du skickar in parametervärden för att anpassa varje distribution. |

    Alla distributionsprojekt för resursgrupper innehåller dessa grundläggande filer. Andra projekt kan innehålla ytterligare filer som ger stöd för andra funktioner.

## <a name="customize-resource-manager-template"></a>Anpassa Resource Manager-mall

Du kan anpassa ett distributionsprojekt genom att ändra Resource Manager-mallen som beskriver de resurser som du vill distribuera. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

1. Om du vill arbeta med din mall, öppna **WebSite.json**.

1. Visual Studio-redigeraren innehåller verktyg som hjälper dig att redigera Resource Manager-mallen. Fönstret **JSON-disposition** gör det enkelt att se elementen som definierats i mallen.

   ![Visa JSON-disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Markera ett element i dispositionen att gå till den delen av mallen.

   ![Navigera i JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Du kan lägga till en resurs genom att antingen välja knappen **Lägg till resurs** längst upp i fönstret JSON-disposition eller genom att högerklicka på **resurser** och välja **Lägg till ny resurs**.

   ![Lägg till resurs](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Välj **Lagringskonto** och ge den ett namn. Ange ett namn som innehåller fler än 11 tecken och endast siffror och små bokstäver.

   ![Lägg till lagringsutrymme](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Observera att inte bara resursen lades till, utan även en parameter för typen av lagringskonto och en variabel för namnet på lagringskontot.

   ![Visa disposition](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. Parametern för typ av storage-konto är fördefinierad med tillåtna typer och en standardtyp. Du kan lämna dessa värden eller redigera dem för ditt scenario. Om du inte vill att någon ska distribuera ett **Premium_LRS**-lagringskonto med den här mallen tar du bort det från de tillåtna typerna.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. Visual Studio tillhandahåller även IntelliSense som gör det lättare för dig att förstå vilka egenskaper som är tillgängliga när du redigerar mallen. Om du till exempel vill redigera egenskaperna för din App Service plan går du till resursen **HostingPlan** och lägger till ett värde för **properties**. Observera att IntelliSense visar de tillgängliga värdena och en beskrivning av det aktuella värdet.

   ![Visa intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Du kan ange **numberOfWorkers** till 1 och spara filen.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Öppna den **WebSite.parameters.json** fil. Du kan använda parameterfilen för att skicka in värden under distributionen som anpassa resursen som ska distribueras. Namnge värdplanen och spara filen.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Distribuera projektet till Azure

Du är nu redo att distribuera projektet till en resursgrupp.

Som standard använder AzureRM-modulen i PowerShell-skript (distribuera AzureResourceGroup.ps1) i projektet. Om du fortfarande har installerat AzureRM-modulen och vill fortsätta använda det, kan du använda det här standardskriptet. Med det här skriptet kan använda du Visual Studio-gränssnittet för att distribuera din lösning.

Men om du har migrerat till den nya [Az modulen](/powershell/azure/new-azureps-module-az), du måste lägga till ett nytt skript i projektet. Om du vill lägga till ett skript som använder Az-modulen, kopiera den [distribuera AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) skript och lägga till den i projektet. Om du vill använda det här skriptet för distribution, måste du köra den från en PowerShell-konsolen i stället för Visual Studio-gränssnittet för distribution.

Båda metoderna som visas i den här artikeln. Den här artikeln refererar till standardskriptet som skriptet som AzureRM-modulen och det nya skriptet som skriptet som Az-modulen.

### <a name="az-module-script"></a>AZ-modulen skript

Öppna en PowerShell-konsol för skriptet Az-modulen och kör:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-modulen skript

För skriptet AzureRM-modulen använder du Visual Studio:

1. På snabbmenyn för distributionsprojektets nod väljer du **Distribuera** > **Ny**.

    ![Menyalternativet för ny distribution](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Dialogrutan **Distribuera till resursgrupp**. I listrutan **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. Välj **Distribuera**.

    ![Distribuera till dialogrutan för resurs-grupp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. I fönstren **Utdata** kan du se status för distributionen. När distributionen är klar indikerar det sista meddelandet att distributionen är framgångsrik med något liknande:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Visa distribuerade resurser

Nu ska vi kontrollera resultaten.

1. Öppna [Azure Portal](https://portal.azure.com/) i en webbläsare och logga in på ditt konto. Du visar resursgruppen genom att välja **Resursgrupper** och den resursgrupp som du har distribuerat till.

1. Du ser alla distribuerade resurser. Observera att namnet på lagringskontot inte stämmer exakt med namnet du angav när du lade till resursen. Lagringskontot måste vara unikt. Mallen lägger automatiskt till en sträng med tecken i det namn du angav för att skapa ett unikt namn.

    ![Visa resurser](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Lägg till kod i projektet

Nu har du distribuerat infrastrukturen för din app, men det finns ingen faktisk kod som distribueras med projektet.

1. Lägga till ett projekt i din Visual Studio-lösning. Högerklicka på lösningen och välj **Lägg till** > **nytt projekt**.

    ![lägga till projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Lägg till en **ASP.NET Core-Webbapp**.

    ![lägga till webbapp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Namnge din webbapp och välj **skapa**.

    ![Namn på webbapp](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Välj **webbprogram** och **skapa**.

    ![Välj webbprogram](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Efter att Visual Studio skapat din webbapp visas båda projekten i lösningen.

    ![Visa projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Nu måste du kontrollera att ditt resursgrupp-projekt är medvetet om det nya projektet. Gå tillbaka till ditt resursgrupp-projekt (ExampleAppDeploy). Högerklicka på **Referenser** och välj **Lägg till referens**.

    ![Lägg till referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Välj det webbapp-projekt som du har skapat.

   ![Lägg till referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Genom att lägga till en referens, länkar du webbappsprojektet till resursgruppsprojektet och ställer automatiskt in vissa egenskaper. Du ser dessa egenskaper i fönstret **Egenskaper** för referensen. **Ta med filsökväg** innehåller sökvägen där paketet skapas. Observera mappen (ExampleApp) och filen (package.zip). Du behöver dessa värden eftersom du anger dem som parametrar när du distribuerar appen.

   ![Se referens](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Gå tillbaka till din mall (WebSite.json) och Lägg till en resurs i mallen.

    ![Lägg till resurs](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Denna gång väljer du **Webbdistribution för Web Apps**. 

    ![Lägg till web distribuera](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Spara mallen.

1. Det finns några nya parametrar i mallen. De har lagts till i föregående steg. Du behöver inte ange värden för **_artifactsLocation** eller **_artifactsLocationSasToken** eftersom dessa värden har genererats automatiskt. Du måste dock ange mappen och filnamnet till sökvägen som innehåller distributionspaketet. Namnen på de här parametrarna sluta med **PackageFolder** och **PackageFileName**. Den första delen av namnet är namnet på den Web Deploy-resurs som du har lagt till. I den här artikeln de namnges **ExampleAppPackageFolder** och **ExampleAppPackageFileName**. 

   Öppna **Website.parameters.json** och ange dessa parametrar de värden som du såg i referensegenskaperna. Ange **ExampleAppPackageFolder** till namnet på mappen. Ange **ExampleAppPackageFileName** till namnet på zip-filen.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Distribuera kod med infrastruktur

Eftersom du har lagt till kod i projektet, är din distribution är lite annorlunda nu. Under distributionen av mellanlagra artefakter för ditt projekt till en plats som har åtkomst till Resource Manager. Artefakter mellanlagras till ett lagringskonto.

### <a name="az-module-script"></a>AZ-modulen skript

Det finns en liten ändring som du behöver göra i mallen om du använder skriptet Az-modulen. Det här skriptet lägger till ett snedstreck till artefakter platsen men mallen förväntar dig inte att snedstreck. Öppna WebSite.json och Sök efter egenskaperna för MSDeploy-tillägget. Den har en egenskap med namnet **packageUri**. Ta bort snedstreck mellan platsen i artefakter och paketmappen.

Det bör se ut:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Observera i föregående exempel finns inga `'/',` mellan **parameters('_artifactsLocation')** och **parameters('ExampleAppPackageFolder')** .

Återskapa projektet. Att skapa projektet ser till att filer som du behöver distribuera läggs till den tillfälliga mappen.

Öppna en PowerShell-konsol och kör:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-modulen skript

För skriptet AzureRM-modulen använder du Visual Studio:

1. Om du vill distribuera om, Välj **distribuera**, och resursgruppen som du distribuerade tidigare.

    ![Omdistribuera projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Välj det lagringskonto som du har distribuerat med den här resursgruppen för den **artefaktlagringskonto**.

   ![Distribuera om web distribuera](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Visa webbapp

1. När distributionen är klar väljer du din webbapp i portalen. Välj webbadress för att gå till webbplatsen.

   ![Bläddra på webbplats](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Nu ser du att du har distribuerat standard-ASP.NET-appen.

   ![Visa distribuerad app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Lägg till åtgärder instrumentpanel

Du är inte begränsad till de resurser som är tillgängliga via Visual Studio-gränssnittet. Du kan anpassa din distribution genom att lägga till en anpassad resurs i mallen. Du kan lägga till en instrumentpanel för att hantera den resurs du har distribuerat.

1. Öppna filen WebSite.json och Lägg till följande JSON efter resursen för lagringskonton men före avslutande `]` av resurserna.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Distribuera om ditt projekt.

1. När distributionen är klar kan du visa instrumentpanelen i portalen. Välj **instrumentpanelen** och välj den som du har distribuerat.

   ![Anpassad instrumentpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Du ser den anpassade instrumentpanelen.

   ![Anpassad instrumentpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

Du kan hantera åtkomsten till instrumentpanelen med hjälp av RBAC-grupper. Du kan också anpassa instrumentpanelens utseende när den har distribuerats. Om du däremot distribuerar om resursgruppen återställs instrumentpanelen till sitt ursprungsläge i mallen. Mer information om hur du skapar instrumentpaneler finns i [Skapa Azure-instrumentpaneler programmässigt](../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Azure-portalen väljer du **resursgrupper** menyn till vänster.

1. Välj resursgruppens namn.

1. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig att skapa och distribuera mallar med hjälp av Visual Studio. Nästa självstudiekurs visar hur du hittar information i mallreferensen så att du kan skapa ett krypterat Azure Storage-konto.

> [!div class="nextstepaction"]
> [Skapa ett krypterat lagringskonto](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
