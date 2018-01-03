## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. Den lokala installationen av Integration Runtime är den komponent som kopierar data från SQL Server på din dator till Azure Blob Storage. 

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och anteckna det. Du använder det senare i den här självstudien. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Skapa Integration Runtime med egen värd. 

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
 
3. Kör följande kommando för att hämta statusen för din skapade Integration Runtime. Kontrollera att värdet för egenskapen **State** är inställd på **NeedRegistration**. 

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

4. Kör följande kommando som används för att hämta autentiseringsnycklarna för att registrera en lokal Integration Runtime med Azure Data Factory-tjänsten i molnet: 

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

5. Kopiera en av nycklarna (uteslut de dubbla citattecknen) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i följande steg.  

## <a name="install-the-integration-runtime"></a>Installera Integration Runtime
1. Om du redan har Integration Runtime på din dator ska du avinstallera det med **Lägg till eller ta bort program**. 

2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator. Kör installationen.

3. På sidan **Välkommen till Microsoft Integration Runtime** klickar du på **Nästa**.

4. På sidan med **licensavtalet för slutanvändare** godkänner du villkoren och väljer du **Nästa**.

5. På sidan **Målmapp** väljer du **Nästa**.

6. På sidan **Klar att installera Microsoft Integration Runtime** väljer du **Installera**.

7. Om du ser ett varningsmeddelande om att konfigurera datorn att övergå i strömsparläge eller viloläge när den inte används, väljer du **OK**.

8. Om du ser sidan **Energialternativ** stänger du den och gå till konfigurationssidan.

9. På sidan **Slutfört installationen av Microsoft Integration Runtime**  väljer du **Slutför**.

10. Klistra in den nyckel som du sparade i föregående avsnitt på sidan **Registrera Integration Runtime (lokal installation)** och välj **Registrera**. 

    ![Registrera Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. När integration runtime med egen värd har registrerats ser du följande meddelande:

    ![Registered successfully (Registrerat)](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. På sidan **Ny nod för Integration Runtime (lokal installation)** väljer du **Nästa**. 

    ![Sidan Ny nod för Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. På sidan **Kommunikationskanal för intranät** väljer du **Hoppa över**. Välj ett TLS/SSL-certifikat för att skydda kommunikationen mellan noderna i en miljö med flera noder för Integration Runtime. 

    ![Sidan Kommunikationskanal för intranät](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. På sidan **Registrera Integration Runtime (lokal installation)** väljer du **Starta konfigurationshanteraren**.

15. När noden är ansluten till molntjänsten ser du följande sida:

    ![Sidan Noden är ansluten](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Testa nu anslutningen till din SQL Server-databasen.

    ![Fliken Diagnostik](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. I fönstret **Konfigurationshanteraren** växlar du till fliken **Diagnostik**.

    b. Välj**SqlServer** som typ av datakälla.

    c. Ange servernamnet.

    d. Ange namnet på databasen.

    e. Välj autentiseringsläge.

    f. Ange användarnamnet.

    g. Ange lösenordet för användarnamnet.

    h. Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server. Du ser en grön bockmarkering om anslutningen är klar. Om anslutningen inte lyckats får du ett felmeddelande. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till SQL Server.    

    > [!NOTE]
    > Anteckna värdena för autentiseringstyp, server, databas, användare och lösenord. Du använder det senare i den här självstudien. 
    
