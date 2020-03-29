---
title: Skapa & distribuera Visual Studio-resursgruppsprojekt
description: Använd Visual Studio för att skapa ett Azure-resursgruppsprojekt och distribuera resurserna till Azure.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5127732ac0c33d4b27f70bd616fb23aaec5c871f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152739"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Skapa och distribuera Azure-resursgrupper via Visual Studio

Med Visual Studio kan du skapa ett projekt som distribuerar din infrastruktur och kod till Azure. Du kan till exempel distribuera webbplatsens webbhotell, webbplats och kod. Visual Studio har många olika startmallar som du kan använda för att distribuera vanliga scenarier. I den här artikeln distribuerar du en webbapp.

Den här artikeln visar hur du använder [Visual Studio 2019 eller senare med Azure-utveckling och ASP.NET arbetsbelastningar installerade](/visualstudio/install/install-visual-studio?view=vs-2019). Om du använder Visual Studio 2017 är din upplevelse i stort sett densamma.

## <a name="create-azure-resource-group-project"></a>Skapa ett projekt för en Azure-resursgrupp

I det här avsnittet skapar du ett Azure Resource Group-projekt med en **webbappmall.**

1. Välj **Arkiv**>**nytt**>**projekt**i Visual Studio .
1. Välj projektmallen **Azure Resource Group** och **Nästa**.

    ![Skapa projekt](./media/create-visual-studio-deployment-project/create-project.png)

1. Ge projektet ett namn. De andra standardinställningarna är förmodligen bra, men granska dem så att de fungerar för din miljö. Välj **Skapa** när du är klar.

    ![Skapa projekt](./media/create-visual-studio-deployment-project/name-project.png)

1. Välj den mall som du vill distribuera till Azure Resource Manager. Observera att det finns många olika alternativ beroende på vilken typ av projekt som du vill distribuera. I den här artikeln väljer du **webbappmallen** och **OK**.

    ![Välja en mall](./media/create-visual-studio-deployment-project/select-project.png)

    Mallen som du väljer är bara en startpunkt. Du kan lägga till och ta bort resurser som passar ditt scenario.

1. Visual Studio skapar ett resursgruppsdistributionsprojekt för webbappen. Om du vill se filerna för projektet tittar du på noden i distributionsprojektet.

    ![Visa noder](./media/create-visual-studio-deployment-project/show-items.png)

    Sedan du valde webbappmallen visas följande filer:

   | Filnamn | Beskrivning |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Ett PowerShell-skript som kör PowerShell-kommandon för distribution till Azure Resource Manager. Visual Studio använder det här PowerShell-skriptet för att distribuera mallen. |
   | WebSite.json |Resource Manager-mallen som definierar infrastrukturen som du vill distribuera till Azure, och de parametrar som du kan ange under distributionen. Den definierar även beroendena mellan resurserna så att resurserna distribueras i rätt ordning av Resource Manager. |
   | WebSite.parameters.json |En parameterfil med värden som krävs av mallen. Du skickar in parametervärden för att anpassa varje distribution. |

    Alla distributionsprojekt för resursgrupper innehåller dessa grundläggande filer. Andra projekt kan innehålla ytterligare filer som ger stöd för andra funktioner.

## <a name="customize-resource-manager-template"></a>Anpassa resurshanterarens mall

Du kan anpassa ett distributionsprojekt genom att ändra resurshanteraren-mallen som beskriver de resurser som du vill distribuera. Mer information om elementen i Resource Manager-mallen finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).

1. Om du vill arbeta med mallen öppnar du **WebSite.json**.

1. Visual Studio-redigeraren innehåller verktyg som hjälper dig att redigera Resource Manager-mallen. Fönstret **JSON-disposition** gör det enkelt att se elementen som definierats i mallen.

   ![Visa JSON-kontur](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Markera ett element i dispositionen för att gå till den delen av mallen.

   ![Navigera JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Du kan lägga till en resurs genom att antingen välja knappen **Lägg till resurs** längst upp i fönstret JSON-disposition eller genom att högerklicka på **resurser** och välja **Lägg till ny resurs**.

   ![Lägg till resurs](./media/create-visual-studio-deployment-project/add-resource.png)

1. Välj **Lagringskonto** och ge det ett namn. Ange ett namn som innehåller fler än 11 tecken och endast siffror och små bokstäver.

   ![Lägg till lagringsutrymme](./media/create-visual-studio-deployment-project/add-storage.png)

1. Observera att inte bara resursen lades till, utan även en parameter för typen av lagringskonto och en variabel för namnet på lagringskontot.

   ![Visa disposition](./media/create-visual-studio-deployment-project/show-new-items.png)

1. Parametern för typen av lagringskonto är fördefinierad med tillåtna typer och en standardtyp. Du kan lämna dessa värden eller redigera dem för ditt scenario. Om du inte vill att någon ska distribuera ett **Premium_LRS**-lagringskonto med den här mallen tar du bort det från de tillåtna typerna.

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

   ![Visa intellisense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Du kan ange **numberOfWorkers** till 1 och spara filen.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Öppna filen **WebSite.parameters.json.** Du använder parameterfilen för att skicka in värden under distributionen som anpassar resursen som distribueras. Ge värdplanen ett namn och spara filen.

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

## <a name="deploy-project-to-azure"></a>Distribuera projekt till Azure

Du är nu redo att distribuera projektet till en resursgrupp.

Som standard använder PowerShell-skriptet (Deploy-AzureResourceGroup.ps1) i projektet AzureRM-modulen. Om du fortfarande har AzureRM-modulen installerad och vill fortsätta använda den kan du använda det här standardskriptet. Med det här skriptet kan du använda Visual Studio-gränssnittet för att distribuera din lösning.

Men om du har migrerat till den nya [Az-modulen](/powershell/azure/new-azureps-module-az)måste du lägga till ett nytt skript i projektet. Om du vill lägga till ett skript som använder Az-modulen kopierar du skriptet [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) och lägger till det i projektet. Om du vill använda det här skriptet för distribution måste du köra det från en PowerShell-konsol i stället för att använda Visual Studios distributionsgränssnitt.

Båda metoderna visas i den här artikeln. Den här artikeln refererar till standardskriptet som AzureRM-modulskript och det nya skriptet som Az-modulskript.

### <a name="az-module-script"></a>Az modul skript

Öppna en PowerShell-konsol för Az-modulskriptet och kör:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-modulskript

Använd Visual Studio för AzureRM-modulskript:

1. Välj **Distribuera** > **ny**på snabbmenyn för distributionsprojektnoden .

    ![Menyalternativ för ny distribution](./media/create-visual-studio-deployment-project/deploy.png)

1. Dialogrutan **Distribuera till resursgrupp**. I listrutan **Resursgrupp** väljer du en befintlig resursgrupp eller skapar en ny. Välj **Distribuera**.

    ![Dialogrutan Distribuera till resursgrupp](./media/create-visual-studio-deployment-project/show-deployment.png)

1. I fönstren **Utdata** kan du se status för distributionen. När distributionen är klar indikerar det sista meddelandet att distributionen är framgångsrik med något liknande:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Visa distribuerade resurser

Låt oss kolla resultaten.

1. Öppna [Azure Portal](https://portal.azure.com/) i en webbläsare och logga in på ditt konto. Du visar resursgruppen genom att välja **Resursgrupper** och den resursgrupp som du har distribuerat till.

1. Du ser alla distribuerade resurser. Observera att namnet på lagringskontot inte stämmer exakt med namnet du angav när du lade till resursen. Lagringskontot måste vara unikt. Mallen lägger automatiskt till en teckensträng i det namn du angav för att skapa ett unikt namn.

    ![Visa resurser](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Lägga till kod i projektet

Nu har du distribuerat infrastrukturen för din app, men det finns ingen faktisk kod som distribueras med projektet.

1. Lägga till ett projekt i din Visual Studio-lösning. Högerklicka på lösningen och välj **Lägg till** > **nytt projekt**.

    ![Lägg till projekt](./media/create-visual-studio-deployment-project/add-project.png)

1. Lägg till ett **ASP.NET core-webbprogram**.

    ![Lägg till webbapp](./media/create-visual-studio-deployment-project/add-app.png)

1. Ge webbappen ett namn och välj **Skapa**.

    ![Namn webbapp](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Välj **Webbprogram** och **Skapa**.

    ![Välj webbprogram](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Efter att Visual Studio skapat din webbapp visas båda projekten i lösningen.

    ![Visa projekt](./media/create-visual-studio-deployment-project/show-projects.png)

1. Nu måste du kontrollera att ditt resursgrupp-projekt är medvetet om det nya projektet. Gå tillbaka till resursgruppsprojektet (ExampleAppDeploy). Högerklicka på **Referenser** och välj **Lägg till referens**.

    ![Lägga till en referens](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Välj det webbapp-projekt som du har skapat.

   ![Lägga till en referens](./media/create-visual-studio-deployment-project/add-reference.png)

   Genom att lägga till en referens länkar du webbappprojektet till resursgruppsprojektet och ställer automatiskt in vissa egenskaper. Du ser dessa egenskaper i fönstret **Egenskaper** för referensen. **Ta med filsökväg** innehåller sökvägen där paketet skapas. Observera mappen (ExampleApp) och filen (package.zip). Du behöver dessa värden eftersom du anger dem som parametrar när du distribuerar appen.

   ![Se referens](./media/create-visual-studio-deployment-project/see-reference.png)

1. Gå tillbaka till mallen (WebSite.json) och lägg till en resurs i mallen.

    ![Lägg till resurs](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Denna gång väljer du **Webbdistribution för Web Apps**.

    ![Lägg till webb distribuera](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Spara mallen.

1. Det finns några nya parametrar i mallen. De lades till i föregående steg. Du behöver inte ange värden för **_artifactsLocation** eller **_artifactsLocationSasToken** eftersom dessa värden genereras automatiskt. Du måste dock ange mapp- och filnamnet på sökvägen som innehåller distributionspaketet. Namnen på dessa parametrar slutar med **PackageFolder** och **PackageFileName**. Den första delen av namnet är namnet på den webb distribuera resurs som du har lagt till. I den här artikeln heter de **ExampleAppPackageFolder** och **ExampleAppPackageFileName**.

   Öppna **Website.parameters.json** och ange dessa parametrar till de värden som du såg i referensegenskaperna. Ange **ExampleAppPackageFolder** till namnet på mappen. Ange **ExampleAppPackageFileName** till namnet på zip-filen.

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

Eftersom du har lagt till kod i projektet är distributionen lite annorlunda den här gången. Under distributionen kan du iscensätta artefakter för projektet till en plats som Resource Manager kan komma åt. Artefakterna är iscensatta till ett lagringskonto.

### <a name="az-module-script"></a>Az modul skript

Det finns en liten ändring du behöver göra i mallen om du använder Az-modulskriptet. Det här skriptet lägger till ett snedstreck på artefaktplatsen, men mallen förväntar sig inte att snedstreck. Öppna WebSite.json och hitta egenskaperna för MSDeploy-tillägget. Den har en egenskap som heter **packageUri**. Ta bort snedstrecket mellan artefaktplatsen och paketmappen.

Det bör se ut så här:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Meddelande i föregående exempel finns `'/',` det inget mellan **parametrar ("_artifactsLocation")** och **parametrar("ExampleAppPackageFolder")**.

Bygg om projektet. Genom att bygga projektet läggs de filer du behöver distribuera till i mellanlagringsmappen.

Öppna nu en PowerShell-konsol och kör:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-modulskript

Använd Visual Studio för AzureRM-modulskript:

1. Om du vill distribuera om väljer du **Distribuera**och den resursgrupp som du distribuerade tidigare.

    ![Omfördelningsprojekt](./media/create-visual-studio-deployment-project/redeploy.png)

1. Välj det lagringskonto som du har distribuerat med den här resursgruppen för **artefaktlagringskontot**.

   ![Distribuera om webbdistribution](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Visa webbapp

1. När distributionen är klar väljer du din webbapp i portalen. Välj webbadress för att gå till webbplatsen.

   ![Bläddra på webbplats](./media/create-visual-studio-deployment-project/browse-site.png)

1. Nu ser du att du har distribuerat standard-ASP.NET-appen.

   ![Visa distribuerad app](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Lägga till instrumentpanel för åtgärder

Du är inte begränsad till de resurser som är tillgängliga via Visual Studio-gränssnittet. Du kan anpassa din distribution genom att lägga till en anpassad resurs i mallen. Du kan lägga till en instrumentpanel för att hantera den resurs du har distribuerat.

1. Öppna filen WebSite.json och lägg till följande JSON efter `]` lagringskontoresursen men innan resursavsnittet stängs.

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
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Distribuera om projektet.

1. När distributionen är klar visar du instrumentpanelen i portalen. Välj **Instrumentpanel** och välj den du distribuerade.

   ![Anpassad instrumentpanel](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Du ser den anpassade instrumentpanelen.

   ![Anpassad instrumentpanel](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Du kan hantera åtkomsten till instrumentpanelen med hjälp av RBAC-grupper. Du kan också anpassa instrumentpanelens utseende när den har distribuerats. Om du däremot distribuerar om resursgruppen återställs instrumentpanelen till sitt ursprungsläge i mallen. Mer information om hur du skapar instrumentpaneler finns i [Skapa Azure-instrumentpaneler programmässigt](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. På Azure-portalen väljer du **Resursgrupper** på den vänstra menyn.

1. Välj resursgruppens namn.

1. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du skapar och distribuerar mallar med Visual Studio. Mer information om mallutveckling finns i vår nya videoprogramledningsserie:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)
