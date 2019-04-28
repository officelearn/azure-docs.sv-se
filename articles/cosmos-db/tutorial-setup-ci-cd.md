---
title: Konfigurera CI/CD-pipeline med Azure Cosmos DB-emulatorns build-uppgift
description: Självstudiekurs om hur du skapar bygg och släpp-arbetsflöden i Azure DevOps med hjälp Azure Cosmos DB-emulatorns build-uppgift
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/02/2018
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: d6250b778cdaec47ccbe2f45d35adea0b676a20a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115473"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Konfigurera en CI/CD-pipeline med Azure Cosmos DB-emulatorns build-uppgift i Azure DevOps

Azure Cosmos DB-emulatorn ger en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utveckling. Med emulatorn kan du utveckla och testa programmet lokalt, utan att skapa en Azure-prenumeration och utan att det kostar något. 

Med Azure Cosmos DB-emulatorns build-uppgift för Azure DevOps kan du göra detsamma i en CI-miljö. Med build-uppgiften kan du köra tester mot emulatorn som en dela av dina bygg och släpp-arbetsflöden. Uppgiften startar en Docker-container där emulatorn redan körs och ger en slutpunkt som kan användas av resten av build-definitionen. Du kan skapa och starta så många instanser av emulatorn som du behöver, där var och en körs i en separat container. 

Den här artikeln visar hur du konfigurerar en CI-pipeline i Azure DevOps för en ASP.NET-app som använder Cosmos DB-emulatorns build-uppgift för att köra tester. Du kan använda ett liknande tillvägagångssätt för att konfigurera en CI-pipeline för ett Node.js- eller Python-program. 

## <a name="install-the-emulator-build-task"></a>Installera emulatorns build-uppgift

För att använda build-uppgiften måste vi först installera den i Azure DevOps-organisationen. Leta reda på tillägget **Azure Cosmos DB-emulator** i [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) och klicka på **Get it free** (Skaffa det kostnadsfritt).

![Hitta och installera Azure Cosmos DB-emulatorns build-uppgift i Azure DevOps Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Välj sedan den organisation där du vill installera tillägget. 

> [!NOTE]
> Om du vill installera ett tillägg till en Azure DevOps-organisation måste du vara Kontoägare eller samling projektadministratör. Om du saknar behörigheter, men du är kontomedlem, kan du istället begära tillägg. [Läs mer.](https://docs.microsoft.com/azure/devops/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions)

![Välj en Azure DevOps-organisation där du vill installera ett tillägg](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Skapa en byggesdefinition

Nu när tillägget har installerats loggar du på ditt Azure DevOps-konto och hitta ditt projekt i projektinstrumentpanelen. Du kan lägga till en [bygg-pipeline](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) i projektet eller ändrar en befintlig bygg-pipeline. Om du redan har en bygg-pipeline kan du gå vidare till [Lägga till emulatorns build-uppgift i en build-definition](#addEmulatorBuildTaskToBuildDefinition).

1. Du kan skapa en ny build-definition genom att gå till fliken **Build** (Bygg) i Azure DevOps. Välj **+New** (Ny)  \> **Skapa ny pipeline**

   ![Skapa en ny bygg-pipeline](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png)

2. Välj **källa**, **Team project** (Teamprojekt), **Repository** (Lagringsplats) och **Default branch for manual and scheduled builds** (Standardgren för manuella och schemalagda byggen). När du har valt de alternativ som krävs väljer du **Continue** (Fortsätt)

   ![Välja teamprojekt, lagringsplats och gren för bygg-pipeline](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

3. Välj slutligen den mall du vill använda för bygg-pipeline. Vi väljer **ASP.NET**-mallen i den här kursen. 

Nu har vi en bygg-pipeline som vi kan konfigurera att använda Azure Cosmos DB-emulatorns build-uppgift. 

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Lägga till uppgiften i en bygg-pipeline

1. Innan du lägger till uppgiften i bygg-pipeline bör du lägga till ett agentjobb. Gå till bygg-pipeline, välj **...** och välj **Add an agent job** (Lägg till ett agentjobb).

1. Välj sedan symbolen **+** bredvid agentjobbet för att lägga till emulatorns build-uppgift. Sök efter **cosmos** i sökrutan, välj **Azure Cosmos DB-emulatorn** och lägg till den i agentjobbet. Build-uppgiften startar en container med en instans av Cosmos DB-emulatorn som redan körs på den. Azure Cosmos DB-emulatoruppgiften ska placeras före alla andra uppgifter som förväntar att emulatorns ska köras.

   ![Lägga till emulatorns build-uppgift i build-definitionen](./media/tutorial-setup-ci-cd/addExtension_3.png)

I den här självstudiekursen lägger du till uppgiften i början för att se till att emulatorn är tillgänglig innan våra tester körs.

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurera tester för att använda emulatorn

Nu kan konfigurerar vi våra tester för att använda emulatorn. Emulatorns build-uppgift exporterar en miljövariabel – CosmosDbEmulator.Endpoint – som uppgifter längre in i bygg-pipeline kan utfärda begäranden mot. 

I den här självstudiekursen använder vi [Visual Studio Test-uppgiften](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) för att köra enhetstester konfigurerade via en **.runsettings**-fil. Mer information om konfiguration av enhetstester finns i [dokumentationen](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017). Det fullständiga kodexemplet för det Todo-program som du använder i det här dokumentet finns på [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app)

Nedan är ett exempel på en **.runsettings**-fil som definierar parametrar som ska skickas till ett programs enhetstester. Observera att `authKey`-variabeln som används är den [välkända nyckeln](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) för emulatorn. Denna `authKey` är nyckeln som förväntas av emulatorns build-uppgift och ska definieras i **.runsettings**-filen.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

Om du konfigurerar en CI/CD-pipeline för ett program som använder Azure Cosmos DB:s API för MongoDB innehåller anslutningssträngen portnumret 10255 som standard. För närvarande är den här porten dock inte öppen, så du bör som ett alternativ använda port 10250 för att upprätta anslutningen. Azure Cosmos DB:s API för MongoDB-anslutningssträngen förblir densamma förutom att det portnummer som stöds är 10250 i stället för 10255.

Dessa parametrar `TestRunParameters` refereras via en `TestContext`-egenskap i programmets testprojekt. Här är ett exempel på ett test som körs mot Cosmos DB.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Gå till körningsalternativen i Visual Studio-Test-uppgiften. I alternativet **Settings file** (Inställningsfil) anger du att testerna har konfigurerats med **.runsettings**-filen. I alternativet **Override test run parameters** (Åsidosätt testkörningsparametrar) lägger du till i `-endpoint $(CosmosDbEmulator.Endpoint)`. När du gör det konfigureras Test-uppgiften så att den refererar till emulatorns build-uppgift, istället för den som definieras i **.runsettings**-filen.  

![Åsidosätt slutpunktsvariabeln med slutpunkten för emulatorns build-uppgift](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Kör bygget

**Spara och köplacera** nu bygget. 

![Spara och kör bygget](./media/tutorial-setup-ci-cd/runBuild_1.png)

Observera när bygget har startats att Cosmos DB-emulatoruppgiften har börjat hämta Docker-avbildningen med emulatorn installerad. 

![Spara och kör bygget](./media/tutorial-setup-ci-cd/runBuild_4.png)

Observera när bygget har slutförts att testet godkänns, och att allt körs mot Cosmos DB-emulatorn från build-uppgiften!

![Spara och kör bygget](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder emulatorn för lokal utveckling och testning finns i [Använda Azure Cosmos DB-emulatorn för lokal utveckling och testning](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Information om hur du exporterar SSL-certifikat för emulatorn finns i [Exportera Azure Cosmos DB emulatorcertifikat för användning med Java, Python och Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
