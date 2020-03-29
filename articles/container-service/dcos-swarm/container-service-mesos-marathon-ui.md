---
title: (FÖRÅLDRAD) Hantera Azure DC/OS-kluster med Marathon-användargränssnittet
description: Distribuera behållare till en klustertjänst i Azure Container Service med Marathons webbgränssnitt.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277807"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(FÖRÅLDRAD) Hantera ett DC/OS-kluster för Azure-behållartjänsten via webbgränssnittet Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Ovanpå DC/OS finns det ett ramverk som hanterar schemaläggning och körning av beräkningsarbetsbelastningar.

Ramverk är tillgängliga för många populära arbetsbelastningar, men det här dokumentet beskriver hur du kommer igång med att distribuera behållare med Marathon. 


## <a name="prerequisites"></a>Krav
Innan du börjar med de här exemplen behöver du ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](../container-service-connect.md)

> [!NOTE]
> Den här artikeln förutsätter att du tunnel till DC/OS-klustret via din lokala port 80.
>

## <a name="explore-the-dcos-ui"></a>Utforska gränssnittet för DC/OS
Med en Secure Shell (SSH) tunnel\/ [etablerad](../container-service-connect.md), bläddra till http: /localhost/. Då läses webbgränssnittet för DC/OS in och du kan se information om klustret, till exempel använda resurser, aktiva agenter och tjänster som körs.

![DC/OS-gränssnitt:](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Utforska Marathon-gränssnittet
För att se Marathon UI,\/bläddra till http: /localhost/marathon. Från den här skärmbilden kan du starta en ny behållare eller ett annat program på DC/OS-klustret för Azure Container Service. Du kan även se information om att köra containrar och program.  

![Gränssnittet i Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Distribuera en Docker-formaterad container
Om du vill distribuera en ny container med hjälp av Marathon klickar du på **Skapa program** och anger följande information på flikarna i formuläret:

| Field | Värde |
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

![Marathon-huvudsidans gränssnitt 0 containerns distributionsstatus](./media/container-service-mesos-marathon-ui/dcos7.png)

När du växlar tillbaka till DC/OS-webbgränssnittet (http:\//localhost/) ser du att en uppgift (i det här fallet en Docker-formaterad behållare) körs i DC/OS-klustret.

![DC/OS-webbgränssnitt – aktivitet som körs på klustret](./media/container-service-mesos-marathon-ui/dcos8.png)

Du kan visa klusternoden som uppgiften körs på genom att klicka på fliken **Noder**.

![DC/OS-webbgränssnitt – klusternod](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Nå behållaren

I det här exemplet körs programmet på en offentlig agentnod. Du når programmet från Internet genom att surfa till agenten `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`FQDN i klustret: , där:

* **DNSPREFIX** är det DNS-prefix som du angav när du distribuerade klustret.
* **REGION** är den region där resursgruppen finns.

    ![Nginx från Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Nästa steg
* [Arbeta med API för DC/OS och Marathon API](container-service-mesos-marathon-rest.md)

* Ingående om Azure Container Service med Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
