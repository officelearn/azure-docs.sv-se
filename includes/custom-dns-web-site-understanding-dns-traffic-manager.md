Das Domain Name System (DNS) erleichtert das Auffinden von Ressourcen im Internet. Wenn Sie zum Beispiel eine Adresse in Ihrem Browser eingeben, oder Sie auf einen Link innerhalb einer Website klicken, so übersetzt DNS die Domäne in eine IP-Adresse. Die IP-Adresse ist mit Straße und Hausnummer vergleichbar, aber sie ist nicht sehr einprägsam. Beispielsweise ist es viel leichter merken einen DNS-Namen wie **contoso.com** als eine IP-Adresse wie 192.168.1.88 oder 2001:0:4137:1f67:24a2:3888:9cce:fea3 zu merken ist.

Das DNS-System basiert auf *Datensätze*. Einträge ordnen einen bestimmten *Namen*, z. B. **contoso.com**, entweder eine IP-Adresse oder einen anderen DNS-Namen. Wenn eine Anwendung wie ein Webbrowser einen Namen im DNS sucht, gelangt sie zum Eintrag und verwendet dessen Verweis als Adresse. Wenn es sich beim Verweis um eine IP-Adresse handelt, so verwendet der Browser diesen Wert. Wird auf einen anderen DNS-Namen gezeigt, so muss die Anwendung den Namen erneut auflösen. Letztendlich führen alle Namensauflösungen zu einer IP-Adresse.

Azure-Websites wird beim Erstellen automatisch ein DNS-Name zugewiesen. Dieser Name hat das Format **& Lt; Yoursitename & Gt;. *.azurewebsites.NET**. Wenn Sie Ihre Website als Azure Traffic Manager-Endpunkt hinzufügen, ist Ihre Website über die **& Lt; Yourtrafficmanagerprofile & Gt;. *.trafficmanager.NET** Domäne.

> [AZURE.NOTE] Wenn Ihre Website als ein Traffic Manager-Endpunkt konfiguriert ist, verwenden Sie die **. *.trafficmanager.NET** Adresse beim DNS-Einträge zu erstellen.

> Mit Traffic Manager können nur CNAME-Einträge verwendet werden.

Es gibt auch mehrere Eintragstypen, jeder mit eigenen Funktionen und Einschränkungen, aber bei als Traffic Manager-Endpunkte konfigurierten Websites interessieren wir uns nur über einen. *CNAME* Datensätze.

###CNAME- oder Aliasdatensatz

Ein CNAME-Eintrag weist eine *bestimmte* DNS-Namen, wie z. B. **mail.contoso.com** oder **www.contoso.com**, einem anderen (kanonischen) Domänennamen. Im Fall von Azure-Websites mit Traffic Manager ist der kanonische Domänenname der **& Lt; Myapp >. *.trafficmanager.NET** Domänennamen Ihres Traffic Manager-Profils. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für die **& Lt; Myapp >. *.trafficmanager.NET** Domänennamen. Der CNAME-Eintrag in die IP-Adresse aufgelöst wird Ihre **& Lt; Myapp >. *.trafficmanager.NET** Domänenname automatisch, wenn die IP-Adresse der Website ändert, müssen Sie keinen ergreifen.

Beim Traffic Manager eingehender Datenverkehr wird nach der Lastenausgleichsmethode, für die er konfiguriert ist, an Ihre Website geroutet. Für die Besucher Ihrer Website ist dieser Vorgang vollständig transparent. Sie sehen nur den benutzerdefinierten Domänennamen in ihrem Browser.

> [AZURE.NOTE] Bei einigen domänenregistrierungen können Sie Unterdomänen zuordnen, wenn Sie einen CNAME-Datensatz wie nur **www.contoso.com**, und nicht mit Stammnamen wie z. B. **contoso.com**. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, <a href="http://en.wikipedia.org/wiki/CNAME_record">dem Wikipedia-Eintrag "CNAME Resource Record"</a> oder dem Dokument <a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification</a> (IEFT-Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).


