Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie beispielsweise eine Web-App-Adresse in Ihrem Browser eingeben oder auf einen Link auf einer Webseite klicken, wird mit DNS die Domäne in eine IP-Adresse übersetzt. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Beispielsweise ist es viel leichter merken einen DNS-Namen wie **contoso.com** als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3 zu merken ist.

Das DNS-System basiert auf *Datensätze*. Einträge ordnen einen bestimmten *Namen*, z. B. **contoso.com**, entweder eine IP-Adresse oder einen anderen DNS-Namen. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Bei der Erstellung einer Web-App in App Service wird der Web-App automatisch ein DNS-Name zugewiesen. Dieser Name hat das Format **& Lt; Web & Gt;. *.azurewebsites.NET**. Es steht auch eine virtuelle IP-Adresse für die Verwendung beim Erstellen von DNS-Einträgen, sodass entweder Einträge erstellen können, die auf die **. azurewebsites.net**, oder Sie können die IP-Adresse verweisen.

> [AZURE.NOTE] Die IP-Adresse Ihrer Web-app wird geändert, wenn Sie löschen und neu zu Ihrer Web-app erstellen oder ändern den Modus des App Service-Plans von **frei** nach dem Festlegen auf **grundlegende**, **Shared**, oder **Standard**.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Einschränkungen für Web-apps interessieren wir uns jedoch nur zwei *ein* und *CNAME* Datensätze.

###Adresseintrag (A-Eintrag)

Ein A-Datensatz ordnet eine Domäne, z. B. **contoso.com** oder **www.contoso.com**, *oder eine Platzhalterdomäne* wie z. B. **\*.contoso.com**, einer IP-Adresse. Bei einer Web-App in App Service ist das die virtuelle IP-Adresse des Diensts oder eine bestimmte IP-Adresse, die Sie für Ihre Web-App erworben haben.

Die Hauptvorteile eines A-Eintrags gegenüber einem CNAME-Eintrag sind:

* Sie können eine Stammdomäne wie z. B. zuordnen **contoso.com** eine IP-Adresse; viele Registrierungsstellen gestatten nur diese mit A-Datensätze

* Sie können einen Eintrag mit einem Platzhalter verwenden, wie z. B. **\*.contoso.com**, behandelt die Anfragen für mehrere Unterdomänen wie z. B. **mail.contoso.com**, **blogs.contoso.com**, oder **www.contso.com**.

> [AZURE.NOTE] Da ein A-Datensatz einer statischen IP-Adresse zugeordnet ist, kann nicht er Änderungen an die IP-Adresse Ihrer Web-app automatisch auflösen. Eine IP-Adresse für die Verwendung mit A-Einträgen wird bereitgestellt, wenn Sie benutzerdefinierte domänennameneinstellungen für Ihre Web-app konfigurieren; Dieser Wert kann jedoch ändern, wenn Sie löschen und neu zu Ihrer Web-app erstellen oder ändern den Modus des App Service-Plans auf **frei**.

###Aliaseintrag (CNAME-Eintrag)

Ein CNAME-Eintrag weist eine *bestimmte* DNS-Namen, wie z. B. **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen. Im Fall von App Service Web Apps ist der kanonische Domänenname der **& Lt; Yourwebappname >. azurewebsites.net** Domänenname der Web-app. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für die **& Lt; Yourwebappname >. azurewebsites.net** Domänennamen. Der CNAME-Eintrag in die IP-Adresse aufgelöst wird Ihre **& Lt; Yourwebappname >. azurewebsites.net** Domänenname automatisch, wenn die IP-Adresse der Web-app ändert, müssen Sie keinen ergreifen.

> [AZURE.NOTE] Bei einigen domänenregistrierungen können Sie Unterdomänen zuordnen, wenn Sie einen CNAME-Datensatz wie nur **www.contoso.com**, und nicht mit Stammnamen wie z. B. **contoso.com**. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, <a href="http://en.wikipedia.org/wiki/CNAME_record">dem Wikipedia-Eintrag "CNAME Resource Record"</a> oder dem Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a> (IEFT-Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

###Web-App-DNS-Besonderheiten

Bei der Verwendung eines A-Datensatzes für Web-Apps müssen Sie zunächst einen der folgenden CNAME-Datensätze erstellen:

* **Für die Stammdomäne oder Platzhalter-Unterdomänen** -ein DNS-Name des **Awverify** auf  **Awverify. & Lt; Web & Gt;. *.azurewebsites.NET**.

* **Für eine bestimmte Unterdomäne** -ein DNS-Name des **Awverify. & Lt; Unterdomäne >**  **Awverify. & Lt; Web & Gt;. *.azurewebsites.NET**. Beispielsweise **awverify.blogs** Wenn der A-Eintrag **blogs.contoso.com**.

Dieser CNAME-Eintrag dient zur Bestätigung, dass Sie die Domäne, die Sie zu verwenden versuchen, besitzen. Dies erfolgt zusätzlich zum Erstellen eines A-Datensatzes mit Verweis auf die virtuelle IP-Adresse Ihrer Web-App.

Sie finden die IP-Adresse als auch die **Awverify** Name und **. azurewebsites.net** Namen für Ihre Web-app, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie in Ihrem Browser die [Azure-Portal](https://portal.azure.com).

2. In der **Web-Apps** Blatt auf den Namen der Web-app, wählen Sie **Alle Einstellungen**, und wählen Sie dann **benutzerdefinierte Domänen und SSL** unten auf der Seite.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. In der **benutzerdefinierte Domänen und SSL** Blatt, klicken Sie auf **schalten externe Domänen**.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

    > [AZURE.NOTE] Können keine benutzerdefinierten Domänennamen mit einer **frei** web-app, und muss die App Service-Plan aktualisieren **Shared**, **grundlegende**, **Standard**, oder **Premium** Ebene. Weitere Informationen zu App Service-Plans Preise der Ebenen, z. B. zum Ändern der Preisstufe der Web-app, finden Sie unter [Skalieren von Web apps](../articles/web-sites-scale.md).

6. In der **schalten externe Domänen** Blade, sehen Sie die **Awverify** Informationen, die derzeit zugewiesene **. azurewebsites.net** Domänennamen und die virtuelle IP-Adresse. Speichern Sie diese Informationen, das sie beim Erstellen der DNS-Einträge benötigt werden.

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)


