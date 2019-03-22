---
title: (INAKTUELL) Hantera Azure DC/OS-kluster med Användargränssnittet för Marathon
description: Distribuera behållare till en klustertjänst i Azure Container Service med Marathons webbgränssnitt.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 16c16c0217a796ffbb57e10430f90cb4a7660ac6
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317620"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(INAKTUELL) Hantera ett Azure Container Service DC/OS-kluster via webbgränssnittet för Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar.

Även om ramverk är tillgängliga för många populära arbetsbelastningar beskriver det här dokumentet hur du kommer igång med att distribuera behållare med Marathon. 


## <a name="prerequisites"></a>Förutsättningar
Innan du börjar med de här exemplen behöver du ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](../container-service-connect.md)

> [!NOTE]
> Den här artikeln förutsätter att du använder tunneltrafik i DC/OS-klustret via en lokal port 80.
>

## <a name="explore-the-dcos-ui"></a>Utforska gränssnittet för DC/OS
Med en Secure Shell (SSH)-tunnel [upprättats](../container-service-connect.md), bläddra till http: \/ /localhost /. Då läses webbgränssnittet för DC/OS in och du kan se information om klustret, till exempel använda resurser, aktiva agenter och tjänster som körs.

![DC/OS-gränssnitt:](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Utforska Marathon-gränssnittet
Om du vill se Användargränssnittet för Marathon, bläddra till http: \/ /localhost/marathon. Från den här skärmbilden kan du starta en ny behållare eller ett annat program på DC/OS-klustret för Azure Container Service. Du kan även se information om att köra containrar och program.  

![Gränssnittet i Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Distribuera en Docker-formaterad container
Om du vill distribuera en ny container med hjälp av Marathon klickar du på **Skapa program** och anger följande information på flikarna i formuläret:

| Fält | Värde |
| --- | --- |
| ID |nginx |
| Minne | 32 |
| Bild |nginx |
| Nätverk |Bryggad |
| Värdport |80 |
| Protokoll |TCP |

![Nytt programgränssnitt – allmänt](./media/container-service-mesos-marathon-ui/dcos4.png)

![Nytt programgränssnitt – Dockercontainer](./media/container-service-mesos-marathon-ui/dcos5.png)

![Nytt programgränssnitt – portar och identifiering av tjänst](./media/container-service-mesos-marathon-ui/dcos6.png)

Om du vill mappa containerporten statiskt till en port på agenten måste du använda JSON-läget. För att göra det växlar du guiden för nya program till **JSON-läge** med hjälp av växlingsknappen. Ange sedan följande inställning under avsnittet `portMappings` i programdefinitionen. Det här exemplet binder containerns port 80 till port 80 på DC/OS-agenten. När du har gjort den här ändringen kan du växla ur guiden ur JSON-läget.

```none
"hostPort": 80,
```

![Nytt programgränssnitt – port 80-exempel](./media/container-service-mesos-marathon-ui/dcos13.png)

Om du vill aktivera hälsokontroller anger du en sökväg på fliken **Hälsokontroller**.

![Nytt programanvändargränssnitt – hälsokontroller](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

DC/OS-klustret distribueras med privata och offentliga agenter. För att klustret ska kunna komma åt program från Internet måste du distribuera programmen till en offentlig agent. För att göra det väljer du fliken **Valfritt** i guiden Nytt program och anger **slave_public** för **Accepterade resursroller**.

Klicka på **Skapa program**.

![Nytt programgränssnitt – inställning av offentlig agent](./media/container-service-mesos-marathon-ui/dcos14.png)

Tillbaka på huvudsidan för Marathon kan du se distributionsstatusen för containern. Inledningsvis visas statusen **Distribuerar**. När distributionen är klar ändras statusen till **Kör**.

![Marathon-huvudsidans gränssnitt 0 behållarens distributionsstatus](./media/container-service-mesos-marathon-ui/dcos7.png)

När du växlar tillbaka till DC/OS-webbgränssnitt (http:\//localhost/), visas att en aktivitet (i det här fallet en Docker-formaterad behållare) körs på DC/OS-klustret.

![DC/OS-webbgränssnitt – aktivitet som körs på klustret](./media/container-service-mesos-marathon-ui/dcos8.png)

Du kan visa klusternoden som uppgiften körs på genom att klicka på fliken **Noder**.

![DC/OS-webbgränssnitt – klusternod](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Nå ut till behållaren

Programmet körs i det här exemplet på en offentlig agent-nod. Du kan komma åt programmet från internet genom att bläddra till agenten FQDN för klustret: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, där:

* **DNSPREFIX** är det DNS-prefix som du angav när du distribuerade klustret.
* **REGION** är den region där resursgruppen finns.

    ![Nginx från Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Nästa steg
* [Arbeta med API för DC/OS och Marathon API](container-service-mesos-marathon-rest.md)

* Ingående om Azure Container Service med Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
