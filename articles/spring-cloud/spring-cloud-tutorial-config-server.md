---
title: Konfigurera konfigurations servern i Azure våren-molnet | Microsoft Docs
description: I den här självstudien får du lära dig hur du konfigurerar en vår moln konfigurations Server för ditt Azure våren-moln på Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 6cf7b4a52ba3a7dbda5fa3fa558c4b68d09f4eb2
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646713"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Självstudie: Konfigurera en fjäder moln konfigurations Server för din tjänst

I den här självstudien får du se hur du ansluter en fjäder moln konfigurations server till din Azure våren Cloud-tjänst.

Våren Cloud config tillhandahåller stöd för Server-och klient sidan för den externa konfigurationen i ett distribuerat system. Med konfigurations servern har du en central plats för att hantera externa egenskaper för program i alla miljöer. Mer information finns i [vår referens för vår moln konfigurations Server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Nödvändiga komponenter
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En Azure våren Cloud-tjänst som redan har tillhandahållits och körs.  Slutför [den här snabb](spring-cloud-quickstart-launch-app-cli.md) starten för att etablera och starta en Azure våren Cloud-tjänst.

## <a name="restriction"></a>Begränsning

Det finns vissa begränsningar när du använder __konfigurations servern__ med en git-Server del. Vissa egenskaper kommer automatiskt att matas in i din program miljö för att få åtkomst till __konfigurations Server__ och __tjänst identifiering__. Om du även konfigurerar dessa egenskaper från **config server** -filerna kan det uppstå konflikter och oväntade beteenden. Egenskaperna är: 

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

Azure våren Cloud stöder Azure DevOps, GitHub, GitLab och Bitbucket för att lagra dina config server-filer. När du är klar med din lagrings plats kan du göra konfigurationsfilerna enligt anvisningarna nedan och lagra dem där.

Dessutom är vissa konfigurerbara egenskaper bara tillgängliga för vissa typer. I följande underavsnitt visas egenskaperna för varje typ av lagrings plats.

### <a name="public-repository"></a>Offentligt lager

När du använder ett offentligt lager kommer dina konfigurerbara egenskaper att bli mer begränsade.

Alla konfigurerbara egenskaper som används för att konfigurera den offentliga `Git`-lagringsplatsen visas nedan.

> [!NOTE]
> Att använda bindestreck ("-") för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Du kan till exempel använda `default-label`, men inte `defaultLabel`.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` av `Git`s databasen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@`eller `ssh://`. |
| `default-label` | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name`eller `commit-id` av lagrings platsen. |
| `search-paths`  | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privat lagring med SSH-autentisering

Alla konfigurerbara egenskaper som används för att konfigurera privat `Git`-lagringsplatsen med `Ssh` visas nedan.

> [!NOTE]
> Att använda bindestreck ("-") för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Du kan till exempel använda `default-label`, men inte `defaultLabel`.

| Egenskap                   | Krävs | Funktion                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | `uri` av `Git`s databasen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@`eller `ssh://`. |
| `default-label`            | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name`eller `commit-id` av lagrings platsen. |
| `search-paths`             | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `private-key`              | `no`     | Den `Ssh` privata nyckeln för att komma åt `Git`-lagringsplatsen, som __krävs__ när `uri` börjar med `git@` eller `ssh://`. |
| `host-key`                 | `no`     | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | Algoritmen för värd nycklar ska vara `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`eller `ecdsa-sha2-nistp521`. Krävs endast om `host-key` finns. |
| `strict-host-key-checking` | `no`     | Anger om konfigurations servern inte kan starta när den privata `host-key`används. Ska vara `true` (standardvärde) eller `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privat lagrings plats med grundläggande autentisering

Alla konfigurerbara egenskaper som används för att konfigurera en privat git-lagringsplats med grundläggande autentisering visas nedan.

> [!NOTE]
> Att använda bindestreck ("-") för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Använd till exempel `default-label` inte `defaultLabel`.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | `uri` av `Git`s databasen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@`eller `ssh://`. |
| `default-label` | `no`     | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name`eller `commit-id` av lagrings platsen. |
| `search-paths`  | `no`     | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `username`      | `no`     | `username` som används för att komma åt `Git`-lagringsplatsen, vilket __krävs__ när `Git`-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `password`      | `no`     | Lösen ordet som används för att komma åt `Git` databas servern, vilket __krävs__ när `Git`-lagringsplatsen har stöd för `Http Basic Authentication`. |

> [!NOTE]
> Vissa `Git` lagrings servrar, t. ex. GitHub, stöder en "personlig token" eller "Access-token" som ett lösen ord för `HTTP Basic Authentication`. Du kan använda denna typ av token som lösen ord här, och "personlig token" eller "Access-token" upphör att gälla. Men för git-lagringsplatser som BitBucket och Azure DevOps går token ut i en eller två timmar, vilket gör att alternativet inte är giltigt för användning med Azure våren Cloud.

### <a name="git-repositories-with-pattern"></a>Git-databaser med mönster

Alla konfigurerbara egenskaper som används för att konfigurera git-databaser med mönster visas nedan.

> [!NOTE]
> Att använda bindestreck ("-") för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Använd till exempel `default-label` inte `defaultLabel`.

| Egenskap                           | Krävs         | Funktion                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | En karta som består av inställningarna för en `Git`-lagringsplats med ett angivet namn. |
| `repos."uri"`                      | `yes` på `repos` | `uri` av `Git`s databasen som används som konfigurations Server Server del bör startas med `http://`, `https://`, `git@`eller `ssh://`. |
| `repos."name"`                     | `yes` på `repos` | Ett namn för att identifiera en `Git`-lagringsplats, __krävs__ endast om `repos` finns. Till exempel ovan, `team-A`, `team-B`. |
| `repos."pattern"`                  | `no`             | En sträng mat ris som används för att matcha ett program namn. Använd `{application}/{profile}` format med jokertecken för varje mönster. |
| `repos."default-label"`            | `no`             | Standard etiketten för `Git`-lagringsplatsen bör vara `branch name`, `tag name`eller `commit-id` av lagrings platsen. |
| `repos."search-paths`"             | `no`             | En sträng mat ris som används för att söka i under kataloger i `Git`-lagringsplatsen. |
| `repos."username"`                 | `no`             | `username` som används för att komma åt `Git`-lagringsplatsen, vilket __krävs__ när `Git`-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | Lösen ordet som används för att komma åt `Git` databas servern, vilket __krävs__ när `Git`-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | Den `Ssh` privata nyckeln för att komma åt `Git`-lagringsplatsen, som __krävs__ när `uri` börjar med `git@` eller `ssh://`. |
| `repos."host-key"`                 | `no`             | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | Algoritmen för värd nycklar ska vara `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`eller `ecdsa-sha2-nistp521`. __Krävs__ endast om `host-key` finns. |
| `repos."strict-host-key-checking"` | `no`             | Anger om konfigurations servern inte kan starta när den privata `host-key`används. Ska vara `true` (standardvärde) eller `false`. |

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Ansluta din konfigurations Server databas till Azure våren Cloud

Nu när du har sparat konfigurationsfilerna i en lagrings plats måste du ansluta Azure våren Cloud till den.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Gå till fliken **konfigurations Server** under rubriken **Inställningar** på menyn på vänster sida.

![fönster skärm bild](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="input-repository-information-directly-to-the-azure-portal"></a>Information om indata från lagrings plats direkt till Azure Portal

#### <a name="default-repository"></a>Standard databas

* Offentligt lager: i avsnittet **standard databas** klistrar du in lagrings platsens URI i **URI** -avsnittet.  Ange **etiketten** till `config`. Se till att **autentiseringsinställningarna** är **offentlig**och välj sedan **tillämpa** på Slutför. 

* Privat lagrings plats: Azure våren Cloud har stöd för grundläggande lösen ords-/token-baserad autentisering och SSH.

    * Grundläggande autentisering: i avsnittet **standard databas** klistrar du in lagrings platsens URI i **URI** -avsnittet och klickar sedan på **autentiseringen**. Välj **grundläggande** som **Autentiseringstyp** och ange ditt användar namn och lösen ord/token för att bevilja åtkomst till Azure våren Cloud. Klicka på **OK** och **Verkställ** för att slutföra konfigurationen av konfigurations servern.

    ![fönster skärm bild](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Vissa git-lagringsplatser som GitHub använder en `personal-token` eller en `access-token` som ett lösen ord för **grundläggande autentisering**. Du kan använda den typen av token som lösen ord i Azure våren Cloud, eftersom den aldrig upphör att gälla. Men för andra git-lagringsplatser som BitBucket och Azure DevOps går `access-token` ut om en eller två timmar. Det innebär att alternativet inte är livskraftigt när du använder dessa databas servrar med Azure våren Cloud.]

    * SSH: i avsnittet **standard databas** klistrar du in lagrings platsens URI i **URI** -avsnittet och klickar sedan på **autentiseringen**. Välj **SSH** som **Autentiseringstyp** och ange din **privata nyckel**. Du kan också ange **värd nyckel** och **värd**nyckelalgoritm. Se till att inkludera din offentliga nyckel i konfigurations serverns lagrings plats. Klicka på **OK** och **Verkställ** för att slutföra konfigurationen av konfigurations servern.

    ![fönster skärm bild](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Mönster Central

Om du vill använda en valfri **mönster lagrings plats** för att konfigurera tjänsten anger du **URI** och **autentisering** på samma sätt som **standard lagrings platsen**. Se till att lägga till ett **namn** för mönstret och klicka sedan på **Använd** för att koppla det till din instans. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Ange lagrings plats information i en YAML-fil

Om du har skrivit en YAML-fil med dina lagrings plats inställningar kan du importera YAML-filen direkt från den lokala datorn till Azure våren Cloud. En enkel YAML-fil för ett privat lager med grundläggande autentisering ser ut så här:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Klicka på knappen **Importera inställningar** och välj sedan `.yml`-filen från projekt katalogen. Klicka på **Importera**. därefter visas en `async` åtgärd från dina **aviseringar** . Efter 1-2 minuter bör det rapporteras som slutfört.

![fönster skärm bild](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Du bör se informationen från din YAML-fil som visas i Azure Portal. Klicka på **tillämpa** för att slutföra. 


## <a name="delete-your-app-configuration"></a>Ta bort din app-konfiguration

När du har sparat en konfigurations fil visas knappen **ta bort app-konfiguration** på fliken **konfiguration** . Detta tar bort dina befintliga inställningar helt. Du bör göra detta om du vill ansluta din konfigurations server till en annan källa, till exempel flytta från GitHub till Azure DevOps.



## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar och konfigurerar konfigurations servern. Om du vill veta mer om hur du hanterar programmet fortsätter du till självstudien för manuell skalning av appen.

> [!div class="nextstepaction"]
> [Lär dig hur du manuellt skalar ditt Azure våren Cloud-program](spring-cloud-tutorial-scale-manual.md).

