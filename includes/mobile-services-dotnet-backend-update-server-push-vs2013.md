Erstellen eine neue benutzerdefinierten [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673) dass an die app sendet Pushbenachrichtigungen. Sie können ähnlichen Code in Implementieren einer [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) oder an anderer Stelle in Ihren Back-End-Diensten. 

1. In Visual Studio Solution Explorer Maustaste auf den Ordner Controllers für das mobile Dienstprojekt, und erweitern Sie **Hinzufügen**, klicken Sie dann auf **neu erstelltes Element**.

    Der Dialog "Add Scaffold" wird angezeigt.

2. Erweitern Sie **Azure Mobile Services** und klicken Sie auf **Azure Mobile Services benutzerdefinierten Controller**, klicken Sie dann auf **Hinzufügen**, geben eine **Controllernamen** von `NotifyAllUsersController`, klicken Sie dann auf **Hinzufügen** erneut.

    ![Web-API-Dialogfeld "Add Scaffold"](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)

    Dies erstellt eine neue leere Controllerklasse namens **NotifyAllUsersController**. 

3. Fügen Sie der neuen Projektdatei "notifyalluserscontroller.cs" folgende **mit** Anweisungen:

        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;

4. Fügen Sie den folgenden Code ein, in dem die POST-Methode für den Controller implementiert ist:

        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<toast><visual><binding template=\"ToastText01\">" + 
                    "<text id=\"1\">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());

                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;

                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }

    >[AZURE.NOTE]Diese POST-Methode kann von jedem beliebigen Client aufgerufen werden, die den Anwendungsschlüssel verfügt, der nicht sicher ist. Fügen Sie zur Absicherung des Endpunkts der Methode oder Klasse das Attribut `[AuthorizeLevel(AuthorizationLevel.User)]` hinzu, um die Authentifizierung zu erzwingen. 


