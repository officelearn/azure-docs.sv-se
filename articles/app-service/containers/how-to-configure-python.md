---
title: Konfigurera inbyggd Python-avbildning i Azure App Service
description: Den här självstudien beskriver alternativ för att redigera och konfigurera ett Python-program på Azure App Service med den inbyggda Python-avbildningen.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228568"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Konfigurera inbyggd Python-avbildning i Azure App Service (förhandsversion)

Den här artikeln visar hur du konfigurerar den inbyggda Python-avbildningen i [App Service i Linux](app-service-linux-intro.md) för att köra Python-program.

## <a name="python-version"></a>Python-version

Python-körning i App Service i Linux använder version `python-3.7.0`.

## <a name="supported-frameworks"></a>Ramverk som stöds

Alla versioner av webbramverk som uppfyller Web Server Gateway Interface (WSGI) och är kompatibla med `python-3.7`-körningen stöds.

## <a name="package-management"></a>Pakethantering

Under Git-publicering letar Kudu-motorn efter [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) i lagringsplatsens rot och installerar automatiskt paketen i Azure med hjälp av `pip`.

Om du vill skapa den här filen innan du publicerar går du till lagringsplatsens rot och kör följande kommando i Python-miljön:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Konfigurera Python-appen

Den inbyggda Python-avbildningen i App Service använder [Gunicorn](http://gunicorn.org/)-servern för att köra Python-program. Gunicorn är en Python WSGI HTTP-server för UNIX. App Service konfigurerar automatiserar Gunicorn för Django- och Flask-projekt.

### <a name="django-app"></a>Django-app

Om du publicerar ett Django-projekt som innehåller en `wsgi.py`-modul anropar Azure automatiskt anropar Gunicorn med följande kommando:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask-app

Om du publicerar en Flask-app och startpunkten är i en `application.py`- eller `app.py`-modul anropar Azure automatiskt Gunicorn med något av följande respektive kommandon:

```bash
gunicorn application:app
```

Eller 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Anpassa start

För att definiera en anpassad startpunkt för din app skapar du först en _.txt_-fil med ett anpassat Gunicorn-kommando och placerar den i roten för projektet. För att till exempel starta servern med modulen _helloworld.py_ och variabeln `app` skapar du en _startup.txt_ med följande innehåll:

```bash
gunicorn helloworld:app
```

På sidan [Programinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) väljer du **Python| 3.7** som **Körningsstack** och anger namnet på din **Startfil** från föregående steg. Till exempel _startup.txt_.
