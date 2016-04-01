<!--author=SharS last changed: 12/01/15-->

### So konfigurieren und registrieren Sie das Gerät

1. Greifen Sie an der seriellen Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Finden Sie unter [Verwenden von PuTTY zum Herstellen der seriellen Konsole des Geräts](#use-putty-to-connect-to-the-device-serial-console) Anweisungen. **Achten Sie darauf, dass Sie die Vorgehensweise genau befolgen. Andernfalls sind Sie nicht in der Lage, auf die Konsole zuzugreifen.**

2. Drücken Sie in der Sitzung, die geöffnet wird, einmal die EINGABETASTE, um eine Eingabeaufforderung zu öffnen. 

3. Sie werden aufgefordert, die Sprache auszuwählen, die für Ihr Gerät festgelegt werden soll. Geben Sie die Sprache an, und drücken Sie dann die EINGABETASTE. 

    ![StorSimple – Konfigurieren und Registrieren des Geräts 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)

4. Wählen Sie im Menü der seriellen Konsole, das angezeigt wird, Option 1 aus, d. h. die Anmeldung mit Vollzugriff. 

    ![StorSimple – Registrieren des Geräts 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
  
5. Führen Sie die folgenden Schritte aus, um die mindestens erforderlichen Netzwerkeinstellungen für Ihr Gerät zu konfigurieren.

    > [AZURE.IMPORTANT] Diese Konfigurationsschritte müssen für den aktiven Controller des Geräts ausgeführt werden. Das Menü der seriellen Konsole gibt den Zustand des Controllers in der Bannermeldung an. Wenn Sie nicht mit dem aktiven Controller verbunden sind, trennen Sie die Verbindung, und stellen Sie dann eine Verbindung mit dem aktiven Controller her.

      1. Geben Sie an der Eingabeaufforderung Ihr Kennwort ein. Das Standardkennwort für das Gerät ist **Kennwort1**.

      2. Geben Sie den folgenden Befehl ein:

           `Invoke-HcsSetupWizard`

      3. Es wird ein Installations-Assistent angezeigt, der Sie beim Konfigurieren der Netzwerkeinstellungen für das Gerät unterstützt. Geben Sie die folgenden Informationen an: 

       - IP address for DATA 0 network interface
       - Subnet mask
       - Gateway
       - IP address for Primary DNS server
       - IP address for Primary NTP server
 
        > [AZURE.NOTE] You may have to wait for a few minutes for the subnet mask and DNS settings to be applied. 

      4. (Optional) Konfigurieren Sie Ihren Webproxyserver.

      > [AZURE.IMPORTANT] Webproxy-Konfiguration ist, zwar optional Bedenken Sie, dass wenn Sie einen Webproxy verwenden, nur hier konfiguriert werden können. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](storsimple-configure-web-proxy.md). 

6. Drücken Sie STRG+C, um den Setup-Assistenten zu beenden.
 
7. Installieren Sie die Updates wie folgt:
      1. Verwenden Sie das folgende Cmdlet, um IP-Adressen für beide Controller festzulegen:

         `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`

      2. Führen Sie an der Eingabeaufforderung Folgendes aus: `Get-HcsUpdateAvailability`. Sie sollten eine Benachrichtigung über Updates erhalten.

      3. Führen Sie `Start-HcsUpdate` aus. Sie können diesen Befehl auf einem beliebigen Knoten ausführen. Die Updates werden auf dem ersten Domänencontroller angewendet, und für den Controller wird ein Failover ausgeführt. Anschließend werden die Updates auf dem anderen Domänencontroller angewendet.

      Sie können den Fortschritt des Updateauftrags verfolgen, indem Sie `Get-HcsUpdateStatus` ausführen.    

       The following sample output shows the update in progress.
  
        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     Die folgende Beispielausgabe zeigt an, dass das Update abgeschlossen ist.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

      Es kann bis zu elf Stunden dauern, bis alle Updates einschließlich der Windows-Updates angewendet wurden.

8. Nachdem alle Updates erfolgreich installiert wurden, führen Sie das folgende Cmdlet aus, um zu überprüfen, ob die Softwareupdates richtig angewendet wurden:

     `Get-HcsSystem`

    Daraufhin sollten die folgenden Versionen angezeigt werden.
    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433
 
9. Führen Sie das folgende Cmdlet aus, um zu überprüfen, ob das Firmwareupdate ordnungsgemäß angewendet wurde:

    `Start-HcsFirmwareCheck`.

     Der firmwarestatus sollte **Stand**.

10. Führen Sie das folgende Cmdlet aus, um das Gerät auf das Microsoft Azure-Portal für Behörden zu verweisen. (Es wird standardmäßig auf das öffentliche klassische Azure-Portal verwiesen.) Beide Controller werden dadurch neu gestartet. Es wird empfohlen, dass Sie zwei PuTTY-Sitzungen verwenden, um gleichzeitig eine Verbindung mit beiden Controllern herzustellen, sodass Sie sehen können, wenn jeder Controller neu gestartet wird.

     `Set-CloudPlatform -AzureGovt_US`

    Es wird eine Bestätigungsmeldung angezeigt. Übernehmen Sie den Standardnamen (**Y**).

11. Führen Sie das folgende Cmdlet aus, um die Installation fortzusetzen:

     `Invoke-HcsSetupWizard`

     ![Fortsetzen des Installations-Assistenten](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)

    Wenn Sie das Setup fortsetzen, wird die Assistenten-Version aus Update 1 verwendet (entspricht Version 17469). 

12. Akzeptieren Sie die Netzwerkeinstellungen. Es wird eine Validierungsmeldung angezeigt, nachdem Sie alle Einstellungen akzeptiert haben.
 
13. Aus Sicherheitsgründen läuft das Administratorkennwort für das Gerät nach der ersten Sitzung ab, und Sie müssen es jetzt ändern. Geben Sie, wenn Sie dazu aufgefordert werden, ein Administratorkennwort für das Gerät an. Ein gültiges Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. Das Kennwort muss aus drei der folgenden Zeichenkategorien bestehen: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen.

    <br/>![StorSimple – Registrieren des Geräts 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)

14. Der letzte Schritt im Installations-Assistenten besteht im Registrieren Ihres Geräts beim StorSimple-Manager-Dienst. Hierzu benötigen Sie den Dienstregistrierungsschlüssel, die Sie in erhalten [Schritt2: Abrufen des dienstregistrierungsschlüssels](storsimple-get-service-registration-key-gov.md). Nachdem Sie den Registrierungsschlüssel bereitgestellt haben, müssen Sie ggf. einige Minuten warten, bis das Gerät registriert wurde. 

      > [AZURE.NOTE] Sie können STRG + C drücken, jederzeit aufrufen, um den Assistenten zu beenden. Wenn Sie alle Netzwerkeinstellungen (IP-Adresse für DATA 0, Subnetzmaske und Gateway) angegeben haben, werden Ihre Einträge beibehalten.

    ![Fortschritt der StorSimple-Registrierung](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)

15. Nachdem das Gerät registriert wurde, wird ein Schlüssel für die Dienstdatenverschlüsselung angezeigt. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. **Dieser Schlüssel ist mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte bei StorSimple-Manager-Dienst erforderlich.** Finden Sie unter [StorSimple-Sicherheit](../articles/storsimple/storsimple-security.md) für Weitere Informationen zu diesem Schlüssel.
    
    ![StorSimple – Registrieren des Geräts 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    

      > [AZURE.IMPORTANT] Um den Text aus dem Fenster der seriellen Konsole zu kopieren, markieren Sie den Text einfach. Sie sollten damit in der Lage sein, ihn in die Zwischenablage oder einen beliebigen Texteditor zu kopieren. 
      > 
      > Verwenden Sie NICHT STRG+C zum Kopieren des Schlüssels für die Dienstdatenverschlüsselung. STRG+C bewirkt, dass der Installations-Assistent beendet wird. Dies führt dazu, dass das Geräteadministratorkennwort nicht geändert und das Gerät auf das Standardkennwort zurückgesetzt wird.

16. Schließen Sie die serielle Konsole.

17. Kehren Sie zum Azure-Portal für Behörden zurück, und führen Sie die folgenden Schritte aus:
  1. Doppelklicken Sie auf dem StorSimple-Manager-Dienst für den Zugriff auf die **Schnellstart** Seite.
  2. Klicken Sie auf **Anzeigen verbundener Geräte**.
  3. Auf der **Geräte** sicher, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status. Der Status des Geräts muss **Online**.
   
        ![StorSimple – Seite "Geräte"](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
  
        Wenn der Gerätestatus **Offline**, warten Sie einige Minuten, bis das Gerät online geschaltet werden kann. 
      
        Wenn das Gerät nach einigen Minuten immer noch offline ist, müssen Sie sicherstellen, dass Ihre Firewall-Netzwerk konfiguriert wurde, wie in beschrieben die [netzwerkanforderungen für Ihr StorSimple-Gerät](https://msdn.microsoft.com/library/dn772371.aspx). Wenn HTTP 1.1 nicht unterstützt wird, überprüfen Sie Port 9354 um sicherzustellen, dass er für die ausgehende Kommunikation geöffnet ist. Dieser Port wird für die Kommunikation zwischen dem StorSimple Manager-Dienst und Ihrem StorSimple-Gerät verwendet.
     
        


