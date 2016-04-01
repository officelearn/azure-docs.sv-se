#Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website

Wenn Sie eine Website erstellen, bietet Azure eine benutzerfreundliche Unterdomäne in der azurewebsites.NET-Domäne damit Ihre Benutzer Ihre Website über eine URL wie http://&lt;mysite>.azurewebsites.net zugreifen können. Wenn Sie für Ihre Websites jedoch den Freigabe- oder Standardmodus festlegen, können Sie Ihre Website Ihrem eigenen Domänennamen zuordnen.

Optional können Sie mit Azure Traffic Manager einen Lastenausgleich für den auf Ihrer Website eingehenden Datenverkehr durchführen. Weitere Informationen zur Funktionsweise von Traffic Manager mit Websites finden Sie unter [Steuern von Azure-Websites-Verkehrs mit Azure Traffic Manager][trafficmanager].

> [AZURE.NOTE] Die Verfahren in dieser Aufgabe gelten für Azure-Websites. Cloud-Diensten finden Sie unter <a href="/develop/net/common-tasks/custom-dns/">Konfigurieren eines benutzerdefinierten Domänennamens in Azure</a>.

> [AZURE.NOTE] Die Schritte in dieser Aufgabe müssen Sie zum Konfigurieren Ihrer Websites für Freigabe- oder Standardmodus, der ändern kann, wie viel Sie für Ihr Abonnement abgerechnet werden. Weitere Informationen finden Sie in den <a href="/pricing/details/web-sites/">Preisdetails zu Websites</a>.

Themen in diesem Artikel:

-   [Informationen zu CNAME- und A-Datensätzen](#understanding-records)
-   [Konfigurieren Ihrer Websites für den Modus "Freigegeben" oder "Standard"](#bkmk_configsharedmode)
-   [Hinzufügen Ihrer Websites zu Traffic Manager](#trafficmanager)
-   [Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurecname)
-   [Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Informationen zu CNAME- und A-Datensätzen</h2>

Mit CNAME-Datensätzen (oder Aliasdatensätzen) und A-Datensätzen können Sie einen Domänennamen einer Website zuweisen, beide funktionieren jedoch unterschiedlich.

###CNAME- oder Aliasdatensatz

Ein CNAME-Eintrag weist eine *bestimmte* Domäne, z. B. **"contoso.com"** oder **www.contoso.com**, zu einem kanonischen Domänennamen. In diesem Fall wird von der kanonische Domänenname entweder der **& Lt; Myapp >. azurewebsites.net** Domänennamen Ihrer Azure-Website oder der **& Lt; Myapp >. trafficmgr.com** Domänennamen Ihres Traffic Manager-Profils. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für die **& Lt; Myapp >. azurewebsites.net** oder **& Lt; Myapp >. trafficmgr.com** Domänennamen. Der CNAME-Eintrag in die IP-Adresse aufgelöst wird Ihre **& Lt; Myapp >. azurewebsites.net** oder **& Lt; Myapp >. trafficmgr.com** Domänenname automatisch, wenn die IP-Adresse der Website ändert, müssen Sie keinen ergreifen.

> [AZURE.NOTE] Bei einigen domänenregistrierungen können Sie einen CNAME-Datensatz wie www.contoso.com zuweisen und nicht mit einem Stammnamen wie contoso.com Unterdomänen nur. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, <a href="http://en.wikipedia.org/wiki/CNAME_record">dem Wikipedia-Eintrag "CNAME Resource Record"</a> oder dem Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a> (IEFT-Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

###A-Datensatz

Ein A-Datensatz ordnet eine Domäne, z. B. **contoso.com** oder **www.contoso.com**, *oder eine Platzhalterdomäne* wie z. B. **\*.contoso.com**, einer IP-Adresse. Im Falle einer Azure-Website entweder die virtuelle IP-Adresse des Dienstes oder eine spezifische, für die Website erworbene IP-Adresse. Der Hauptvorteil eines A-Datensatzes gegenüber einem CNAME-Datensatz ist daher, dass Sie einen Eintrag haben können, die einen Platzhalter, wie z. B. verwendet ***. contoso.com**, behandelt die Anfragen für mehrere Unterdomänen wie z. B. **mail.contoso.com**, **login.contoso.com**, oder **www.contso.com**.

> [AZURE.NOTE] Da ein A-Datensatz einer statischen IP-Adresse zugeordnet ist, kann nicht er Änderungen an die IP-Adresse Ihrer Website automatisch auflösen. Eine IP-Adresse zur Verwendung mit A-Einträgen erhalten Sie, wenn Sie benutzerdefinierte Domänennameneinstellungen für Ihre Website konfigurieren.Dieser Wert kann sich jedoch ändern, wenn Sie Ihre Website löschen und neu erstellen oder den Websitemodus auf "Kostenlos" zurücksetzen.

> [AZURE.NOTE] A-Datensätze können nicht für den Lastenausgleich mit Traffic Manager verwendet werden. Weitere Informationen finden Sie unter [Steuern von Azure-Websites-Verkehrs mit Azure Traffic Manager][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Konfigurieren Ihrer Websites für den Modus "Shared" oder "Standard"</h2>

Ein benutzerdefinierter Domänenname für eine Website kann nur in den Azure-Websites-Modi "Freigegeben" und "Standard" festgelegt werden. Bevor Sie den Modus einer Website von "Kostenlos" in "Freigegeben" oder "Standard" ändern können, müssen Sie das für Ihr Abonnement geltende Ausgabenlimit aufheben. Weitere Informationen zu Freigabe- und Preise, finden Sie unter [Preisdetails][PricingDetails].

1. Öffnen Sie in Ihrem Browser die [Verwaltungsportal][portal].
2. In der **Websites** Registerkarte, klicken Sie auf den Namen Ihrer Website.

    ![][standardmode1]

3. Klicken Sie auf die **Skalieren** Registerkarte.

    ![][standardmode2]


4. In den **Allgemeine** legen den websitemodus durch Klicken auf **SHARED**.

    ![][standardmode3]

    > [AZURE.NOTE] Wenn Sie Traffic Manager mit dieser Website verwenden, müssen Sie den Standardmodus anstelle des Freigabemodus verwenden.

5. Klicken Sie auf **Speichern**.
6. Wenn Sie dazu aufgefordert werden, zu der Anstieg der Kosten für den Freigabemodus (oder im Standardmodus, falls Sie Standard gewählt), klicken Sie auf **Ja** Wenn Sie zustimmen.

    <!--![][standardmode4]-->

    **Hinweis**<br />
    Wenn Sie eine Fehlermeldung erhalten, dass das Konfigurieren der Größenordnung für die Website mit dem angegebenen Namen fehlgeschlagen ist, können Sie über die Detailschaltfläche weitere Informationen anzeigen.

<a name="trafficmanager"></a><h2>(Optional) Hinzufügen Ihrer Websites zu Traffic Manager</h2>

Gehen Sie folgendermaßen vor, um Ihre Website mit Traffic Manager zu verwenden.

1. Wenn Sie nicht bereits über ein Traffic Manager-Profil verfügen, verwenden Sie die Informationen im [Erstellen eines Traffic Manager-Profils mithilfe der Schnellerfassung][createprofile] zu erstellen. Beachten Sie die **. trafficmgr.com** Domänennamens Traffic Manager-Profil zugeordnet. Dieser wird in einem späteren Schritt verwendet.

2. Verwenden Sie die Informationen im [Hinzufügen oder Löschen von Endpunkten][addendpoint] um die Website als Endpunkt in Traffic Manager-Profil hinzuzufügen.

    > [AZURE.NOTE] Wenn Ihre Website beim Hinzufügen eines Endpunkts nicht aufgeführt ist, stellen Sie sicher, dass es für den Standardmodus konfiguriert ist. Sie müssen für Ihre Website den Standardmodus verwenden, damit Sie mit Traffic Manager arbeiten können.

3. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site mit der Bezeichnung **Domänennamen**, **DNS**, oder **Namenserververwaltung**.

4. Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias**, oder **Unterdomänen**.

5. Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Z. B. **Www** Wenn Sie einen Alias erstellen möchten **www.customdomain.com**.

5. Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist die **. trafficmgr.com** -Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** auf **contoso.trafficmgr.com**, den Domänennamen einer Website:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong></td>
<td><strong>Kanonische Domäne</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

Einem Besucher von **www.contoso.com** wird niemals den wirkliche Host angezeigt.
(contoso.azurewebsite.net) angezeigt, sodass der Weiterleitungsprozess für den Endbenutzer nicht sichtbar ist.

> [AZURE.NOTE] Wenn Sie Traffic Manager für eine Website verwenden, müssen Sie nicht die Schritte in den folgenden Abschnitten '**Hinzufügen ein CNAME-Datensatzes für Ihre benutzerdefinierte Domäne**'und'**Hinzufügen ein A-Datensatzes für Ihre benutzerdefinierte Domäne**'. Der in den vorhergehenden Schritten erstellte CNAME-Datensatz leitet eingehenden Datenverkehr an Traffic Manager weiter, der ihn wiederum an die Website-Endpunkte weiterleitet.

<a name="bkmk_configurecname"></a><h2>Hinzufügen eines CNAME-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen einen neuen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzufügen, um einen CNAME-Datensatz zu erstellen. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich.

1. Verwenden Sie eine der folgenden Methoden zum Suchen der **. Alternativ** Domänennamen Ihrer Website zugewiesen.

    * Melden Sie sich die [Azure-Verwaltungsportal][portal], wählen Sie Ihre Website **Dashboard**, und suchen Sie dann die **Website-URL** Eintrag in der **auf einen Blick** Abschnitt.

    * Installieren und Konfigurieren von [Azure Powershell](/manage/install-and-configure-windows-powershell/), und verwenden Sie dann den folgenden Befehl aus:

            get-azurewebsite yoursitename | select hostnames

    * Installieren und Konfigurieren der [Azure-Befehlszeilenschnittstelle](/manage/install-and-configure-cli/), und verwenden Sie dann den folgenden Befehl aus:

            azure site domain list yoursitename

    Speichern Sie diesen **. Alternativ** Namen, da es in den folgenden Schritten verwendet werden soll.

3. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site mit der Bezeichnung **Domänennamen**, **DNS**, oder **Namenserververwaltung**.

4. Navigieren Sie nun zu dem Bereich, in dem Sie CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen. Suchen Sie nach den Wörtern **CNAME**, **Alias**, oder **Unterdomänen**.

5. Sie müssen auch den Domänen- oder Unterdomänenalias für den CNAME angeben. Z. B. **Www** Wenn Sie einen Alias erstellen möchten **www.customdomain.com**. Wenn Sie einen Alias für die Stammdomäne erstellen möchten, kann es als aufgeführt der "**@**" Symbol in Ihrer Registrierungsstelle DNS-Tools.

5. Sie müssen auch einen Hostnamen angeben, der der kanonische Domänenname dieses CNAME-Alias ist. Dies ist die **. Alternativ** -Name für Ihre Website.

Der folgende CNAME-Datensatz leitet zum Beispiel den gesamten Verkehr von **www.contoso.com** auf **contoso.azurewebsite.net**, den Domänennamen einer Website:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Alias/Hostname/Unterdomäne</strong></td>
<td><strong>Kanonische Domäne</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

Einem Besucher von **www.contoso.com** wird niemals den wirkliche Host angezeigt.
(contoso.azurewebsite.net) angezeigt, sodass der Weiterleitungsprozess für den
Endbenutzer nicht sichtbar ist.

> [AZURE.NOTE] Das oben genannte Beispiel gilt nur für Verkehr an der __Www__ Unterdomäne. Da Sie keine Platzhalter mit CNAME-Datensätzen verwenden können, müssen Sie einen CNAME für jede Domäne/Unterdomäne erstellen. Wenn Sie Datenverkehr von Unterdomänen wie z. B. weiterleiten möchten *. contoso.com, Ihre azurewebsite.NET-Adresse können Sie konfigurieren eine __URL-Umleitung__ oder __URL-Weiterleitung__ in Ihrer DNS-Einstellungen, oder erstellen Sie einen A-Datensatz.

> [AZURE.NOTE] Es dauert einige Zeit Ihr CNAME über das DNS-System weitergegeben. Der CNAME für die Website kann erst nach abgeschlossener CNAME-Verteilung eingestellt werden. Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

###Hinzufügen eines Domänennamens zu Ihrer Website

Nachdem der CNAME-Datensatz für den Domänennamen verteilt wurde, müssen Sie ihn Ihrer Website zuweisen. Sie können entweder die Azure-Befehlszeilenschnittstelle oder das Azure-Verwaltungsportal verwenden, um den durch den CNAME-Datensatz festgelegten benutzerdefinierten Domänennamen zu Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Befehlszeilentools**

Installieren und Konfigurieren der [Azure-Befehlszeilenschnittstelle](/manage/install-and-configure-cli/), und verwenden Sie dann den folgenden Befehl aus:

    azure site domain add customdomain yoursitename

Im folgenden werden z. B. einen benutzerdefinierten Domänennamen hinzufügen **www.contoso.com** an die **contoso.azurewebsite.net** Website:

    azure site domain add www.contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname der Website hinzugefügt wurde:

    azure site domain list yoursitename

Mit diesem Befehl zurückgegebene Liste sollte den Domänennamen als auch die Standardeinstellung enthalten **. Alternativ** Eintrag.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1. Öffnen Sie in Ihrem Browser die [Azure-Verwaltungsportal][portal].

2. In der **Websites** Registerkarte, klicken Sie auf den Namen Ihrer Site, wählen **Dashboard**, und wählen Sie dann **Domänen verwalten** unten auf der Seite.

    ![][setcname2]

6. In der **DOMÄNENNAMEN** Text Geben Sie den Domänennamen, den Sie konfiguriert haben.

    ![][setcname3]

6. Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname in Liste der **Domänennamen** Teil der **konfigurieren** Seite Ihrer Website.

<a name="bkmk_configurearecord"></a><h2>Hinzufügen eines A-Datensatzes für Ihre benutzerdefinierte Domäne</h2>

Sie müssen zunächst die IP-Adresse Ihrer Website ermitteln, um einen A-Datensatz zu erstellen. Fügen Sie dann einen Eintrag zu der DNS-Tabelle Ihrer benutzerdefinierten Domäne hinzu. Verwenden Sie hierzu die durch Ihre Registrierung bereitgestellten Tools. Die Methoden zum Festlegen eines A-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden Sie sich auch in einigen Punkten. Die Konzepte sind allerdings gleich. Neben dem A-Datensatz müssen Sie auch einen CNAME-Datensatz erstellen, den Azure verwendet, um den A-Datensatz zu überprüfen.

1. Öffnen Sie in Ihrem Browser die [Azure-Verwaltungsportal][portal].

2. In der **Websites** Registerkarte, klicken Sie auf den Namen Ihrer Site, wählen **Dashboard**, und wählen Sie dann **Domänen verwalten** aus dem unteren Rand des Bildschirms.

    ![][setcname2]

5. Auf der **benutzerdefinierte Domänen verwalten** im Dialogfeld Suchen **IP-Adresse zu verwenden, wenn der A-Einträge konfigurieren**. Kopieren Sie die IP-Adresse. Diese wird zum Erstellen des A-Datensatzes verwendet.

5. Auf der **benutzerdefinierte Domänen verwalten** Dialog, beachten Sie Awverify-Domänennamen am Ende der Text am oberen Rand des Dialogfelds. Sollte **awverify.mysite.azurewebsites.net** in denen **Mysite** ist der Name Ihrer Website. Kopieren Sie diesen, da das der Domänenname ist, der zum Erstellen der Überprüfung des CNAME-Datensatzes verwendet wird.

6. Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung. Suchen Sie nach Links oder Bereichen der Site mit der Bezeichnung **Domänennamen**, **DNS**, oder **Namenserververwaltung**.

6. Navigieren Sie zu dem Bereich, in dem Sie A- und CNAME-Datensätze auswählen oder eingeben können. Möglicherweise müssen Sie den Datensatztyp in einem Dropdownmenü auswählen oder die Seite für erweiterte Einstellungen aufrufen.

7. Führen Sie die folgenden Schritte aus, um einen A-Datensatz zu erstellen:

    1. Wählen Sie die Domäne oder Unterdomäne aus, die den A-Datensatz verwenden wird, oder geben Sie diese ein. Wählen Sie z. B. **Www** Wenn Sie einen Alias erstellen möchten **www.customdomain.com**. Wenn Sie einen Platzhaltereintrag für alle Unterdomänen erstellen möchten, geben Sie "__*__". Dies deckt alle Unterdomänen wie z. B. **mail.customdomain.com**, **login.customdomain.com**, und **www.customdomain.com**.

        Wenn Sie einen A-Datensatz für die Stammdomäne erstellen möchten, kann es als aufgeführt der "**@**" Symbol in Ihrer Registrierungsstelle DNS-Tools.

    2. Geben Sie die IP-Adresse Ihres Clouddiensts in das angegebene Feld ein. So wird der im A-Datensatz verwendete Domäneneintrag der IP-Adresse Ihrer Clouddienstbereitstellung zugewiesen.

        Z. B. ein Datensatzes leitet den gesamten Datenverkehr von folgenden **contoso.com** auf **137.135.70.239**, die IP-Adresse unserer bereitgestellten Anwendung:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Hostname/Unterdomäne</strong></td>
        <td><strong>IP-Adresse</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        Dieses Beispiel zeigt das Erstellen eines A-Datensatzes für die Stammdomäne. Wenn Sie alle Unterdomänen abdeckt einen Platzhaltereintrag erstellen möchten, geben Sie "__*__" als Unterdomäne.

7. Als Nächstes erstellen Sie einen CNAME-Datensatz, der einen von Alias **Awverify**, und eine kanonische Domäne von **awverify.mysite.azurewebsites.net** die Sie zuvor erhalten haben.

    > [AZURE.NOTE] Während ein Alias von Awverify für manche funktionieren kann, erfordern andere den vollständigen Alias-Domänennamen des www.customdomainname.com oder awverify.customdomainname.com.

    Das folgende Beispiel erstellt einen CNAME-Datensatz, den Azure zur Überprüfung einer A-Datensatzkonfiguration verwenden kann.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Alias/Hostname/Unterdomäne</strong></td>
    <td><strong>Kanonische Domäne</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.net</td>
    </tr>
    </table>

> [AZURE.NOTE] Es dauert einige Zeit der awverify-CNAME über das DNS-System weitergegeben. Sie können den durch den A-Datensatz für die Website festgelegten benutzerdefinierten Domänennamen erst einstellen, wenn die Verteilung des awverify-CNAME abgeschlossen ist. Mithilfe eines Services wie <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> (in englischer Sprache) können Sie überprüfen, ob der CNAME verfügbar ist.

###Hinzufügen eines Domänennamens zu Ihrer Website

Nach der **Awverify** CNAME-Eintrag für den Domänennamen verteilt wurde, können Sie die von Ihrer Website A-Datensatz festgelegte benutzerdefinierte Domäne zuweisen. Sie können entweder die Azure-Befehlszeilenschnittstelle oder das Azure-Verwaltungsportal verwenden, um den durch den A-Datensatz festgelegten benutzerdefinierten Domänennamen zu Ihrer Website hinzuzufügen.

**Hinzufügen eines Domänennamens mithilfe der Azure-Befehlszeilenschnittstelle (Azure-CLI)**

Installieren und Konfigurieren der [Befehlszeilenschnittstelle](/manage/install-and-configure-cli/), und verwenden Sie dann den folgenden Befehl aus:

    azure site domain add customdomain yoursitename

Im folgenden werden z. B. einen benutzerdefinierten Domänennamen hinzufügen **contoso.com** an die **contoso.azurewebsite.net** Website:

    azure site domain add contoso.com contoso

Mit dem folgenden Befehl können Sie überprüfen, ob der benutzerdefinierte Domänenname der Website hinzugefügt wurde:

    azure site domain list yoursitename

Mit diesem Befehl zurückgegebene Liste sollte den Domänennamen als auch die Standardeinstellung enthalten **. Alternativ** Eintrag.

**Hinzufügen eines Domänennamens mithilfe des Azure Verwaltungsportals**

1. Öffnen Sie in Ihrem Browser die [Azure-Verwaltungsportal][portal].

2. In der **Websites** Registerkarte, klicken Sie auf den Namen Ihrer Site, wählen **Dashboard**, und wählen Sie dann **Domänen verwalten** unten auf der Seite.

    ![][setcname2]

6. In der **DOMÄNENNAMEN** Text Geben Sie den Domänennamen, den Sie konfiguriert haben.

    ![][setcname3]

6. Klicken Sie auf das Häkchen, um den Domänennamen zu akzeptieren.

Nach Abschluss der Konfiguration wird der benutzerdefinierte Domänenname in Liste der **Domänennamen** Teil der **konfigurieren** Seite Ihrer Website.

> [AZURE.NOTE] Nachdem Sie den benutzerdefinierten Domänennamen durch den A-Datensatz zu Ihrer Website hinzugefügt haben, können Sie den Awverify-CNAME-Eintrag mithilfe der durch Ihre Registrierung bereitgestellten Tools entfernen. Wenn Sie jedoch in Zukunft einen weiteren A-Datensatz hinzufügen möchten, müssen Sie den awverify-Datensatz neu erstellen, damit Sie den durch den neuen A-Datensatz festgelegten neuen Domänennamen der Website zuweisen können.

## Nächste Schritte

-   [How to manage web sites (Verwalten von Websites, in englischer Sprache)](/manage/services/web-sites/how-to-manage-websites/)

-   [Configure an SSL certificate for Web Sites (Konfigurieren eines SSL-Zertifikats für Web Sites, in englischer Sprache)](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png


