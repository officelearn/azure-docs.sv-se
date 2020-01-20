---
title: Självstudie – konfigurera konfigurations Server instansen i Azure våren Cloud
description: I den här självstudien får du lära dig hur du konfigurerar en våren Cloud config Server-instans för ditt Azure våren-moln på Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277532"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Självstudie: Konfigurera en våren Cloud config Server-instans för din tjänst

Den här artikeln visar hur du ansluter en våren Cloud config Server-instans till din Azure våren Cloud-tjänst.

Våren Cloud config tillhandahåller stöd för Server-och klient sidan för en extern konfiguration i ett distribuerat system. Med konfigurations Server instansen har du en central plats för att hantera externa egenskaper för program i alla miljöer. Mer information finns i [referens för vår moln konfigurations Server](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Krav
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En Azure våren Cloud-tjänst som redan har tillhandahållits och körs. Information om hur du konfigurerar och startar en Azure våren Cloud-tjänst finns i [snabb start: starta ett Java våren-program med hjälp av Azure CLI](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Begränsning

Det finns vissa begränsningar när du använder konfigurations servern med en git-backend. Vissa egenskaper matas automatiskt in i din program miljö för att få åtkomst till konfigurations Server och tjänst identifiering. Om du även konfigurerar dessa egenskaper från config server-filerna kan det uppstå konflikter och oväntade beteenden. Egenskaperna är: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Vi rekommenderar starkt att du _inte_ lägger till egenskaperna ovan i config server-programfilerna.

## <a name="create-your-config-server-files"></a>Skapa dina config server-filer

Azure våren Cloud stöder Azure DevOps, GitHub, GitLab och Bitbucket för att lagra dina config server-filer. När du har en lagrings plats kan du skapa konfigurationsfilerna med följande instruktioner och lagra dem där.

Dessutom är vissa konfigurerbara egenskaper bara tillgängliga för vissa typer. I följande underavsnitt visas egenskaperna för varje typ av lagrings plats.

### <a name="public-repository"></a>Offentligt lager

När du använder ett offentligt lager är dina konfigurerbara egenskaper mer begränsade.

Alla konfigurerbara egenskaper som används för att konfigurera den offentliga git-lagringsplatsen visas i följande tabell:

> [!NOTE]
> Att använda bindestreck (-) för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Du kan till exempel använda *default-Label*, men inte *defaultLabel*.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | URI: n för git-lagringsplatsen som används som konfigurations serverns Server del börjar med *http://* , *https://* , *git@* eller *SSH://* . |
| `default-label` | Inga     | Standard etiketten för git-lagringsplatsen ska vara *gren namnet*, *taggnamnet*eller lagrings- *ID* för lagrings platsen. |
| `search-paths`  | Inga     | En sträng mat ris som används för att söka efter under kataloger i git-lagringsplatsen. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privat lagring med SSH-autentisering

Alla konfigurerbara egenskaper som används för att konfigurera en privat git-lagringsplats med SSH visas i följande tabell:

> [!NOTE]
> Att använda bindestreck (-) för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Du kan till exempel använda *default-Label*, men inte *defaultLabel*.

| Egenskap                   | Krävs | Funktion                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Ja    | Den URI för git-lagringsplatsen som används som konfigurations serverns Server del bör startas med *http://* , *https://* , *git@* eller *SSH://* . |
| `default-label`            | Inga     | Standard etiketten för git-lagringsplatsen ska vara *gren namnet*, *taggnamnet*eller lagrings- *ID* för lagrings platsen. |
| `search-paths`             | Inga     | En sträng mat ris som används för att söka i under kataloger på git-lagringsplatsen. |
| `private-key`              | Inga     | Den privata SSH-nyckeln för åtkomst till git-lagringsplatsen, som _krävs_ när URI: n börjar med *git@* eller *SSH://* . |
| `host-key`                 | Inga     | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `host-key-algorithm`       | Inga     | Algoritmen för värd nycklar ska vara *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*eller *ECDSA-SHA2-nistp521*. *Krävs* endast om `host-key` finns. |
| `strict-host-key-checking` | Inga     | Anger om konfigurations Server instansen inte kan starta när den privata `host-key`används. Ska vara *Sant* (standardvärde) eller *falskt*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Privat lagrings plats med grundläggande autentisering

Alla konfigurerbara egenskaper som används för att konfigurera en privat git-lagringsplats med grundläggande autentisering visas nedan.

> [!NOTE]
> Att använda bindestreck (-) för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Använd till exempel *standard-etikett*, inte *defaultLabel*.

| Egenskap        | Krävs | Funktion                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Ja    | URI: n för git-lagringsplatsen som används som konfigurations serverns Server del bör startas med *http://* , *https://* , *git@* eller *SSH://* . |
| `default-label` | Inga     | Standard etiketten för git-lagringsplatsen ska vara *gren namnet*, *taggnamnet*eller lagrings- *ID* för lagrings platsen. |
| `search-paths`  | Inga     | En sträng mat ris som används för att söka i under kataloger på git-lagringsplatsen. |
| `username`      | Inga     | Det användar namn som används för att få åtkomst till git-lagringsplatsen, vilket _krävs_ när git-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `password`      | Inga     | Lösen ordet som används för att få åtkomst till git-lagringsplatsen, vilket _krävs_ när git-lagringsplatsen har stöd för `Http Basic Authentication`. |

> [!NOTE]
> Många `Git` lagrings plats servrar stöder token i stället för lösen ord för HTTP Basic-autentisering. Vissa lagrings platser, till exempel GitHub, tillåter token att kvarhållas på obestämd tid. Vissa git-lagringsplatser, inklusive Azure DevOps, tvingar dock token att gå ut inom några timmar. Databaser som gör att token upphör att gälla bör inte använda tokenbaserad autentisering med Azure våren Cloud.

### <a name="git-repositories-with-pattern"></a>Git-databaser med mönster

Alla konfigurerbara egenskaper som används för att konfigurera git-databaser med mönster visas nedan.

> [!NOTE]
> Att använda bindestreck (-) för att avgränsa ord är den enda namngivnings konventionen som stöds för närvarande. Använd till exempel *standard-etikett*, inte *defaultLabel*.

| Egenskap                           | Krävs         | Funktion                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Inga             | En karta som består av inställningarna för en git-lagringsplats med ett angivet namn. |
| `repos."uri"`                      | Ja på `repos` | URI: n för git-lagringsplatsen som används som konfigurations serverns Server del bör startas med *http://* , *https://* , *git@* eller *SSH://* . |
| `repos."name"`                     | Ja på `repos` | Ett namn som ska identifieras på git-lagringsplatsen, _krävs_ endast om `repos` finns. Till exempel *Team-A*, *team-B*. |
| `repos."pattern"`                  | Inga             | En sträng mat ris som används för att matcha ett program namn. Använd `{application}/{profile}` format med jokertecken för varje mönster. |
| `repos."default-label"`            | Inga             | Standard etiketten för git-lagringsplatsen ska vara *gren namnet*, *taggnamnet*eller lagrings- *ID* för lagrings platsen. |
| `repos."search-paths`"             | Inga             | En sträng mat ris som används för att söka i under kataloger på git-lagringsplatsen. |
| `repos."username"`                 | Inga             | Det användar namn som används för att få åtkomst till git-lagringsplatsen, vilket _krävs_ när git-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `repos."password"`                 | Inga             | Lösen ordet som används för att få åtkomst till git-lagringsplatsen, vilket _krävs_ när git-lagringsplatsen har stöd för `Http Basic Authentication`. |
| `repos."private-key"`              | Inga             | Den privata SSH-nyckeln för att komma åt git-lagringsplats _krävs_ när URI: n börjar med *git@* eller *SSH://* . |
| `repos."host-key"`                 | Inga             | Värd nyckeln för git-lagringsplatsen bör inte innehålla prefixet algoritm som omfattas av `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Inga             | Algoritmen för värd nycklar ska vara *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*eller *ECDSA-SHA2-nistp521*. *Krävs* endast om `host-key` finns. |
| `repos."strict-host-key-checking"` | Inga             | Anger om konfigurations Server instansen inte kan starta när den privata `host-key`används. Ska vara *Sant* (standardvärde) eller *falskt*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Koppla din konfigurations Server lagring till Azure våren Cloud

Nu när konfigurationsfilerna sparas i en lagrings plats måste du ansluta Azure våren Cloud till den.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Välj den tjänst som ska konfigureras.

1. I den vänstra rutan på sidan tjänst går du till **Inställningar**och väljer fliken **konfigurations Server** .

![Fönstret konfigurations Server](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Ange lagrings plats information direkt till Azure Portal

#### <a name="default-repository"></a>Standard databas

* **Offentligt lager**: i rutan **URI** i rutan **standard lagring** klistrar du in lagrings-URI: n.  Ställ in **etiketten** på **config**. Se till att **autentiseringsinställningarna** är **offentlig**och välj sedan **tillämpa** på Slutför. 

* **Privat lagring**: Azure våren Cloud stöder grundläggande lösen ord/tokenbaserad autentisering och SSH.

    * **Grundläggande autentisering**: i rutan **URI** i rutan **standard plats** klistrar du in lagrings platsens URI och väljer sedan knappen för **autentisering** (Penn ikonen). I list rutan **Autentiseringstyp** i rutan **Redigera autentisering** väljer du **http Basic**och anger sedan ditt användar namn och lösen ord/token för att ge åtkomst till Azure våren-molnet. Välj **OK**och välj sedan **tillämpa** för att slutföra konfigurationen av konfigurations Server instansen.

    ![Fönstret Redigera autentisering](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Vissa git-lagringsplatser, till exempel GitHub, använder en *personlig token* eller en *åtkomsttoken*, till exempel ett lösen ord, för **grundläggande autentisering**. Du kan använda den typen av token som ett lösen ord i Azure våren Cloud, eftersom det aldrig upphör att gälla. Men för andra git-lagringsplatser, till exempel BitBucket och Azure DevOps, förfaller *Access-token* på en eller två timmar. Det innebär att alternativet inte är livskraftigt när du använder dessa databas servrar med Azure våren Cloud.

    * **SSH**: i rutan **standard databas** i rutan **URI** klistrar du in lagrings platsens URI och väljer sedan knappen för **autentisering** (Penn ikonen). I list rutan **Autentiseringstyp** i rutan **Redigera autentisering** väljer du **SSH**och anger sedan din **privata nyckel**. Du kan också ange **värd nyckel** och **värd**nyckelalgoritm. Se till att inkludera din offentliga nyckel i konfigurations serverns lagrings plats. Välj **OK**och välj sedan **tillämpa** för att slutföra konfigurationen av konfigurations Server instansen.

    ![Fönstret Redigera autentisering](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Mönster Central

Om du vill använda en valfri **mönster lagrings plats** för att konfigurera tjänsten anger du **URI** och **autentisering** på samma sätt som **standard lagrings platsen**. Se till att lägga till ett **namn** för ditt mönster och välj sedan **Använd** för att koppla det till din instans. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Ange lagrings plats information i en YAML-fil

Om du har skrivit en YAML-fil med dina lagrings plats inställningar kan du importera filen direkt från den lokala datorn till Azure våren Cloud. En enkel YAML-fil för ett privat lager med grundläggande autentisering ser ut så här:

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

Välj knappen **Importera inställningar** och välj sedan yaml-filen från projekt katalogen. Välj **Importera**, så visas en `async` åtgärd från dina **aviseringar** . Efter 1-2 minuter bör det rapporteras som slutfört.

![Fönstret konfigurations Server meddelanden](media/spring-cloud-tutorial-config-server/local-yml-success.png)


Informationen från din YAML-fil bör visas i Azure Portal. Välj **tillämpa** på Slutför. 


## <a name="delete-your-app-configuration"></a>Ta bort din app-konfiguration

När du har sparat en konfigurations fil visas knappen **ta bort app-konfiguration** på fliken **konfiguration** . om du väljer den här knappen raderas dina befintliga inställningar helt och hållet. Du bör välja den om du vill ansluta din konfigurations Server instans till en annan källa, till exempel flytta från GitHub till Azure DevOps.



## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar och konfigurerar din våren Cloud config Server-instans. Om du vill veta mer om hur du hanterar programmet fortsätter du till självstudien om hur du skalar appen manuellt.

> [!div class="nextstepaction"]
> [Självstudie: skala ett program i Azure våren Cloud](spring-cloud-tutorial-scale-manual.md)
