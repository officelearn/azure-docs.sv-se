<!--author=alkohli last changed: 12/01/15-->


#### So konfigurieren und registrieren Sie das Gerät

1. Greifen Sie an der seriellen Konsole Ihres StorSimple-Geräts auf die Windows PowerShell-Benutzeroberfläche zu. Finden Sie unter [Verwenden von PuTTY zum Herstellen der seriellen Konsole des Geräts](#use-putty-to-connect-to-the-device-serial-console) Anweisungen. **Achten Sie darauf, dass Sie die Vorgehensweise genau befolgen. Andernfalls sind Sie nicht in der Lage, auf die Konsole zuzugreifen.**

2. Drücken Sie in der Sitzung, die geöffnet wird, einmal die EINGABETASTE, um eine Eingabeaufforderung zu öffnen. 

3. Sie werden aufgefordert, die Sprache auszuwählen, die für Ihr Gerät festgelegt werden soll. Geben Sie die Sprache an, und drücken Sie dann die EINGABETASTE. 

    ![StorSimple – Konfigurieren und Registrieren des Geräts 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)

4. Wählen Sie im Menü der seriellen Konsole, das angezeigt wird, Option 1 aus, d. h. die Anmeldung mit Vollzugriff. 

    ![StorSimple – Registrieren des Geräts 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
  
     Führen Sie die Schritte 5 bis 12 aus, um die mindestens erforderlichen Netzwerkeinstellungen für Ihr Gerät zu konfigurieren. **Diese Konfigurationsschritte müssen für den aktiven Controller des Geräts ausgeführt werden.** Das Menü der seriellen Konsole gibt den Zustand des Controllers in der Bannermeldung an. Wenn Sie nicht mit dem aktiven Controller verbunden sind, trennen Sie die Verbindung, und stellen Sie dann eine Verbindung mit dem aktiven Controller her.

5. Geben Sie an der Eingabeaufforderung Ihr Kennwort ein. Das Standardkennwort für das Gerät ist **Kennwort1**.

6. Geben Sie den folgenden Befehl ein:

     `Invoke-HcsSetupWizard` 

7. Es wird ein Installations-Assistent angezeigt, der Sie beim Konfigurieren der Netzwerkeinstellungen für das Gerät unterstützt. Geben Sie die folgenden Informationen an: 
   - IP-Adresse für die Netzwerkschnittstelle DATA 0
   - Subnetzmaske
   - Gateway
   - IP-Adresse für den primären DNS-Server
   - IP-Adresse für den sekundären DNS-Server
   
      > [AZURE.NOTE] Sie müssen möglicherweise warten einige Minuten, bis die Subnetzmaske und die DNS-Einstellungen angewendet werden. Wenn Sie erhalten ein "das Gerät ist nicht bereit." Fehler, überprüfen Sie die physische Netzwerkverbindung für die Netzwerkschnittstelle DATA 0 Ihres aktiven Controllers.

8. (Optional) Konfigurieren Sie Ihren Webproxyserver. Webproxy-Konfiguration ist zwar optional, **Beachten Sie, dass wenn Sie einen Webproxy verwenden, nur hier konfiguriert werden können**. Weitere Informationen finden Sie unter [Konfigurieren des Webproxys für Ihr Gerät](storsimple-configure-web-proxy.md). Wenn Sie während dieses Schritts Probleme stoßen, finden Sie Hinweise zur Problembehandlung [Fehler bei der webproxykonfiguration](storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).
 

      > [AZURE.NOTE] Sie können STRG + C drücken, jederzeit aufrufen, um den Assistenten zu beenden. Alle Einstellungen, die Sie vor dem Ausgeben dieses Befehls angewendet haben, werden beibehalten.

9. Aus Sicherheitsgründen läuft das Standardadministratorkennwort für das Gerät nach der ersten Sitzung ab, und Sie müssen es für die nachfolgenden Sitzungen ändern. Geben Sie, wenn Sie dazu aufgefordert werden, ein Administratorkennwort für das Gerät an. Ein gültiges Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. Das Kennwort muss eine Kombination aus Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen enthalten.

10. Das Kennwort für den StorSimple-Momentaufnahme-Manager wird ebenfalls hier festgelegt. Verwenden Sie dieses Kennwort, wenn Sie ein Gerät bei Ihrem Windows-Host authentifizieren, der den StorSimple-Momentaufnahme-Manager ausführt. Wenn Sie dazu aufgefordert werden, geben Sie ein Kennwort ein, das 14 bis 15 Zeichen lang ist. Das Kennwort muss eine Kombination aus den folgenden Kategorien enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. 

    ![StorSimple – Registrieren des Geräts 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)

    Sie können das Kennwort für den StorSimple-Momentaufnahme-Manager auch über die Benutzeroberfläche des StorSimple-Manager-Diensts zurückzusetzen. Ausführliche Schritte finden Sie unter [Ändern Sie die StorSimple-Kennwörter, die mit dem StorSimple-Manager-Dienst](storsimple-change-passwords.md).

    Um Probleme zu beheben während dieses Schritts, finden Sie Hinweise zur Problembehandlung [Fehler in Bezug auf Kennwörter](storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).

11. Der letzte Schritt im Installations-Assistenten besteht im Registrieren Ihres Geräts beim StorSimple-Manager-Dienst. Zu diesem Zweck benötigen Sie den Dienstregistrierungsschlüssel, den Sie in Schritt 2 abgerufen haben. Nachdem Sie den Registrierungsschlüssel bereitgestellt haben, müssen Sie ggf. einige Minuten warten, bis das Gerät registriert wurde. 

    Um mögliche Gerät Registrierung Fehler zu beheben, finden Sie unter [Fehler bei der Registrierung des Geräts](storsimple-troubleshoot-deployment.md#errors-during-device-registration). Detaillierte Problembehandlung erhalten Sie auch auf [schrittweisen Anleitung zur Problembehandlung](storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).

12. Nachdem das Gerät registriert wurde, wird ein Schlüssel für die Dienstdatenverschlüsselung angezeigt. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf.
    
    > [AZURE.WARNING] Dieser Schlüssel wird mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte bei StorSimple-Manager-Dienst erforderlich sein. Finden Sie unter [StorSimple-Sicherheit](../articles/storsimple/storsimple-security.md) für Weitere Informationen zu diesem Schlüssel.

     ![StorSimple – Registrieren des Geräts 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)

     Markieren Sie zum Kopieren des Texts aus dem Fenster der seriellen Konsole den Text einfach. Sie sollten damit in der Lage sein, ihn in die Zwischenablage oder einen beliebigen Texteditor zu kopieren. Verwenden Sie NICHT STRG+C zum Kopieren des Schlüssels für die Dienstdatenverschlüsselung. STRG + C bewirkt, dass der Installations-Assistent beendet wird. Als Ergebnis werden das Geräteadministratorkennwort und das Kennwort des StorSimple-Momentaufnahme-Managers nicht geändert, und das Gerät wird auf die Standardkennwörter zurückgesetzt.

13. Schließen Sie die serielle Konsole.

14. Kehren Sie zum klassischen Azure-Portal zurück, und führen Sie die folgenden Schritte aus:
  1. Doppelklicken Sie auf dem StorSimple-Manager-Dienst für den Zugriff auf die **Schnellstart** Seite.
  2. Klicken Sie auf **Anzeigen verbundener Geräte**.
  3. Auf der **Geräte** sicher, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status. Der Status des Geräts muss **Online**. Wenn der Gerätestatus **Offline**, warten Sie einige Minuten, bis das Gerät online geschaltet werden kann.
   
    ![StorSimple Devices page](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
  
      > [AZURE.IMPORTANT] After the device is online, plug in the network cables that you had unplugged in the beginning of this step.

Wenn das Gerät erfolgreich registriert wurde, jedoch nicht online geschaltet wird, führen Sie `Test-HcsmConnection -Verbose` aus, um sicherzustellen, dass die Netzwerkverbindungen funktionieren. Die ausführliche Informationen zur Nutzung dieses Cmdlets finden Sie unter [Cmdlet-Referenz für Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Video verfügbar](./media/storsimple-configure-and-register-device/Video_icon.png) **Video verfügbar**

Um ein Video ansehen, das zeigt, wie konfigurieren und registrieren Ihr Gerät über Windows PowerShell für StorSimple, klicken Sie auf [hier](http://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

