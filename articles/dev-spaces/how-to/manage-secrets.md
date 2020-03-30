---
title: Hantera hemligheter när du arbetar med ett Azure Dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Lär dig hur du använder Kubernetes hemligheter vid körning eller byggtid när du utvecklar program med Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438468"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Hantera hemligheter när du arbetar med ett Azure Dev Space

Dina tjänster kan kräva vissa lösenord, anslutningssträngar och andra hemligheter, till exempel för databaser eller andra säkra Azure-tjänster. Genom att ange värdena för dessa hemligheter i konfigurationsfiler kan du göra dem tillgängliga i koden som miljövariabler.  Dessa konfigurationsfiler måste hanteras varsamt för att undvika att äventyra hemligheternas säkerhet.

## <a name="storing-and-using-runtime-secrets"></a>Lagra och använda körningshemligheter

Azure Dev Spaces innehåller två rekommenderade, strömlinjeformade alternativ för lagring av hemligheter i `values.dev.yaml` Helm-diagram som genereras `azds.yaml`av Azure Dev Spaces-klientverktyget: i filen och infogade direkt i . Det rekommenderas inte att lagra `values.yaml`hemligheter i .

> [!NOTE]
> Följande metoder visar hur du lagrar och använder hemligheter för Helm-diagram som genereras av klientverktygen. Om du skapar ett eget Helm-diagram kan du använda Helm-diagrammet direkt för att hantera och lagra hemligheter.

### <a name="using-valuesdevyaml"></a>Använda values.dev.yaml

I ett projekt som du redan har förberett `values.dev.yaml` med Azure Dev `azds.yaml` Spaces skapar du en fil i samma mapp som att definiera dina hemliga nycklar och värden. Ett exempel:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifiera `azds.yaml` filreferenserna `values.dev.yaml` som valfria med hjälp av en `?`. Ett exempel:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Om du har ytterligare hemliga filer kan du lägga till dem här också.

Uppdatera eller verifiera dina tjänstreferenser dina hemligheter som miljövariabler. Ett exempel:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Kör dina uppdaterade `azds up`tjänster med .

```console
azds up
```
 
Används `kubectl` för att verifiera att dina hemligheter har skapats.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Det rekommenderas inte att lagra hemligheter i källkontroll. Om du använder `values.dev.yaml` Git `.gitignore` lägger du till i filen för att undvika att hemligheter i källkontrollen begår.

### <a name="using-azdsyaml"></a>Använda azds.yaml

I ett projekt som du redan har förberett med Azure Dev Spaces lägger du till hemliga `azds.yaml`nycklar och värde med *$PLACEHOLDER* syntax under *configurations.develop.install.set* i . Ett exempel:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Du kan ange hemliga värden direkt utan `azds.yaml`att använda *$PLACEHOLDER* syntax i . Den här metoden rekommenderas `azds.yaml` dock inte eftersom lagras i källkontrollen.
     
Skapa `.env` en fil i `azds.yaml` samma mapp som för att definiera *dina $PLACEHOLDER* värden. Ett exempel:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Det rekommenderas inte att lagra hemligheter i källkontroll. Om du använder `.env` Git `.gitignore` lägger du till i filen för att undvika att hemligheter i källkontrollen begår.

Uppdatera eller verifiera dina tjänstreferenser dina hemligheter som miljövariabler. Ett exempel:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Kör dina uppdaterade `azds up`tjänster med .

```console
azds up
```
 
Används `kubectl` för att verifiera att dina hemligheter har skapats.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Använda hemligheter som byggargument

Föregående avsnitt visade hur du lagrar och använder hemligheter som ska användas vid körning i behållaren. Du kan också använda alla hemliga vid containerversionstid, till `azds.yaml`exempel ett lösenord för en privat NuGet, med .

I `azds.yaml`anger du byggtidshemligheter i *configurations.develop.build.args* med syntaxen. `<variable name>: ${secret.<secret name>.<secret key>}` Ett exempel:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

I exemplet ovan är *mynugetsecret* en befintlig hemlighet och *pattoken* är en befintlig nyckel.

>[!NOTE]
> Hemliga namn och `.` nycklar kan innehålla tecknet. Används `\` för `.` att fly när du passerar hemligheter som bygga argument. Till exempel, för att passera en hemlighet som heter `MYTOKEN: ${secret.foo\.bar.token}` *foo.bar* med nyckeln *till token:*. Dessutom kan hemligheter utvärderas med prefix och postfix text. Till exempel `MYURL: eus-${secret.foo\.bar.token}-version1`. Dessutom kan hemligheter som finns i föräldra- och morföräldersutrymmen skickas som byggargument.

I Dockerfile använder du *ARG-direktivet* för att använda hemligheten och använder sedan samma variabel senare i Dockerfile. Ett exempel:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Uppdatera de tjänster som körs i klustret med dessa ändringar. På kommandoraden kör du kommandot:

```
azds up
```

## <a name="next-steps"></a>Nästa steg

Med dessa metoder kan du nu säkert ansluta till en databas, en Azure-cache för Redis eller komma åt säkra Azure-tjänster.
 
