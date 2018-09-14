---
title: Konfigurera CI/CD-pipeline med Azure Cosmos DB-emulatorns build-uppgift
description: Självstudiekurs om hur du konfigurerar bygg och släpp-arbetsflöden i Visual Studio Team Services (VSTS) med Cosmos DB-emulatorns build-uppgift
services: cosmos-db
keywords: Azure Cosmos DB-emulatorn
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783882"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Konfigurera en CI/CD-pipeline med Azure Cosmos DB-emulatorns build-uppgift i Visual Studio Team Services

Azure Cosmos DB-emulatorn ger en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utveckling. Med emulatorn kan du utveckla och testa programmet lokalt, utan att skapa en Azure-prenumeration och utan att det kostar något. 

Med Azure Cosmos DB-emulatorns build-uppgift för Visual Studio Team Services (VSTS) kan du göra samma sak i en CI-miljö. Med build-uppgiften kan du köra tester mot emulatorn som en dela av dina bygg och släpp-arbetsflöden. Uppgiften startar en Docker-container där emulatorn redan körs och ger en slutpunkt som kan användas av resten av build-definitionen. Du kan skapa och starta så många instanser av emulatorn som du behöver, där var och en körs i en separat container. 

Den här artikeln visar hur du konfigurerar en CI-pipeline i VSTS för en ASP.NET-app som använder Cosmos DB-emulatorns build-uppgift för att köra tester. 

## <a name="install-the-emulator-build-task"></a>Installera emulatorns build-uppgift

För att använda build-uppgiften måste vi först installera den i VSTS-organisationen. Leta reda på tillägget **Azure Cosmos DB-emulator** i [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) och klicka på **Get it free** (Skaffa det kostnadsfritt).

![Hitta och installera Azure Cosmos DB-emulatorns build-uppgift i VSTS Marketplace](./media/tutorial-setup-ci-cd/addExtension_1.png)

Välj sedan den organisation där du vill installera tillägget. 

> [!NOTE]
> Om du vill installera ett tillägg i en VSTS-organisation måste du vara kontoägare eller projektsamlingsadministratör. Om du saknar behörigheter, men du är kontomedlem, kan du istället begära tillägg. [Läs mer.](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Välj en VSTS-organisation där du vill installera ett tillägg](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Skapa en build-definition

Nu när tillägget har installerats måste vi lägga till det i en [build-definition](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav). Du kan ändra en befintlig build-definition eller skapa en ny. Om du redan har en befintlig byggdefinition kan du hoppa fram till [Lägga till emulatorns build-uppgift i en build-definition](#addEmulatorBuildTaskToBuildDefinition).

Du kan skapa en ny build-definition genom att gå till fliken **Build and Release** (Bygg och släpp) i VSTS. Välj **+New** (Ny).

![Skapa en ny build-definition](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Välj teamprojekt, lagringsplats och gren för att aktivera byggen. 

![Välj teamprojekt, lagringsplats och gren för build-definitionen ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Välj slutligen den mall du vill använda för build-definitionen. Vi väljer **ASP.NET**-mallen i den här kursen. 

![Välja mall för build-definition ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Nu har vi en build-definition som vi kan konfigurera att använda Azure Cosmos DB-emulatorns build-uppgift som ser ut som nedan. 

![ASP.NET-mall för build-definition](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Lägga till uppgiften i en build-definition

Lägg till emulatorns build-uppgift genom att söka efter **cosmos** i sökrutan och välja **Lägg till**. Build-uppgiften startar en container med en instans av Cosmos DB-emulatorn som redan körs, så att uppgiften måste placeras före alla andra uppgifter som förväntar att emulatorn körs.

![Lägga till emulatorns build-uppgift i build-definitionen](./media/tutorial-setup-ci-cd/addExtension_3.png) I den här självstudiekursen lägger vi till uppgiften i början av fas 1 för att se till att emulatorn är tillgänglig innan våra tester körs.
Den slutförda build-definitionen ser nu ut så här. 

![ASP.NET-mall för build-definition](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurera tester för att använda emulatorn
Nu kan konfigurerar vi våra tester för att använda emulatorn. Emulatorns build-uppgift exporterar en miljövariabel – CosmosDbEmulator.Endpoint – som uppgifter längre in i bygg-pipeline kan utfärda begäranden mot. 

I den här självstudiekursen använder vi [Visual Studio Test-uppgiften](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md) för att köra enhetstester konfigurerade via en **.runsettings**-fil. Mer information om konfiguration av enhetstester finns i [dokumentationen](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

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

Gå till körningsalternativen i Visual Studio-Test-uppgiften. I alternativet **Settings file** (Inställningsfil) anger du att testerna har konfigurerats med **.runsettings**-filen. I alternativet **Override test run parameters** (Åsidosätt testkörningsparametrar) lägger du till i ` -endpoint $(CosmosDbEmulator.Endpoint)`. När du gör det konfigureras Test-uppgiften så att den refererar till emulatorns build-uppgift, istället för den som definieras i **.runsettings**-filen.  

![Åsidosätt slutpunktsvariabeln med slutpunkten för emulatorns build-uppgift](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Kör bygget
Spara och köplacera nu bygget. 

![Spara och kör bygget](./media/tutorial-setup-ci-cd/runBuild_1.png)

Observera när bygget har startats att Cosmos DB-emulatoruppgiften har börjat hämta Docker-avbildningen med emulatorn installerad. 

![Spara och kör bygget](./media/tutorial-setup-ci-cd/runBuild_4.png)

Observera när bygget har slutförts att testet godkänns, och att allt körs mot Cosmos DB-emulatorn från build-uppgiften!

![Spara och kör bygget](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder emulatorn för lokal utveckling och testning finns i [Använda Azure Cosmos DB-emulatorn för lokal utveckling och testning](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Information om hur du exporterar SSL-certifikat för emulatorn finns i [Exportera Azure Cosmos DB emulatorcertifikat för användning med Java, Python och Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
