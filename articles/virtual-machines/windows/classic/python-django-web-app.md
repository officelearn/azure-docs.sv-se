---
title: "Django-webbapp på en Windows Server Azure VM | Microsoft Docs"
description: "Lär dig mer om att vara värd för en webbplats med Django-baserade i Azure med hjälp av en Windows Server 2012 R2 Datacenter VM med den klassiska distributionsmodellen."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Django Hello World webbprogram på en Windows Server-VM

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och den klassiska distributionsmodellen](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Vi rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Den här kursen visar hur du värd för en webbplats med Django-baserade i Windows Server i Azure Virtual Machines. I självstudiekursen förutsätter vi att några tidigare erfarenheter med Azure. När du slutför kursen har du ett Django-program in och körs i molnet.

Lär dig att:

* Konfigurera en virtuell Azure-dator till värd Django. Även om den här självstudiekursen beskrivs hur du gör detta för **Windows Server**, gör samma sak för en Linux VM finns i Azure.
* Skapa ett nytt Django-program i Windows.

Kursen visar hur du skapar ett grundläggande Hello World-webbprogram. Programmet finns i en virtuell Azure-dator.

Följande skärmbild visar den färdiga appen:

![Ett fönster i webbläsaren visar hello world-sidan i Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Skapa och konfigurera en virtuell Azure-dator till värd Django

1. Om du vill skapa en virtuell Azure-dator med Windows Server 2012 R2 Datacenter-distribution, se [skapa en virtuell dator som kör Windows i Azure portal](tutorial.md).
2. Ange Azure för att dirigera port 80 trafik från webben till port 80 på den virtuella datorn:
   
   1. Gå till instrumentpanelen i Azure-portalen och välj den nyligen skapade virtuella datorn.
   2. Klicka på **Slutpunkter** och sedan på **Lägg till**.

     ![Lägga till en slutpunkt](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. På den **lägga till slutpunkten** sidan för **namn**, ange **HTTP**. Ställ in TCP offentliga och privata portar på **80**.

     ![Ange namn och ange offentliga och privata portar](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Klicka på **OK**.
     
3. Välj den virtuella datorn på instrumentpanelen. För att använda Remote Desktop Protocol (RDP) för att logga in via fjärranslutning på den nyligen skapade virtuella Azure-datorn, klickar du på **Anslut**.  

> [!IMPORTANT] 
> Följande instruktioner förutsätter att du loggat in på den virtuella datorn på rätt sätt. De förutsätter att du utfärdar kommandon på den virtuella datorn och inte på den lokala datorn.

## <a id="setup"></a>Installera Python och Django WFastCGI
> [!NOTE]
> För att hämta med hjälp av Internet Explorer, kanske du måste konfigurera Internet Explorer **Förbättrad säkerhetskonfiguration** inställningar. Gör detta genom att klicka på **starta** > **Administrationsverktyg** > **Serverhanteraren** > **lokal Server**. Klicka på **Förbättrad säkerhetskonfiguration**, och välj sedan **av**.

1. Installera de senaste versionerna av Python 2.7 eller Python 3.4 från [python.org][python.org].
2. Installera wfastcgi och django-paket med hjälp av pip.
   
    För Python 2.7, använder du följande kommando:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    För Python 3.4, använder du följande kommando:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Installera IIS med FastCGI
* Installera Internet Information Services (IIS) med stöd för FastCGI. Det kan ta flera minuter att utföra.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Skapa ett nytt Django-program
1. I C:\inetpub\wwwroot, för att skapa ett nytt Django-projekt, anger du följande kommando:
   
   För Python 2.7, använder du följande kommando:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   För Python 3.4, använder du följande kommando:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![Resultatet av kommandot New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. Den `django-admin` kommando skapar en grundläggande struktur för Django-baserade webbplatser:
   
   * `helloworld\manage.py`hjälper dig att värd för start och stopp där webbplatsen Django-baserade.
   * `helloworld\helloworld\settings.py`har Django-inställningar för ditt program.
   * `helloworld\helloworld\urls.py`har koden mappning mellan varje URL och vyn.
3. Skapa en ny fil med namnet views.py i katalogen C:\inetpub\wwwroot\helloworld\helloworld. Den här filen innehåller den vy som återger sidan ”hello world”. Ange följande kommandon i Redigeraren för koden:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Ersätt innehållet i filen urls.py med följande kommandon:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Konfigurera IIS
1. Lås upp hanteraravsnittet i globala applicationhost.config-filen.  På så sätt kan web.config-filen att använda Python-hanteraren. Lägg till det här kommandot:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Aktivera WFastCGI. Detta lägger till ett program till globala applicationhost.config-filen som refererar till Python-tolkning körbara och wfastcgi.py-skriptet.
   
    I Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    I Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. I C:\inetpub\wwwroot\helloworld, skapar du en web.config-fil. Värdet för den `scriptProcessor` attributet bör matcha utdata från föregående steg. Mer information om inställningen wfastcgi finns [pypi wfastcgi][wfastcgi].
   
   I Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   I Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Uppdatera platsen på standardwebbplatsen för IIS att peka mot Django projektmappen:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Läs in webbsidan i webbläsaren.

![Ett fönster i webbläsaren visar hello world-sidan på Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Stäng av den virtuella Azure-datorn
När du är klar med den här kursen rekommenderar vi att du stänga av eller ta bort Azure-VM som du skapade under kursen. Detta frigör resurser för andra kurser och du kan undvika medför Azure kostnader.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
