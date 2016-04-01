<properties 
    pageTitle="Cloud App Discovery-Registrierungseinstellungen für Proxydienste | Microsoft Azure" 
    description="In diesem Thema werden die Schritte beschrieben, die Sie ausführen müssen, um den erforderlichen Port auf den Computern mit dem Cloud App Discovery-Agent festzulegen." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2015" 
    ms.author="markusvi"/>

# Cloud App Discovery-Registrierungseinstellungen für Proxydienste

Standardmäßig verwendet der Cloud App Discovery-Agent nur die Ports 80 oder 443. 
Wenn Sie planen, Cloud App Discovery in einer Umgebung mit einem Proxyserver zu installieren, der einen benutzerdefinierten Port (weder 80 noch 443) verwendet, müssen Sie die Agents zur Verwendung dieses Ports konfigurieren. 
Die Konfiguration basiert auf einem Registrierungsschlüssel.


In diesem Thema werden die Schritte beschrieben, die Sie ausführen müssen, um den erforderlichen Port auf den Computern mit dem Cloud App Discovery-Agent festzulegen.



**Um den Port zu ändern, der vom Computer mit dem Cloud App Discovery-Agent verwendet wird, führen Sie die folgenden Schritte aus:**


1. Starten Sie den Registrierungs-Editor. <br> ![Ausführen](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Navigieren Sie zu, oder erstellen Sie den folgenden Registrierungsschlüssel: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint** 

3. Erstellen Sie ein neues **Mehrteilige Zeichenfolge** Wert mit der Bezeichnung **Ports**. ![Neue](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Zum Öffnen der **Mehrteilige Zeichenfolge bearbeiten** Dialogfeld, doppelklicken Sie auf den Wert des Ports.


5. Klicken Sie in das Textfeld Wert Daten ein Geben Sie die folgenden Werte, und fügen Sie alle benutzerdefinierten Ports, die von Ihrer Organisation verwendet werden: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Mehrteilige Zeichenfolge bearbeiten](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Klicken Sie auf **OK** Schließen die **Mehrteilige Zeichenfolge bearbeiten** Dialogfeld.



**Zusätzliche Ressourcen**


* [Wie ermittle ich nicht genehmigte Cloud-Apps, die in meiner Organisation verwendet werden?](active-directory-cloudappdiscovery-whatis.md) 




