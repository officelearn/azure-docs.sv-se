<properties
   pageTitle="Azure Container Service-hantering av behållare via webbgränssnittet | Microsoft Azure"
   description="Distribuera behållare till en klustertjänst i Azure Container Service med Marathons webbgränssnitt."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, behållare, Micro-tjänster, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/19/2016"
   ms.author="nepeters"/>


# Behållarhantering via webbgränssnittet

DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar.


Även om ramverk är tillgängliga för många populära arbetsbelastningar beskriver det här dokumentet hur du kan skapa och skala distribution i behållare med Marathon. Innan du börjar med de här exemplen måste du ha ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

- [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
- [Ansluta till ett Azure Container Service-kluster](container-service-connect.md)

## Utforska gränssnittet för DC/OS

Gå till http://localhost/ med en SSH-tunnel (Secure Shell) upprättad. Då läses webbgränssnittet för DC/OS in och du kan se information om klustret, till exempel använda resurser, aktiva agenter och tjänster som körs.

![DC/OS-gränssnitt:](media/dcos/dcos2.png)

## Utforska Marathon-gränssnittet

Gå till http://localhost/Marathon om du vill se användargränssnittet för Marathon. Från den här skärmbilden kan du starta en ny behållare eller ett annat program på DC/OS-klustret för Azure Container Service. Du kan även se information om att köra behållare och program.  

![Gränssnittet i Marathon](media/dcos/dcos3.png)

## Distribuera en Docker-formaterad behållare

För att distribuera en ny behållare med Marathon klickar du på knappen **Skapa program** och anger följande information i formuläret:

Fält           | Värde
----------------|-----------
ID              | nginx
Bild           | nginx
Nätverk         | Bryggad
Värdport       | 80
Protokoll        | TCP

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

## Skala behållarna

Du kan använda gränssnittet i Marathon för att skala instansantalet för en behållare. Det gör du genom att gå till **Marathon**-sidan, markera den behållare som du vill skala och klicka på knappen **Skala**. I dialogrutan **Skala program** anger du önskat antal behållarinstanser och väljer sedan **Skala program**.

![Marathon-gränssnitt – dialogrutan Skala program](media/dcos/dcos10.png)

När skalningsåtgärden har slutförts visas flera instanser av samma aktivitet fördelade på DC/OS-agenterna.

![DC/OS-instrumentpanel för webbgränssnitt – aktivitetsfördelning mellan agenter](media/dcos/dcos11.png)

![DC/OS-webbgränssnitt – noder](media/dcos/dcos12.png)

## Nästa steg

- [Arbeta med API för DC/OS och Marathon API](container-service-mesos-marathon-rest.md)

Ingående om Azure Container Service med Mesos

> [AZURE.VIDEO] azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]



<!--HONumber=Sep16_HO3-->


