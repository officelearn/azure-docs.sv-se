---
title: Konfigurera Python med Azure App Service Web Apps
description: Den här självstudien beskrivs alternativ för att redigera och konfigurera en grundläggande webbserver wsgi (kompatibilitet Gateway Interface) Python-program på Azure App Service Web Apps.
services: app-service
documentationcenter: python
tags: python
author: cephalin
manager: erikre
editor: ''
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: f1859660d2370093ab582c417233b25d363ce952
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228181"
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurera Python med Azure App Service Web Apps
Den här självstudien beskrivs alternativ för att redigera och konfigurera ett grundläggande Web Server (wsgi kompatibilitet Gateway Interface) Python-program på [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Det beskriver ytterligare funktioner för Git-distribution, till exempel virtuell miljö och installera paket med hjälp av requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django eller Flask?
Azure Marketplace innehåller mallar för Bottle, Django och Flask-ramverk. Om du utvecklar din första webbapp i Azure App Service kan du skapa en snabbt från Azure portal:

* [Webbapp med Bottle på Linux](https://portal.azure.com/#create/PTVS.BottleLinux)
* [Webbapp med Django i Linux](https://portal.azure.com/#create/PTVS.DjangoLinux)
* [Webbapp med Flask på Linux](https://portal.azure.com/#create/PTVS.FlaskLinux)

Eller så kan du [utforska Azure Marketplace själv](https://portal.azure.com/#create/hub).

## <a name="web-app-creation-on-azure-portal"></a>Skapa en webbapp i Azure-portalen
Den här självstudien förutsätter att en befintlig Azure-prenumeration och åtkomst till Azure-portalen.

Om du inte har en befintlig webbapp, kan du skapa en från den [Azure-portalen](https://portal.azure.com). I det övre vänstra hörnet, klickar du på **skapa en resurs** > **webb + mobilt** > **webbapp**.

## <a name="git-publishing"></a>Git-publicering
Konfigurera Git-publicering för den nya webbappen genom att följa anvisningarna i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md). Den här självstudien använder Git för att skapa, hantera och publicera ditt Python-webbapp till Azure App Service.

När Git-publicering har ställts in, skapas en Git-lagringsplats och som är associerade med ditt webbprogram. URL för den databasen visas och kan användas för att skicka data från den lokala utvecklingsmiljön till molnet. Kontrollera att en Git-klient installeras även för att publicera program via Git och använda anvisningarna för att skicka ditt webbinnehåll för app till Azure App Service.

## <a name="application-overview"></a>Programöversikt
I nästa avsnitt skapas följande filer. De ska placeras i roten för Git-lagringsplats.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Wsgi kompatibilitet hanterare
Wsgi kompatibilitet är en Python-standard som beskrivs av [program 3333](http://www.python.org/dev/peps/pep-3333/) definierar ett gränssnitt mellan webbservern och Python. Det ger en standardiserad gränssnitt för att skriva olika webbaserade program och ramverk med hjälp av Python. Populära Python-webbramverk använda idag wsgi kompatibilitet. Azure App Service Web Apps ger dig stöd för ramverk; Dessutom kan skapa avancerade användare även egna så länge som anpassade hanteraren följer riktlinjerna som wsgi kompatibilitet-specifikationen.

Här är ett exempel på en `app.py` som definierar en anpassad hanterare:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Du kan köra programmet lokalt med `python app.py`, bläddra sedan till `http://localhost:5555` i din webbläsare.

## <a name="virtual-environment"></a>Virtuell miljö
Även om föregående exempelappen inte kräver några externa paket, är det troligt att ditt program kräver vissa.

För att hantera externt paketberoenden, stöder skapandet av virtuella miljöer i Azure Git-distribution.

När Azure upptäcker ett requirements.txt i Lagringsplatsens rot, skapas automatiskt en virtuell miljö med namnet `env`. Det här inträffar bara på den första distributionen eller under en distribution när den valda Python runtime har ändrats.

Du förmodligen vill skapa en virtuell miljö lokalt för utveckling, men inkludera inte den i din Git-lagringsplats.

## <a name="package-management"></a>Pakethantering
Paket som listas i requirements.txt installeras automatiskt i den virtuella miljön med hjälp av pip. Detta sker vid varje distribution, men pip hoppar över installationen om ett paket är redan installerad.

Exempel `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Python-version
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exempel `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Du behöver skapa en web.config-fil om du vill ange hur servern ska hantera begäranden.

Om du har en web.x.y.config-fil i din lagringsplats där x.y matchar den valda Python-körningen, sedan kopierar Azure automatiskt filen Web.config.

I följande exempel web.config är beroende av en virtuell miljö proxyskript som beskrivs i nästa avsnitt.  De fungerar med wsgi kompatibilitet hanteraren som används i exemplet `app.py` ovan.

Exempel `web.config` för Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exempel `web.config` för Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Statiska filer hanteras av webbservern direkt, utan att gå via Python-kod för bättre prestanda.

I föregående exempel, måste platsen för de statiska filerna på disk matcha platsen i URL: en. Detta innebär att en begäran om `http://pythonapp.azurewebsites.net/static/site.css` fungerar filen på disken på `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` är där du anger wsgi kompatibilitet hanteraren. I föregående exempel har `app.wsgi_app` eftersom hanteraren är en funktion som heter `wsgi_app` i `app.py` i rotmappen.

`PYTHONPATH` kan anpassas, men om du installerar alla beroenden i den virtuella miljön genom att ange dem i requirements.txt, du bör inte ändra den.

## <a name="virtual-environment-proxy"></a>Virtuell miljö Proxy
Följande skript används för att hämta wsgi kompatibilitet-hanteraren, aktiverar de virtuella miljö och logga fel. Det är avsett att vara Allmänt och används utan ändringar.

Innehållet i `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n')

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')

        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()

        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []

        site.main()

        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Anpassa Git-distribution
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]

## <a name="troubleshooting---package-installation"></a>Felsökning – installation av paket
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Felsökning – virtuell miljö
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="troubleshooting---startup-errors"></a>Felsökning – startfel
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Python Developer Center](/python/azure/).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 
