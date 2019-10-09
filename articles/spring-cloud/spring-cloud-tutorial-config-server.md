---
title: Konfigurera konfigurations servern i Azure våren-molnet | Microsoft Docs
description: I den här självstudien får du lära dig hur du konfigurerar en vår moln konfigurations Server för ditt Azure våren-moln på Azure Portal
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: f959d216859ded54de52de474ee3150f91bcfff1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038925"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Självstudier: Konfigurera en fjäder moln konfigurations Server för din tjänst

I den här självstudien får du se hur du ansluter en fjäder moln konfigurations server till din Azure våren Cloud-tjänst.

Våren Cloud config tillhandahåller stöd för Server-och klient sidan för den externa konfigurationen i ett distribuerat system. Med konfigurations servern har du en central plats för att hantera externa egenskaper för program i alla miljöer. Mer information finns i [vår referens för vår moln konfigurations Server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En Azure våren Cloud-tjänst som redan har tillhandahållits och körs.  Slutför [den här snabb](spring-cloud-quickstart-launch-app-cli.md) starten för att etablera och starta en Azure våren Cloud-tjänst.


## <a name="restriction"></a>Begränsning

Det finns vissa begränsningar när du använder __konfigurations servern__ med en git-Server del. Vissa egenskaper kommer automatiskt att matas in i program miljön för att få åtkomst till __konfigurations Server__ och __tjänst identifiering__. Om du även konfigurerar dessa egenskaper från **config server** -filerna kan det uppstå konflikter och oväntade beteenden.  Egenskaperna är: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> Vi rekommenderar starkt att du __inte__ lägger till egenskaperna ovan i __config server__ -programfilerna.

## <a name="create-your-config-server-files"></a>Skapa dina config server-filer

Azure våren Cloud stöder Azure DevOps, GitHub, GitLab och Bitbucket för att lagra dina config server-filer.  När du är klar med din lagrings plats kan du göra konfigurationsfilerna enligt anvisningarna nedan och lagra dem där.

Dessutom är vissa konfigurerbara egenskaper bara tillgängliga för vissa typer. I följande underavsnitt visas egenskaperna för varje typ av lagrings plats.


### <a name="public-repository"></a>Offentligt lager

När du använder ett offentligt lager kommer dina konfigurerbara egenskaper att bli mer begränsade.

Alla konfigurerbara egenskaper som används för att konfigurera den offentliga `Git`-lagringsplatsen visas nedan.

> [!NOTE]
> Endast bindestreck ("-") namngivnings konventionen för avgränsade ord stöds för tillfället. Du kan till exempel använda `default-label` men inte `defaultLabel`.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | @No__t-0 av `Git`-lagringsplatsen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@` eller `ssh://`. |
| `default-label` | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name` eller `commit-id` av lagrings platsen. |
| `search-paths`  | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privat lagring med SSH-autentisering

Alla konfigurerbara egenskaper som används för att konfigurera privat `Git`-lagringsplatsen med `Ssh` visas nedan.

> [!NOTE]
> Endast bindestreck ("-") namngivnings konventionen för avgränsade ord stöds för tillfället. Du kan till exempel använda `default-label` men inte `defaultLabel`.

| Egenskap                   | Krävs | Funktion                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | @No__t-0 av `Git`-lagringsplatsen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@` eller `ssh://`. |
| `default-label`            | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name` eller `commit-id` av lagrings platsen. |
| `search-paths`             | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `private-key`              | `no`     | Den `Ssh` privata nyckeln för att komma åt `Git`-lagringsplats, __krävs__ när `uri` startade med `git@` eller `ssh://`. |
| `host-key`                 | `no`     | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Värd nyckelalgoritm måste vara en av `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` och `ecdsa-sha2-nistp521`. Krävs endast om `host-key` finns. |
| `strict-host-key-checking` | `no`     | Indikerar om konfigurations servern inte kan starta när du använder ange `host-key`, ska vara `true` (standardvärde) eller `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privat lagrings plats med grundläggande autentisering

Alla konfigurerbara egenskaper som används för att konfigurera en privat git-lagringsplats med grundläggande autentisering visas nedan.

> [!NOTE]
> Endast bindestreck ("-") namngivnings konventionen för avgränsade ord stöds för tillfället. Du kan till exempel använda `default-label` men inte `defaultLabel`.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | @No__t-0 av `Git`-lagringsplatsen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@` eller `ssh://`. |
| `default-label` | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name` eller `commit-id` av lagrings platsen. |
| `search-paths`  | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `username`      | `no`     | @No__t-0 som används för att få åtkomst till `Git`-lagringsplatsen, __krävs__ för `Git`-lagringsplats `Http Basic Authentication`. |
| `password`      | `no`     | Lösen ordet som används för att komma åt `Git`-lagringsplatsen, som __krävs__ för `Git`-lagringsplatsen server stöd `Http Basic Authentication`. |

> [!NOTE]
> Vissa `Git` databas servrar, t. ex. GitHub, stöder en "personlig token" eller "Access-token" som ett lösen ord för `HTTP Basic Authentication`. Du kan använda denna typ av token som lösen ord här, och "personlig token" eller "Access-token" upphör att gälla. Men för git-lagringsplatser som BitBucket och Azure DevOps går token ut i en eller två timmar, vilket gör att alternativet inte är giltigt för användning med Azure våren Cloud.

### <a name="git-repositories-with-pattern"></a>Git-databaser med mönster

Alla konfigurerbara egenskaper som används för att konfigurera git-databaser med mönster visas nedan.

> [!NOTE]
> Endast bindestreck ("-") namngivnings konventionen för avgränsade ord stöds för tillfället. Du kan till exempel använda `default-label` men inte `defaultLabel`.

| Egenskap                           | Krävs         | Funktion                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | En karta består av `Git`-Arkiv inställningar med det namnet. |
| `repos."uri"`                      | `yes` på `repos` | @No__t-0 av `Git`-lagringsplatsen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@` eller `ssh://`. |
| `repos."name"`                     | `yes` på `repos` | Ett namn för att identifiera en `Git`-lagringsplats, __krävs__ endast om `repos` finns. Till exempel ovan, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | En sträng mat ris som används för att matcha program namnet, för varje mönster tar `{application}/{profile}`-format med jokertecken. |
| `repos."default-label"`            | `no`             | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name` eller `commit-id` av lagrings platsen. |
| `repos."search-paths`"             | `no`             | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `repos."username"`                 | `no`             | @No__t-0 som används för att få åtkomst till `Git`-lagringsplatsen, __krävs__ för `Git`-lagringsplats `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Lösen ordet som används för att komma åt `Git`-lagringsplatsen, som __krävs__ för `Git`-lagringsplatsen server stöd `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Den `Ssh` privata nyckeln för att komma åt `Git`-lagringsplats, __krävs__ när `uri` startade med `git@` eller `ssh://`. |
| `repos."host-key"`                 | `no`             | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Värd nyckelalgoritm måste vara en av `ssh-dss`. `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384` och `ecdsa-sha2-nistp521`. __Krävs__ endast om `host-key` finns. |
| `repos."strict-host-key-checking"` | `no`             | Indikerar om konfigurations servern inte kan starta när du använder ange `host-key`, ska vara `true` (standardvärde) eller `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importera `application.yml`-fil från vår moln konfiguration

Du kan importera vissa standardinställningar för konfigurations servern direkt från [konfigurations webbplatsen för vår moln konfiguration](https://spring.io/projects/spring-cloud-config) . Du kan göra detta direkt från Azure Portal, så du behöver inte vidta några steg nu för att förbereda dina config server-filer eller-lagrings platser.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Ansluta din konfigurations Server databas till Azure våren Cloud

Nu när du har sparat konfigurationsfilerna i en lagrings plats måste du ansluta Azure våren Cloud till den.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Gå till fliken **konfigurations Server** under rubriken **Inställningar** på menyn på vänster sida.

### <a name="public-repository"></a>Offentligt lager

Om lagrings platsen är offentlig klickar du bara på knappen **offentlig** och klistrar in URL: en.

### <a name="private-repository"></a>Privat lagring

Azure våren Cloud stöder SSH-autentisering. Följ anvisningarna på Azure Portal för att lägga till den offentliga nyckeln till din lagrings plats. Var noga med att ta med din privata nyckel i konfigurations filen.

Klicka på **tillämpa** för att slutföra konfigurationen av konfigurations servern.


## <a name="delete-your-app-configuration"></a>Ta bort din app-konfiguration

När du har sparat en konfigurations fil visas knappen **ta bort app-konfiguration** på fliken **konfiguration** . Detta tar bort dina befintliga inställningar helt. Du bör göra detta om du vill ansluta din konfigurations server till en annan källa, till exempel flytta från GitHub till Azure DevOps.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar och konfigurerar konfigurations servern. Om du vill veta mer om hur du hanterar programmet fortsätter du till självstudien för manuell skalning av appen.

> [!div class="nextstepaction"]
> [Lär dig hur du manuellt skalar ditt Azure våren Cloud-program](spring-cloud-tutorial-scale-manual.md).

