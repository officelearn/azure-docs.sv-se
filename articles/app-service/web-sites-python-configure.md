---
title: Konfigurera Python med Azure App Service Web Apps
description: "Den här självstudiekursen beskrivs alternativ för redigering och konfigurerar en enkel webbserver Gateway Interface (WSGI) kompatibla Python-program i Azure App Service Web Apps."
services: app-service
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: fd00dc91-9935-4331-b955-4bd71e66d518
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/26/2016
ms.author: huvalo
ms.openlocfilehash: fa5f9afbc595f06bd41e8670fab7730b610f570e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configuring-python-with-azure-app-service-web-apps"></a>Konfigurera Python med Azure App Service Web Apps
Den här självstudiekursen beskrivs alternativ för redigering och konfigurera en grundläggande Web Server Gateway Interface (WSGI) kompatibla Python-program på [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Beskriver ytterligare funktioner för Git-distribution, till exempel virtuell miljö och installationen med hjälp av requirements.txt.

## <a name="bottle-django-or-flask"></a>Bottle, Django eller Flask?
Azure Marketplace innehåller mallar för Bottle, Django och Flask ramverk. Om du skapar din första webbapp i Azure App Service kan skapa du en snabbt från Azure portal:

* [Skapa webbappar med Bottle](https://portal.azure.com/#create/PTVS.Bottle)
* [Skapa webbappar med Django](https://portal.azure.com/#create/PTVS.Django)
* [Skapa webbappar med Flask](https://portal.azure.com/#create/PTVS.Flask)

## <a name="web-app-creation-on-azure-portal"></a>Skapa en webbapp i Azure-portalen
Den här kursen förutsätter att en befintlig Azure-prenumeration och åtkomst till Azure-portalen.

Om du inte har en befintlig webbapp, kan du skapa en från den [Azure-portalen](https://portal.azure.com). I det övre vänstra hörnet, klickar du på **skapar du en resurs** > **webb + mobilt** > **webbapp**.

## <a name="git-publishing"></a>Git-publicering
Konfigurera Git-publicering för den nya webbappen genom att följa anvisningarna i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md). Den här kursen använder Git för att skapa, hantera och publicera Python-webbapp till Azure App Service.

När Git-publicering har ställts in, skapas en Git-lagringsplats och som är kopplad till ditt webbprogram. Databasens URL visas och kan användas för att skicka data från den lokala utvecklingsmiljön till molnet. Kontrollera att en Git-klient installeras även för att publicera program via Git och använda anvisningarna för att push-webbinnehållet app till Azure App Service.

## <a name="application-overview"></a>Programöversikt
Följande filer skapas i nästa avsnitt. De ska placeras i roten på Git-lagringsplats.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>WSGI Handler
WSGI är en Python-standard som beskrivs av [program 3333](http://www.python.org/dev/peps/pep-3333/) definierar ett gränssnitt mellan webbservern och Python. Det ger en standardiserad gränssnitt för att skriva olika webbaserade program och ramverk som använder Python. Populära Python web ramverk använder dag WSGI. Azure App Service Web Apps får du stöd för ramverk; Dessutom kan avancerade användare även skapa egna så länge anpassade hanteraren följer riktlinjer för WSGI-specifikationen.

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

Du kan köra programmet lokalt med `python app.py`, bläddra sedan till `http://localhost:5555` i webbläsaren.

## <a name="virtual-environment"></a>Virtuell miljö
Även om föregående exempel appen inte kräver några externa paket, är det troligt att ditt program kräver vissa.

För att hantera externa paketberoenden, stöder Azure Git-distribution skapandet av virtuella miljöer.

När Azure upptäcker en requirements.txt i Lagringsplatsens rot, skapas automatiskt en virtuell miljö som heter `env`. Det här inträffar bara på den första distributionen eller under en distribution när den valda Python runtime har ändrats.

Du förmodligen vill skapa en virtuell miljö lokalt för utveckling, men inkludera inte den i Git-lagringsplatsen.

## <a name="package-management"></a>Pakethantering
Paket som anges i requirements.txt installeras automatiskt i den virtuella miljön med hjälp av pip. Detta sker vid varje distribution, men pip hoppar över installationen om ett paket har redan installerats.

Exempel `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Python Version
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exempel `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config
Du måste skapa en web.config-fil om du vill ange hur servern ska hantera begäranden.

Om du har en web.x.y.config-fil i databasen, där x.y matchar den valda Python-körningen, sedan kopieras Azure automatiskt filen Web.config.

I följande exempel web.config förlitar sig på en virtuell miljö proxyskript som beskrivs i nästa avsnitt.  De fungerar med WSGI hanteraren används i exemplet `app.py` ovan.

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

I föregående exempel, ska platsen för de statiska filerna på disk matcha plats i URL: en. Detta innebär att en begäran om `http://pythonapp.azurewebsites.net/static/site.css` fungerar filen på disken på `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER` är där du anger WSGI-hanteraren. I föregående exempel, den har `app.wsgi_app` eftersom hanteraren är en funktion som heter `wsgi_app` i `app.py` i rotmappen.

`PYTHONPATH` kan anpassas, men om du installerar alla beroenden i den virtuella miljön genom att ange dem i requirements.txt, du får inte ändra den.

## <a name="virtual-environment-proxy"></a>Virtuell miljö Proxy
Följande skript för att hämta WSGI-hanteraren, aktivera de virtuella miljön och logga fel. Den är avsedd att vara generisk och används utan ändringar.

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

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Python Developer Center](/develop/python/).

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 
