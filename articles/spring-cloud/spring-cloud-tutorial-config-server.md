---
title: Självstudiekurs - Konfigurera din Config Server-instans i Azure Spring Cloud
description: I den här självstudien får du lära dig hur du konfigurerar en Spring Cloud Config Server-instans för ditt Azure Spring Cloud på Azure-portalen
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277532"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Självstudiekurs: Konfigurera en Spring Cloud Config Server-instans för din tjänst

Den här artikeln visar hur du ansluter en Spring Cloud Config Server-instans till din Azure Spring Cloud-tjänst.

Spring Cloud Config ger stöd på server- och klientsidan för en extern konfiguration i ett distribuerat system. Med Config Server-instansen har du en central plats för att hantera externa egenskaper för program i alla miljöer. Mer information finns i [Spring Cloud Config Server-referens](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 
* En redan etablerad och körande Azure Spring Cloud-tjänst. Information om hur du konfigurerar och startar en Azure Spring Cloud-tjänst finns i [Snabbstart: Starta ett Java Spring-program med hjälp av Azure CLI](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Begränsning

Det finns vissa begränsningar när du använder Config Server med en Git-server. Vissa egenskaper injiceras automatiskt i programmiljön för att komma åt Identifiering av Konfigurationsserver och tjänst. Om du också konfigurerar dessa egenskaper från Config Server-filerna kan det uppstå konflikter och oväntade problem. Egenskaperna inkluderar: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Vi rekommenderar starkt att du _inte_ placerar ovanstående egenskaper i dina Config Server-programfiler.

## <a name="create-your-config-server-files"></a>Skapa dina Config Server-filer

Azure Spring Cloud stöder Azure DevOps, GitHub, GitLab och Bitbucket för lagring av config-serverfiler. När du har arkivet klart skapar du konfigurationsfilerna med följande instruktioner och lagrar dem där.

Dessutom är vissa konfigurerbara egenskaper endast tillgängliga för vissa typer. I följande underavsnitt visas egenskaperna för varje databastyp.

### <a name="public-repository"></a>Offentligt arkiv

När du använder en offentlig databas är dina konfigurerbara egenskaper mer begränsade.

Alla konfigurerbara egenskaper som används för att ställa in den offentliga Git-databasen visas i följande tabell:

> [!NOTE]
> Att använda ett bindestreck (-) för att separera ord är den enda namngivningskonvention som för närvarande stöds. Du kan till exempel använda *standardetikett*, men inte *standardLabel*.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | Uri-databasen för Git som används som Config Server-server-server-server-server börjar med *http://,* *https://,* *git@* eller *ssh://*. |
| `default-label` | Inga     | Standardetiketten för Git-databasen ska vara *grennamnet,* *taggnamnet*eller *commit-id* för databasen. |
| `search-paths`  | Inga     | En matris med strängar som används för att söka efter underkataloger i Git-databasen. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privat databas med SSH-autentisering

Alla konfigurerbara egenskaper som används för att konfigurera en privat Git-databas med SSH visas i följande tabell:

> [!NOTE]
> Att använda ett bindestreck (-) för att separera ord är den enda namngivningskonvention som för närvarande stöds. Du kan till exempel använda *standardetikett*, men inte *standardLabel*.

| Egenskap                   | Krävs | Funktion                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | URI-databasen för Git som används som Config Server-server-serverserverns serverslutning ska startas med *http://*, *https://,* *git@* eller *ssh://*. |
| `default-label`            | Inga     | Standardetiketten för Git-databasen ska vara *grennamnet,* *taggnamnet*eller *commit-id* för databasen. |
| `search-paths`             | Inga     | En matris med strängar som används för att söka efter underkataloger i Git-databasen. |
| `private-key`              | Inga     | Den privata SSH-nyckeln för att komma åt Git-databasen, _som krävs_ när URI börjar med *git@* eller *ssh://*. |
| `host-key`                 | Inga     | Värdnyckeln för Git-databasservern bör inte innehålla algoritmprefixet som omfattas av `host-key-algorithm`. |
| `host-key-algorithm`       | Inga     | Värdnyckelalgoritmen, bör *vara ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, eller *ecdsa-sha2-nistp521*. *Krävs* endast `host-key` om det finns. |
| `strict-host-key-checking` | Inga     | Anger om Config Server-instansen inte startar när `host-key`den privata . Ska vara *sant* (standardvärde) eller *falskt*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privat databas med grundläggande autentisering

Alla konfigurerbara egenskaper som används för att konfigurera privata Git-databaser med grundläggande autentisering visas nedan.

> [!NOTE]
> Att använda ett bindestreck (-) för att separera ord är den enda namngivningskonvention som för närvarande stöds. Använd till exempel *standardetikett*, inte *standardLabel*.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | Uri-databasen för Git som används som Config Server-server-server ska startas med *http://,* *https://,* *git@* eller *ssh://*. |
| `default-label` | Inga     | Standardetiketten för Git-databasen ska vara *grennamnet,* *taggnamnet*eller *commit-id* för databasen. |
| `search-paths`  | Inga     | En matris med strängar som används för att söka efter underkataloger i Git-databasen. |
| `username`      | Inga     | Användarnamnet som används för att komma åt Git-databasservern `Http Basic Authentication`som _krävs_ när Git-databasservern stöder . |
| `password`      | Inga     | Lösenordet som används för att komma åt Git-databasservern _som krävs_ när Git-databasservern stöder `Http Basic Authentication`. |

> [!NOTE]
> Många `Git` databasservrar stöder användning av token i stället för lösenord för HTTP Basic-autentisering. Vissa databaser, till exempel GitHub, tillåter att token kvarstår på obestämd tid. Vissa Git-databasservrar, inklusive Azure DevOps, tvingar dock token att upphöra att gälla inom några timmar. Databaser som orsakar att token upphör att gälla bör inte använda tokenbaserad autentisering med Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Git-databaser med mönster

Alla konfigurerbara egenskaper som används för att ställa in Git-databaser med mönster visas nedan.

> [!NOTE]
> Att använda ett bindestreck (-) för att separera ord är den enda namngivningskonvention som för närvarande stöds. Använd till exempel *standardetikett*, inte *standardLabel*.

| Egenskap                           | Krävs         | Funktion                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Inga             | En karta som består av inställningarna för en Git-databas med ett förnamn. |
| `repos."uri"`                      | Ja på`repos` | Uri-databasen för Git som används som Config Server-server-server ska startas med *http://,* *https://,* *git@* eller *ssh://*. |
| `repos."name"`                     | Ja på`repos` | Ett namn som ska identifieras i Git-databasen _krävs_ endast om `repos` det finns något. Till exempel *team-A*, *team-B*. |
| `repos."pattern"`                  | Inga             | En matris med strängar som används för att matcha ett programnamn. Använd `{application}/{profile}` formatet med jokertecken för varje mönster. |
| `repos."default-label"`            | Inga             | Standardetiketten för Git-databasen ska vara *grennamnet,* *taggnamnet*eller *commit-id* för databasen. |
| `repos."search-paths`"             | Inga             | En matris med strängar som används för att söka efter underkataloger i Git-databasen. |
| `repos."username"`                 | Inga             | Användarnamnet som används för att komma åt Git-databasservern `Http Basic Authentication`som _krävs_ när Git-databasservern stöder . |
| `repos."password"`                 | Inga             | Lösenordet som används för att komma åt Git-databasservern _som krävs_ när Git-databasservern stöder `Http Basic Authentication`. |
| `repos."private-key"`              | Inga             | Den privata SSH-nyckeln för att komma åt Git-databasen _som krävs_ när URI:n börjar med *git@* eller *ssh://*. |
| `repos."host-key"`                 | Inga             | Värdnyckeln för Git-databasservern bör inte innehålla algoritmprefixet som omfattas av `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Inga             | Värdnyckelalgoritmen, bör *vara ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, eller *ecdsa-sha2-nistp521*. *Krävs* endast `host-key` om det finns. |
| `repos."strict-host-key-checking"` | Inga             | Anger om Config Server-instansen inte startar när `host-key`den privata . Ska vara *sant* (standardvärde) eller *falskt*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Bifoga din Config Server-databas till Azure Spring Cloud

Nu när dina konfigurationsfiler har sparats i en databas måste du ansluta Azure Spring Cloud till den.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till sidan **översikt över** Azure Spring Cloud.

1. Välj den tjänst som ska konfigureras.

1. Välj fliken **Konfigurationsserver** under **Inställningar**i den vänstra rutan på tjänstsidan.

![Fönstret Config Server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Ange databasinformation direkt till Azure-portalen

#### <a name="default-repository"></a>Standarddatabas

* **Offentlig databas**: Klistra in databasens URI i **rutan** **Standarddatabas.**  Ställ in **etiketten** på **config**. Kontrollera att **autentiseringsinställningen** är **offentlig**och välj sedan **Använd** för att slutföra. 

* **Privat databas**: Azure Spring Cloud stöder grundläggande lösenord/tokenbaserad autentisering och SSH.

    * **Grundläggande autentisering**: I avsnittet **Standarddatabas** i rutan **Uri** klistrar du in databas-URI:n och väljer sedan knappen **Autentisering** ("penna"). I fönstret **Redigera autentisering** i listrutan **Autentiseringstyp** väljer du **HTTP Basic**och anger sedan ditt användarnamn och lösenord/token för att bevilja åtkomst till Azure Spring Cloud. Välj **OK**och välj sedan **Använd** för att slutföra konfigurationen av Konfigurationsserverinstansen.

    ![Fönstret Redigera autentisering](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Vissa Git-databasservrar, till exempel GitHub, använder en *personlig token* eller en *åtkomsttoken*, till exempel ett lösenord, för **grundläggande autentisering**. Du kan använda den typen av token som ett lösenord i Azure Spring Cloud, eftersom den aldrig upphör att gälla. Men för andra Git-databasservrar, till exempel Bitbucket och Azure DevOps, upphör *åtkomsttoken* att gälla om en eller två timmar. Det innebär att alternativet inte är genomförbart när du använder dessa databasservrar med Azure Spring Cloud.

    * **SSH**: I avsnittet **Standarddatabas** i rutan **Uri** klistrar du in databas-URI:n och väljer sedan knappen **Autentisering** ("penna"). Välj **SSH**i listrutan **Redigera autentisering** i listrutan **Autentiseringstyp** och ange sedan din **privata nyckel**. Du kan också ange **värdnyckeln** och **värdnyckelalgoritmen**. Var noga med att inkludera din offentliga nyckel i Config Server-databasen. Välj **OK**och välj sedan **Använd** för att slutföra konfigurationen av Konfigurationsserverinstansen.

    ![Fönstret Redigera autentisering](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Mönsterdatabas

Om du vill använda en valfri **mönsterdatabas** för att konfigurera tjänsten anger du **URI** och **Autentisering** på samma sätt som **standarddatabasen**. Var noga med att inkludera ett **namn** för ditt mönster och välj sedan **Använd** för att bifoga det till din instans. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Ange databasinformation i en YAML-fil

Om du har skrivit en YAML-fil med dina databasinställningar kan du importera filen direkt från din lokala dator till Azure Spring Cloud. En enkel YAML-fil för en privat databas med grundläggande autentisering skulle se ut så här:

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

Välj knappen **Importera inställningar** och välj sedan YAML-filen i projektkatalogen. Välj **Importera**och `async` sedan visas en åtgärd från **aviseringarna.** Efter 1-2 minuter, bör det rapportera framgång.

![Fönstret Konfigurationsservermeddelanden](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informationen från YAML-filen ska visas i Azure-portalen. Välj **Använd** för att avsluta. 


## <a name="delete-your-app-configuration"></a>Ta bort appkonfigurationen

När du har sparat en konfigurationsfil visas **knappen Ta bort appkonfiguration** på fliken **Konfiguration.** Du bör välja det om du vill ansluta din Config Server-instans till en annan källa, till exempel flytta från GitHub till Azure DevOps.



## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du aktiverar och konfigurerar din Spring Cloud Config Server-instans. Om du vill veta mer om hur du hanterar ditt program fortsätter du till självstudien om att manuellt skala appen.

> [!div class="nextstepaction"]
> [Självstudiekurs: Skala ett program i Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)
