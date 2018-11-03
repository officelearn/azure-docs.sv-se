---
title: Konfigurera roller för en Azure-molntjänst med Visual Studio | Microsoft Docs
description: Lär dig mer om att installera och konfigurera roller för Azure cloud services med hjälp av Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 4405476a2f006ca8e4e565a8cdd2f6c12e2ed684
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969477"
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Konfigurera Azure molntjänstroller med Visual Studio
En Azure-molntjänst kan ha en eller flera worker eller web-roller. För varje roll måste du definiera hur rollen ställs in och även konfigurera hur rollen körs. Mer information om roller i cloud services, se videon [introduktion till Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Informationen för din molntjänst lagras i följande filer:

- **ServiceDefinition.csdef** -tjänstdefinitionsfilen definierar körningsinställningar för din molntjänst, inklusive vilka roller är obligatoriska, slutpunkter och VM-storlek. Ingen av de data som lagras i `ServiceDefinition.csdef` kan ändras när du kör din roll.
- **ServiceConfiguration.cscfg** – tjänstkonfigurationsfilen anger hur många instanser av en roll körs och värdena för inställningarna som definierats för en roll. Data som lagras i `ServiceConfiguration.cscfg` kan ändras när din roll körs.

Du kan definiera flera konfigurationer för att lagra olika värden för de inställningar som styr hur en roll körs. Du kan använda en annan tjänstkonfiguration för varje distributionsmiljö. Du kan till exempel ange din anslutningssträng för lagringskonto att använda lokala Azure storage-emulatorn i en lokal tjänst-konfiguration och skapa en annan tjänstkonfiguration för att använda Azure storage i molnet.

När du skapar en Azure cloud Services i Visual Studio kan skapas och läggs till din Azure-projekt automatiskt två konfigurationer:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Konfigurera en Azure-molntjänst
Du kan konfigurera en Azure-molntjänst från Solution Explorer i Visual Studio som du ser i följande steg:

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn väljer **egenskaper**.
   
    ![Snabbmenyn för projektet i Solution Explorer](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Egenskapssidan för projektet, Välj den **utveckling** fliken. 

    ![Projektet egenskapssidan - utveckling-fliken](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. I den **tjänstkonfiguration** väljer du namnet på tjänstens konfiguration som du vill redigera. (Om du vill göra ändringar i alla tjänstkonfiguration för den här rollen, Välj **alla konfigurationer**.)
   
    > [!IMPORTANT]
    > Om du väljer en specifik tjänst-konfiguration kan har vissa egenskaper inaktiverats eftersom de kan bara anges för alla konfigurationer. Om du vill redigera dessa egenskaper, måste du välja **alla konfigurationer**.
    > 
    > 
   
    ![Tjänstkonfigurationen listan för en Azure cloud Services](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Ändra antalet rollinstanser
Du kan ändra antalet instanser av en roll som körs, baserat på antalet användare eller belastningen som förväntat för en viss roll för att förbättra prestanda för din molntjänst. En separat virtuell dator skapas för varje instans av en roll när Molntjänsten som körs i Azure. Detta påverkar faktureringen för distributionen av den här Molntjänsten. Läs mer om fakturering, [förstå fakturan för Microsoft Azure](billing/billing-understand-your-bill.md).

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på den roll som du vill uppdatera och på snabbmenyn väljer **egenskaper**.

    ![Solution Explorer Azure rollen snabbmenyn](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **Configuration** fliken.

    ![Konfigurationsfliken](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen av som du vill uppdatera.
   
    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. I den **antal instanser** text anger antalet instanser som du vill börja för den här rollen. Varje instans som körs på en separat virtuell dator när du publicerar Molntjänsten till Azure.

    ![Uppdatering av antalet instanser](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Från Visual Studio, verktygsfältet och väljer **spara**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Hantera anslutningssträngar för storage-konton
Du kan lägga till, ta bort eller ändra anslutningssträngar för din tjänstkonfiguration. Exempelvis kan du vilja ha en lokal anslutningssträng för en lokal tjänst-konfiguration som har värdet `UseDevelopmentStorage=true`. Du kanske också vill konfigurera en molntjänstkonfiguration som använder ett lagringskonto i Azure.

> [!WARNING]
> När du anger Azure storage-konto viktig information för en anslutningssträng för lagringskonto, lagras den här informationen lokalt i tjänstekonfigurationsfilen. Men den här informationen lagras för närvarande inte som krypterade text.
> 
> 

Med ett annat värde för varje tjänstkonfigurationen kan behöver du inte använda olika anslutningssträngar i din molntjänst eller ändra koden när du publicerar din molntjänst till Azure. Du kan använda samma namn för anslutningssträngen i din kod och värdet är olika, baserat på konfigurationen av som du väljer när du skapar din molntjänst eller när du publicerar den.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på den roll som du vill uppdatera och på snabbmenyn väljer **egenskaper**.

    ![Solution Explorer Azure rollen snabbmenyn](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **inställningar** fliken.

    ![Inställningsfliken](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen av som du vill uppdatera.

    ![Tjänstkonfiguration](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Om du vill lägga till en anslutningssträng, Välj **Lägg till inställning**.

    ![Lägg till anslutningssträng](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. När den nya inställningen har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny anslutningssträng](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Namn på** -ange det namn som du vill använda för anslutningssträngen.
    - **Typ** – Välj **Connection String** från den nedrullningsbara listan.
    - **Värdet** -du kan antingen ange anslutningssträngen direkt till den **värdet** cell, eller välj ellipsen (...) i den **skapa Lagringsanslutningssträng** dialogrutan.  

1. I den **skapa Lagringsanslutningssträng** dialogrutan väljer du ett alternativ för **ansluta med hjälp av**. Följ anvisningarna för vilket alternativ du väljer:

    - **Microsoft Azure storage-emulatorn** -om du väljer det här alternativet om de återstående inställningarna i dialogrutan har inaktiverats eftersom de endast tillämpas på Azure. Välj **OK**.
    - **Din prenumeration** – om du väljer det här alternativet använder den nedrullningsbara listan att antingen välja och logga in på ett Microsoft-konto eller Lägg till ett Microsoft-konto. Välj ett Azure-prenumeration och storage-konto. Välj **OK**.
    - **Angett autentiseringsuppgifterna manuellt** -ange lagringskontonamnet och den primära eller andra nyckeln. Välj ett alternativ för **anslutning** (HTTPS rekommenderas för de flesta scenarier.) Välj **OK**.

1. Välj anslutningssträngen för att ta bort en anslutningssträng, och därefter **ta bort inställningen**.

1. Från Visual Studio, verktygsfältet och väljer **spara**.

## <a name="programmatically-access-a-connection-string"></a>Via programmering komma åt en anslutningssträng

Följande steg visar hur programmatisk åtkomst till en anslutningssträng med C#.

1. Lägg till följande med hjälp av direktiv till en C#-fil där du tänker använda inställningen:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Följande kod visar ett exempel på hur du kommer åt en anslutningssträng. Ersätt den &lt;ConnectionStringName > med lämpligt värde. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Lägga till anpassade inställningar ska användas i din Azure-molntjänst
Anpassade inställningar i tjänstekonfigurationsfilen kan du lägga till ett namn och värde för en sträng för en specifik tjänst-konfiguration. Du kan välja att använda den här inställningen för att konfigurera en funktion i din molntjänst genom att läsa av värdet för inställningen och använder det här värdet för att styra logiken i din kod. Du kan ändra dessa värden för konfiguration av tjänsten utan att behöva återskapa ditt abonnemang eller när du kör din molntjänst. Din kod kan söka efter meddelanden om när en inställningen ändras. Se [RoleEnvironment.Changing händelse](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Du kan lägga till, ta bort eller ändra anpassade inställningar för din tjänstkonfiguration. Du kanske vill olika värden för de här strängarna för olika konfigurationer.

Med ett annat värde för varje tjänstkonfigurationen kan behöver du inte använda olika strängar i din molntjänst eller ändra koden när du publicerar din molntjänst till Azure. Du kan använda samma namn för strängen i din kod och värdet är olika, baserat på konfigurationen av som du väljer när du skapar din molntjänst eller när du publicerar den.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på den roll som du vill uppdatera och på snabbmenyn väljer **egenskaper**.

    ![Solution Explorer Azure rollen snabbmenyn](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **inställningar** fliken.

    ![Inställningsfliken](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen av som du vill uppdatera.

    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Om du vill lägga till en anpassad inställning, Välj **Lägg till inställning**.

    ![Lägg till anpassad inställning](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. När den nya inställningen har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny anpassad inställning](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Namn på** – ange namnet på inställningen.
    - **Typ** – Välj **sträng** från den nedrullningsbara listan.
    - **Värdet** – ange värdet för inställningen. Du kan antingen ange värde direkt i den **värdet** celler eller välj ellipsen (...) för att ange värdet i den **Redigera sträng** dialogrutan.  

1. Om du vill ta bort en anpassad inställning, Välj inställningen och därefter **ta bort inställningen**.

1. Från Visual Studio, verktygsfältet och väljer **spara**.

## <a name="programmatically-access-a-custom-settings-value"></a>Programmässigt få åtkomst till en anpassad inställningens värde
 
Följande steg visar hur programmatisk åtkomst till en anpassad inställning med C#.

1. Lägg till följande med hjälp av direktiv till en C#-fil där du tänker använda inställningen:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Följande kod visar ett exempel på hur du kommer åt en anpassad inställning. Ersätt den &lt;SettingName > med lämpligt värde. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Hantera lokal lagring för varje rollinstans
Du kan lägga till lagringsutrymme för lokala filsystem för varje instans av en roll. Data som lagras i denna lagring inte kan nås av andra instanser av rollen för vilket data lagras eller genom att andra roller.  

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på den roll som du vill uppdatera och på snabbmenyn väljer **egenskaper**.

    ![Solution Explorer Azure rollen snabbmenyn](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **lokal lagring** fliken.

    ![Fliken lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. I den **tjänstkonfiguration** Kontrollera att **alla konfigurationer** är markerad som lokal lagring-inställningar gäller för alla tjänstkonfigurationer av. Ett annat värde resulterar i inmatningsfält på sidan håller på att inaktiveras. 

    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Om du vill lägga till en post för lokal lagring, Välj **Lägg till lokal lagring**.

    ![Lägg till lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. När den nya posten för lokal lagring har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny post för lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Namn på** -ange det namn som du vill använda för det nya lokala lagringsutrymmet.
    - **Storlek (MB)** -Ange storlek i MB som ska användas för den nya lokal lagringen.
    - **Rensa på återanvändning av roll** – Välj det här alternativet för att ta bort data i den nya lokal lagringen när den virtuella datorn för rollen återanvänds.

1. Om du vill ta bort en post för lokal lagring, Välj posten och välj sedan **ta bort lokal lagring**.

1. Från Visual Studio, verktygsfältet och väljer **spara**.

## <a name="programmatically-accessing-local-storage"></a>Programmässigt få åtkomst till lokal lagring

Det här avsnittet illustrerar hur programmatisk åtkomst till lokal lagring med C# genom att skriva en test-textfil `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Skriv en textfil till lokal lagring

Följande kod visar ett exempel på hur du skriver en textfil till lokal lagring. Ersätt den &lt;LocalStorageName > med lämpligt värde. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Hitta en fil som skrivs till lokal lagring

Följ dessa steg om du vill visa filen som skapas av koden i föregående avsnitt:
    
1.  I meddelandefältet Windows högerklickar du på ikonen för Azure, och, från snabbmenyn väljer **visa Compute Emulator UI**. 

    ![Visa Azure-beräkningsemulatorn](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Välj webbrollen.

    ![Azure-beräkningsemulatorn](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. På den **Microsoft Azure Compute Emulator** menyn och välj **verktyg** > **öppna lokala arkivet**.

    ![Öppna lokala arkivet menyalternativ](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. När Windows Explorer-fönster öppnas, anger du ”MyLocalStorageTest.txt'' i den **Search** textrutan och välj **RETUR** sökningen ska startas. 

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure-projekt i Visual Studio genom att läsa [konfigurerar ett Azure-projekt](vs-azure-tools-configuring-an-azure-project.md). Läs mer om cloud service-schemat genom att läsa [Schemareferens](https://msdn.microsoft.com/library/azure/dd179398).

