<properties 
    pageTitle="Bekannte Netzwerke | Microsoft Azure" 
    description="Durch das Konfigurieren bekannter Netzwerkwerke können Sie vermeiden, dass IP-Adressen Ihrer Organisation in die Berichte „Anmeldungen aus mehreren geografischen Regionen“ und „Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten“ aufgenommen werden." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="msStevenPo"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="markvi"/>

# Bekannte Netzwerke


Sie können die Zugriffs- und Nutzungsberichte von Azure Active Directory verwenden, um sich einen Einblick in die Integrität und Sicherheit des Verzeichnisses Ihrer Organisation zu verschaffen. Mithilfe dieser Informationen kann ein Verzeichnisadministrator mögliche Sicherheitsrisiken besser bestimmen, um angemessen zu planen, wie diese Risiken eingedämmt werden können.

Es ist möglich, die "*Anmeldungen aus mehreren geografischen Regionen*"und"*Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten*" Berichte flag falsch IP-Adressen, die tatsächlich von Ihrem Unternehmen gehören. 

Dies kann in folgenden Fällen geschehen: 

- Ein Benutzer in der Niederlassung in Boston hat sich per Remoteverbindung beim Rechenzentrum San Francisco angemeldet. Dadurch wird der Bericht „Anmeldungen aus mehreren geografischen Regionen“ ausgelöst. 

- Ein Benutzer Ihrer Organisation versucht mehrere Male, sich mit einem falschen Kennwort anzumelden. Dadurch wird der Bericht „Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten“ ausgelöst. 

Damit in diesen Fällen keine irreführenden Sicherheitsberichte generiert werden, wird empfohlen, der Liste der öffentlichen IP-Adressen Ihres Unternehmens bekannte IP-Adressbereiche hinzuzufügen.    


###Führen Sie zum Hinzufügen der öffentlichen IP-Adressbereiche Ihrer Organisation die folgenden Schritte aus: 

1.  Anmelden bei der [Azure-Verwaltungsportal](https://manage.windowsazure.com).

2.  Klicken Sie im linken Bereich auf **Active Directory**. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-01.png)

3.  In der **Verzeichnis** Registerkarte, wählen Sie Ihr Verzeichnis.

4.  Klicken Sie im Menü am oberen Rand **konfigurieren**. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-02.png)

5.  Öffnen Sie auf der Registerkarte konfigurieren, **der Organisationen öffentliche IP-Adressbereiche** <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Klicken Sie auf **bekannte IP-Adressbereiche hinzufügen**.

7.  Klicken Sie im Dialogfeld fügen Sie die Adressbereiche hinzu, und klicken Sie auf das Kontrollkästchen, wenn Sie fertig sind. <br><br>![Funktionsweise von Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-04.png)


**Zusätzliche Ressourcen**


* [Anzeigen von Zugriffs- und Nutzungsberichten](active-directory-view-access-usage-reports.md)
* [Anmeldungen von IP-Adressen mit verdächtigen Aktivitäten](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Anmeldungen aus mehreren geografischen Regionen](active-directory-reporting-sign-ins-from-multiple-geographies.md)




