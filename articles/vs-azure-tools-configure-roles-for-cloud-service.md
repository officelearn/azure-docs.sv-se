---
title: "Konfigurera roller för en Azure-molntjänst med Visual Studio | Microsoft Docs"
description: "Lär dig hur du skapar och konfigurerar roller för Azure-molntjänster med Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 17da71ac0c5ab9330b9244c0354e4d161d98229e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Konfigurera roller för Azure cloud service med Visual Studio
En Azure-molntjänst kan ha en eller flera worker webbroller. För varje roll måste du definiera hur rollen har ställts in och även konfigurera hur rollen körs. Mer information om roller i molntjänster finns videon [introduktion till Azure Cloud Services](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Information för din molntjänst lagras i följande filer:

- **ServiceDefinition.csdef** -tjänstdefinitionsfilen definierar körningsinställningar för Molntjänsten, inklusive vilka roller är obligatoriska, slutpunkter och storlek på virtuell dator. Ingen av de data som lagras i `ServiceDefinition.csdef` kan ändras när den körs.
- **ServiceConfiguration.cscfg** - tjänstkonfigurationsfilen konfigurerar hur många instanser av en roll körs och värden för inställningarna som har definierats för en roll. Data som lagras i `ServiceConfiguration.cscfg` kan ändras när din roll körs.

Du kan definiera flera konfigurationer för att lagra olika värden för de inställningar som styr hur en roll körs. Du kan använda en annan tjänstkonfiguration för varje distributionsmiljö. Du kan till exempel ange anslutningssträngen för lagring kontot du använder lokala Azure storage-emulatorn i en lokal tjänst-konfiguration och skapa en annan tjänstkonfiguration om du vill använda Azure storage i molnet.

När du skapar en Azure-molntjänst i Visual Studio, skapas och läggs till din Azure-projekt automatiskt två konfigurationer:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Konfigurera en Azure-molntjänst
Du kan konfigurera en Azure-molntjänst från Solution Explorer i Visual Studio enligt följande steg:

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn, Välj **egenskaper**.
   
    ![Snabbmenyn för Solution Explorer-projekt](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Välj i projektets egenskapssida i **Development** fliken. 

    ![Projektet egenskapssidan - utveckling fliken](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. I den **tjänstkonfiguration** väljer du namnet på tjänstkonfigurationen som du vill redigera. (Om du vill göra ändringar i alla tjänstkonfiguration för den här rollen, välja **alla konfigurationer av**.)
   
    > [!IMPORTANT]
    > Om du väljer en specifik tjänstkonfiguration har vissa egenskaper inaktiverats eftersom de kan bara anges för alla konfigurationer. Om du vill redigera dessa egenskaper, måste du välja **alla konfigurationer av**.
    > 
    > 
   
    ![Tjänstkonfigurationen listan för en Azure-molntjänst](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Ändra antalet rollinstanser
Du kan ändra antalet instanser av en roll som körs, baserat på antalet användare eller belastning som förväntas för en viss roll för att förbättra prestandan för din tjänst i molnet. En separat virtuell dator skapas för varje instans av rollen när Molntjänsten som körs i Azure. Detta påverkar fakturering för distribution av den här Molntjänsten. Mer information om fakturering finns [förstå fakturan för Microsoft Azure](billing/billing-understand-your-bill.md).

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på rollen du vill uppdatera och på snabbmenyn Välj **egenskaper**.

    ![Snabbmenyn för Solution Explorer Azure roll](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **Configuration** fliken.

    ![Konfigurationsfliken](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen för tjänsten som du vill uppdatera.
   
    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. I den **instansen antal** text anger antalet instanser som du vill starta för den här rollen. Varje instans som körs på en separat virtuell dator när du publicerar Molntjänsten på Azure.

    ![Uppdatera Instansantalet](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Från Visual Studio i verktygsfältet väljer **spara**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Hantera anslutningssträngar för storage-konton
Du kan lägga till, ta bort eller ändra anslutningssträngar för service-konfigurationer. Du kan exempelvis lokala anslutningssträngen för en lokal tjänst-konfiguration som har värdet `UseDevelopmentStorage=true`. Du kan också konfigurera en tjänst molnkonfiguration som använder ett lagringskonto i Azure.

> [!WARNING]
> När du registrerar Azure storage-konto viktig information för en anslutningssträng för storage-konto, lagras informationen lokalt i tjänstekonfigurationsfilen. Men den här informationen lagras för närvarande inte som krypterad text.
> 
> 

Med hjälp av ett annat värde för varje tjänstkonfiguration behöver du inte använda olika anslutningssträngar i din tjänst i molnet eller ändra koden när du publicerar din tjänst i molnet till Azure. Du kan använda samma namn för anslutningssträngen i koden och värdet är olika, baserat på konfigurationen av tjänsten som du väljer när du skapar din molntjänst eller när du publicerar den.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på rollen du vill uppdatera och på snabbmenyn Välj **egenskaper**.

    ![Snabbmenyn för Solution Explorer Azure roll](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **inställningar** fliken.

    ![Fliken Inställningar](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen för tjänsten som du vill uppdatera.

    ![Tjänstkonfiguration](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Om du vill lägga till en anslutningssträng, Välj **Lägg till inställning**.

    ![Lägg till anslutningssträng](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. När den nya inställningen har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny anslutningssträng](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Namnet** – ange namnet som du vill använda för anslutningssträngen.
    - **Typen** – Välj **anslutningssträngen** från den nedrullningsbara listan.
    - **Värdet** -kan du antingen ange anslutningssträngen direkt i den **värdet** celler eller välj ellips (...) att arbeta i den **skapa Lagringsanslutningssträng** dialogrutan.  

1. I den **skapa Lagringsanslutningssträng** väljer ett alternativ för **ansluta med**. Följ sedan anvisningarna för det alternativ du väljer:

    - **Microsoft Azure storage-emulatorn** -om du väljer det här alternativet om de återstående inställningarna i dialogrutan har inaktiverats eftersom de tillämpas endast för Azure. Välj **OK**.
    - **Din prenumeration** - om du väljer det här alternativet använder den nedrullningsbara listan för att antingen välja och logga in på ett Microsoft-konto eller Lägg till ett Microsoft-konto. Välj ett Azure-prenumeration och storage-konto. Välj **OK**.
    - **Autentiseringsuppgifterna anges manuellt** -ange lagringskontonamn och den primära eller den andra nyckeln. Välj ett alternativ för **anslutning** (HTTPS rekommenderas för de flesta fall.) Välj **OK**.

1. Välj anslutningssträngen för att ta bort en anslutningssträng och välj sedan **ta bort inställningen**.

1. Från Visual Studio i verktygsfältet väljer **spara**.

## <a name="programmatically-access-a-connection-string"></a>Komma åt en anslutningssträng

Följande steg visar hur programmatisk åtkomst till en anslutningssträng med C#.

1. Lägg till följande med hjälp av direktiven till en C#-fil där du ska använda inställningen:

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

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Lägg till anpassade inställningar som ska användas i Azure-molntjänst
Anpassade inställningar i tjänstkonfigurationsfilen kan du lägga till ett namn och värde för en sträng för en specifik tjänst-konfiguration. Du kan välja att använda den här inställningen för att konfigurera en funktion i din molntjänst genom att läsa värdet för inställningen och använder det här värdet för att styra logiken i din kod. Du kan ändra dessa värden för konfiguration av tjänsten utan att behöva återskapa ditt abonnemang eller när Molntjänsten körs. Koden kan söka efter meddelanden om ändringar i en inställning. Se [RoleEnvironment.Changing händelse](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Du kan lägga till, ta bort eller ändra anpassade inställningar för din tjänstkonfiguration. Du kanske vill olika värden för de här strängarna för olika konfigurationer.

Med hjälp av ett annat värde för varje tjänstkonfiguration behöver du inte använda olika strängar i din tjänst i molnet eller ändra koden när du publicerar din tjänst i molnet till Azure. Du kan använda samma namn för strängen i koden och värdet är olika, baserat på konfigurationen av tjänsten som du väljer när du skapar din molntjänst eller när du publicerar den.

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på rollen du vill uppdatera och på snabbmenyn Välj **egenskaper**.

    ![Snabbmenyn för Solution Explorer Azure roll](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **inställningar** fliken.

    ![Fliken Inställningar](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. I den **tjänstkonfiguration** väljer du konfigurationen för tjänsten som du vill uppdatera.

    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Om du vill lägga till en anpassad inställning, Välj **Lägg till inställning**.

    ![Lägg till anpassad inställning](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. När den nya inställningen har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny anpassad inställning](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Namnet** – ange namnet på inställningen.
    - **Typen** – Välj **sträng** från den nedrullningsbara listan.
    - **Värdet** -ange värdet för inställningen. Du kan antingen ange värde direkt i den **värdet** cell eller markera ellips (...) för att ange värdet i den **Redigera sträng** dialogrutan.  

1. Välj inställning för att ta bort en anpassad inställning, och välj sedan **ta bort inställningen**.

1. Från Visual Studio i verktygsfältet väljer **spara**.

## <a name="programmatically-access-a-custom-settings-value"></a>Komma åt en inställningens värde
 
Följande steg visar hur programmatisk åtkomst till en anpassad inställning med C#.

1. Lägg till följande med hjälp av direktiven till en C#-fil där du ska använda inställningen:

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
Du kan lägga till lokala system fillagring för varje instans av en roll. De data som lagras i denna lagring inte är tillgänglig i andra instanser av rollen för vilket data lagras eller av andra roller.  

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, expandera projektnoden. Under den **roller** nod, högerklicka på rollen du vill uppdatera och på snabbmenyn Välj **egenskaper**.

    ![Snabbmenyn för Solution Explorer Azure roll](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Välj den **lokal lagring** fliken.

    ![Fliken lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. I den **tjänstkonfiguration** lista, se till att **alla konfigurationer av** är markerad som lokal Lagringsinställningar gäller för alla konfigurationer. Ett annat värde resulterar i indatafält på sidan håller på att inaktiveras. 

    ![Tjänstkonfigurationen lista](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Om du vill lägga till en post för lokal lagring, Välj **Lägg till lokal lagring**.

    ![Lägg till lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. När den nya posten för lokal lagring har lagts till i listan, uppdatera en rad i listan med nödvändig information.

    ![Ny post för lokal lagring](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Namnet** – ange namnet som du vill använda för den nya lokal lagringen.
    - **Storlek (MB)** -ange en storlek i MB som du behöver för den nya lagringsplatsen.
    - **Rensa på rollen återvinning** -Välj detta alternativ för att ta bort data i den nya lokal lagringen när den virtuella datorn för rollen återanvänds.

1. Om du vill ta bort en post för lokal lagring, Välj posten och välj sedan **ta bort lokal lagring**.

1. Från Visual Studio i verktygsfältet väljer **spara**.

## <a name="programmatically-accessing-local-storage"></a>Att komma åt lokal lagring

Det här avsnittet visar hur programmatisk åtkomst till lokal lagring med hjälp av C# genom att skriva en test-textfil `MyLocalStorageTest.txt`.  

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
    
1.  I meddelandefältet i Windows högerklickar du på ikonen för Azure och på snabbmenyn Välj **visa Compute Emulator UI**. 

    ![Visa Azure-beräkningsemulatorn](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Välj rollen webbserver.

    ![Azure-beräkningsemulatorn](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. På den **Microsoft Azure Compute Emulator** väljer du **verktyg** > **öppna lokalt Arkiv**.

    ![Öppna lokalt Arkiv menyobjekt](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. När fönstret Utforskaren öppnas, ange ' MyLocalStorageTest.txt'' i den **Sök** text och markera **RETUR** sökningen ska startas. 

## <a name="next-steps"></a>Nästa steg
Lär dig mer om Azure-projekt i Visual Studio genom att läsa [konfigurera ett Azure-projekt](vs-azure-tools-configuring-an-azure-project.md). Mer information om cloud service schemat genom att läsa [Schemareferens](https://msdn.microsoft.com/library/azure/dd179398).

