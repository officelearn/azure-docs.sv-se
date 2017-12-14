## <a name="create-a-self-hosted-ir"></a>Skapa IR med egen värd

I det här avsnittet kan du skapa en Integration Runtime med egen värd och koppla den till en lokal dator med SQL Server-databasen. Den lokala installationen av Integration Runtime är den komponent som kopierar data från SQL Server på din dator till Azure Blob Storage. 

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och notera namnet. Du använder det senare i den här självstudien. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Skapa Integration Runtime med egen värd. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Här är exempel på utdata:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Kör följande kommando för att hämta status för din skapade integration runtime. Kontrollera att värdet för egenskapen **State** är inställd på **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Här är exempel på utdata:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Kör följande kommando för att hämta **autentiseringsnycklarna** för att registrera Integration Runtime med tjänsten Data Factory i molnet. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Här är exempel på utdata:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Kopiera en av nycklarna (uteslut dubbla citattecken) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i nästa steg.  

## <a name="install-integration-runtime"></a>Installera Integration Runtime
1. Om du redan har **Microsoft Integration Runtime** på din dator ska du avinstallera det med **Lägg till eller ta bort program**. 
2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator och kör installationen. 
3. På **välkomstskärmen till installationsguiden för Microsoft Integration Runtime** klickar du på **Nästa**.  
4. På sidan med **licensavtalet** godkänner du villkoren och klickar på **Nästa**. 
5. På sidan **Målmapp** klickar du på **Nästa**. 
6. På sidan **Klar att installera Microsoft Integration Runtime** klickar du på **Installera**. 
7. Om du ser ett varningsmeddelande om att datorn håller på att konfigureras för att övergå i strömsparläge eller viloläge när den inte används, klickar du på **OK**. 
8. Om du ser fönstret **Energialternativ** stänger du det och växlar till konfigurationsfönstret. 
9. På sidan för att **installationsguiden för Microsoft Integration Runtime har slutförts** klickar du på **Slutför**.
10. Klistra in den nyckel som du sparade i föregående avsnitt på sidan **Registrera Integration Runtime (lokal)** och klicka på **Registrera**. 

   ![Registrera Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. När integration runtime med egen värd har registrerats ser du följande meddelande:

   ![Registered successfully (Registrerat)](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. På sidan **Ny nod för Integration Runtime (lokal)** klickar du på **Nästa**. 

    ![Sidan Ny nod för Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. På sidan **Kommunikationskanal för intranät** klickar du på **Hoppa över**. Du kan välja ett TLS/SSL-certifikat för att skydda kommunikationen mellan noderna i en miljö med flera noder för Integration Runtime. 

    ![Sidan Kommunikationskanal för intranät](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. På sidan **Registrera Integration Runtime (lokal)** klickar du på **Starta Konfigurationshanteraren**. 
6. Du ser följande sida när noden är ansluten till molntjänsten:

   ![Node is connected (Noden är ansluten)](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. Testa nu anslutningen till din SQL Server-databasen.

    ![Fliken Diagnostik](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - I fönstret **Configuration Manager** växlar du till fliken **Diagnostik**.
    - Välj**SqlServer** som **typ av datakälla**.
    - Ange **servernamnet**.
    - Ange namnet på **databasen**. 
    - Välj **autentiseringsmetod**. 
    - Ange **användarnamn**. 
    - Ange **lösenord** för användarnamnet.
    - Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server. Du ser en grön bockmarkering om anslutningen är klar. Annars ser du ett felmeddelande som är kopplat till felet. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till din SQL Server.    

    > [!NOTE]
    > Anteckna värdena (autentiseringstyp, server, databas, användare, lösenord). Du använder det senare i den här självstudien. 
    
