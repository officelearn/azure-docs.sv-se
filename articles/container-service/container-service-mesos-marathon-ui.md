---
title: "Azure Container Service-hantering av behållare via webbgränssnittet | Microsoft Docs"
description: "Distribuera behållare till en klustertjänst i Azure Container Service med Marathons webbgränssnitt."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, behållare, Micro-tjänster, Mesos, Azure"
ms.assetid: d148ed1e-b582-4d51-944f-1ac7ae3c4fd6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae2945b0025da3bc933a1e4a8f10f21fd35cb51c


---
# <a name="container-management-through-the-web-ui"></a>Behållarhantering via webbgränssnittet
DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar.


Även om ramverk är tillgängliga för många populära arbetsbelastningar beskriver det här dokumentet hur du kan skapa och skala distribution i behållare med Marathon. Innan du börjar med de här exemplen måste du ha ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](container-service-connect.md)

## <a name="explore-the-dcos-ui"></a>Utforska gränssnittet för DC/OS
Gå till http://localhost/ med en SSH-tunnel (Secure Shell) upprättad. Då läses webbgränssnittet för DC/OS in och du kan se information om klustret, till exempel använda resurser, aktiva agenter och tjänster som körs.

![DC/OS-gränssnitt:](media/dcos/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Utforska Marathon-gränssnittet
Gå till http://localhost/Marathon om du vill se användargränssnittet för Marathon. Från den här skärmbilden kan du starta en ny behållare eller ett annat program på DC/OS-klustret för Azure Container Service. Du kan även se information om att köra behållare och program.  

![Gränssnittet i Marathon](media/dcos/dcos3.png)

## <a name="deploy-a-dockerformatted-container"></a>Distribuera en Docker-formaterad behållare
För att distribuera en ny behållare med Marathon klickar du på knappen **Skapa program** och anger följande information i formuläret:

| Fält | Värde |
| --- | --- |
| ID |nginx |
| Bild |nginx |
| Nätverk |Bryggad |
| Värdport |80 |
| Protokoll |TCP |

![Nytt programgränssnitt – allmänt](media/dcos/dcos4.png)

![Nytt programgränssnitt – Dockerbehållare](media/dcos/dcos5.png)

![Nytt programgränssnitt – portar och identifiering av tjänst](media/dcos/dcos6.png)

Om du vill mappa behållarporten statiskt till en port på agenten måste du använda JSON-läget. För att göra det växlar du guiden för nya program till **JSON-läge** med hjälp av växlingsknappen. Ange sedan följande under avsnittet `portMappings` i programmets definition. Det här exemplet binder behållarens port 80 till port 80 på DC/OS-agenten. När du har gjort den här ändringen kan du växla ur guiden ur JSON-läget.

```none
"hostPort": 80,
```

![Nytt programgränssnitt – port 80-exempel](media/dcos/dcos13.png)

DC/OS-klustret distribueras med privata och offentliga agenter. För att klustret ska kunna komma åt program från Internet måste du distribuera programmen till en offentlig agent. För att göra det väljer du fliken **Valfritt** i guiden Nytt program och anger **slave_public** för **Accepterade resursroller**.

![Nytt programgränssnitt – inställning av offentlig agent](media/dcos/dcos14.png)

Tillbaka på huvudsidan för Marathon kan du se distributionsstatusen för behållaren.

![Marathon-huvudsidans gränssnitt 0 behållarens distributionsstatus](media/dcos/dcos7.png)

När du växlar tillbaka till webbgränssnittet för DC/OS (http://localhost/) ser du att en aktivitet (i det här fallet en Docker-formaterad behållare) körs på DC/OS-klustret.

![DC/OS-webbgränssnitt – aktivitet som körs på klustret](media/dcos/dcos8.png)

Du kan också se klusternoden som aktiviteten körs på.

![DC/OS-webbgränssnitt – klusternod](media/dcos/dcos9.png)

## <a name="scale-your-containers"></a>Skala behållarna
Du kan använda gränssnittet i Marathon för att skala instansantalet för en behållare. Det gör du genom att gå till **Marathon**-sidan, markera den behållare som du vill skala och klicka på knappen **Skala**. I dialogrutan **Skala program** anger du önskat antal behållarinstanser och väljer sedan **Skala program**.

![Marathon-gränssnitt – dialogrutan Skala program](media/dcos/dcos10.png)

När skalningsåtgärden har slutförts visas flera instanser av samma aktivitet fördelade på DC/OS-agenterna.

![DC/OS-instrumentpanel för webbgränssnitt – aktivitetsfördelning mellan agenter](media/dcos/dcos11.png)

![DC/OS-webbgränssnitt – noder](media/dcos/dcos12.png)

## <a name="next-steps"></a>Nästa steg
* [Arbeta med API för DC/OS och Marathon API](container-service-mesos-marathon-rest.md)

Ingående om Azure Container Service med Mesos

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
> 
> 




<!--HONumber=Nov16_HO2-->


