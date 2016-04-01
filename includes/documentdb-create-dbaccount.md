1.  Melden Sie sich bei der online- [Microsoft Azure Preview-Portal](https://portal.azure.com/).
2.  Klicken Sie in der Navigationsleiste auf **Neu**, klicken Sie dann auf **Daten + Speicher**, und klicken Sie dann auf **Azure DocumentDB**. 
  
    ![Screenshot des Azure-vorschauportals zum Erstellen einer Datenbank, markieren die Schaltfläche "Neu", Daten + Speicher im Fenster erstellen und Azure DocumentDB in den Daten + Speicher Blatt](media/documentdb-create-dbaccount/ca1.png)  

3. In der **neue DocumentDB-Konto** Blatt geben Sie die gewünschte Konfiguration für das DocumentDB-Konto. 
 
    ![Screenshot des Blatts "Neue DocumentDB"](media/documentdb-create-dbaccount/ca3.png) 


    - In the **ID** box, enter a name to identify the DocumentDB account.  When the **ID** is validated, a green check mark appears in the **ID** box. The **ID** value becomes the host name within the URI. The **ID** may contain only lowercase letters, numbers, and the '-' character, and must be between 3 and 50 characters. Note that *documents.azure.com* is appended to the endpoint name you choose, the result of which will become your DocumentDB account endpoint.
    

    - The **Account Tier** lens is locked because DocumentDB supports a single standard account tier. For more information, see [DocumentDB pricing](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).
    
    - For **Subscription**, select the Azure subscription that you want to use for the DocumentDB account. If your account has only one subscription, that account is selected by default.

    - In **Resource Group**, select or create a resource group for your DocumentDB account.  By default, a new Resource group will be created.  You may, however, choose to select an existing resource group to which you would like to add your DocumentDB account. For more information, see [Using the Azure Preview Portal to manage your Azure resources](resource-group-portal.md).
 
    - Use **Location** to specify the geographic location in which to host your DocumentDB account.   

4.  Konfigurieren der Optionen für den neuen DocumentDB-Konto, und klicken Sie auf **Erstellen**.  Die Erstellung des DocumentDB-Kontos kann einige Minuten dauern.  Sie können den Fortschritt über das Startmenü überwachen, um den Status zu überprüfen.  
    ![Screenshot der Kachel "erstellen" im Startmenü – Online-Datenbankersteller](media/documentdb-create-dbaccount/ca4.png)  
  
    Sie können den Status auch über den Hub "Benachrichtigungen" überwachen.  

    ![Datenbanken schnell erstellen - Screenshot des Benachrichtigungs-Hubs, der zeigt, dass das DocumentDB-Konto erstellt wird](media/documentdb-create-dbaccount/ca5.png)  

    ![Screenshot des Benachrichtigungs-Hubs, der zeigt, dass das DocumentDB-Konto erfolgreich erstellt und in einer Ressourcengruppe bereitgestellt wurde – Benachrichtigung für Online-Datenbankersteller](media/documentdb-create-dbaccount/ca6.png)

5.  Nachdem das DocumentDB-Konto erstellt wurde, kann es mit den Standardeinstellungen im Onlineportal verwendet werden. Beachten Sie, dass die Standardkonsistenz des DocumentDB-Kontos wird auf **Sitzung**.  Sie können die Standardkonsistenz anpassen, indem Sie auf die **Standardkonsistenz** auf die Kachel der **DocumentDB-Konto** Blatt.

    ![Screenshot des Blatts "Ressourcengruppe" – Anwendungsentwicklung beginnen](media/documentdb-create-dbaccount/ca7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


