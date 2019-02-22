---
title: I den här självstudien skapar du en Azure Stack-dator med en mall | Microsoft Docs
description: Beskriver hur du använder ASDK för att skapa en virtuell dator med en fördefinierad mall och en anpassad mall från GitHub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: a663a5c45c542ac4bfa19266c73066f8e41ba5d8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648888"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Självstudier: skapa en virtuell dator med hjälp av en community-mallar

Som en Azure Stack-operatör eller användare, kan du skapa en virtuell dator (VM) med [anpassade GitHub-snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) i stället för att distribuera en mall manuellt från Azure Stack marketplace.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd Azure Stack-snabbstartsmallar
> * Skapa en virtuell dator med en anpassad mall för GitHub
> * Starta minikube och installera ett program

## <a name="azure-stack-quickstart-templates"></a>Azure Stack-snabbstartsmallar

Azure Stack-snabbstartsmallar lagras i GitHub [offentliga Azure Stack-Snabbstartsmallarna på lagringsplatsen](https://github.com/Azure/AzureStack-QuickStart-Templates). Den här lagringsplatsen innehåller mallar för distribution av Azure Resource Manager som har testats med Microsoft Azure Stack Development Kit (ASDK). Du kan använda dem för att göra det enklare för dig att utvärdera Azure Stack och använda ASDK-miljö.

Framöver kommer ha många GitHub användare bidragit till lagringsplatsen, vilket resulterar i en samling med mer än 400 mallar för distribution. Den här databasen är en utmärkt utgångspunkt för att få en bättre förståelse för hur du kan distribuera olika typer av miljöer till Azure Stack.

>[!IMPORTANT]
> Vissa av dessa mallar skapas av medlemmar i communityn, inte av Microsoft. Varje mall är licensierad enligt ett licensavtal av respektive ägare, inte Microsoft. Microsoft ansvarar inte för dessa mallar och undersöker inte mallarnas säkerhet, kompatibilitet eller prestanda. Community-mallar stöds inte supportprogram eller-tjänster och görs tillgängliga ”som är” utan garanti av något slag.

Om du vill bidra med Azure Resource Manager-mallar till GitHub, göra ditt bidrag till den [AzureStack Snabbstartsmallar](https://github.com/Azure/AzureStack-QuickStart-Templates) lagringsplats. Läs mer om den här lagringsplatsen och hur du kan bidra till den i den [readme-filen](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md).

## <a name="create-a-vm-using-a-custom-github-template"></a>Skapa en virtuell dator med en anpassad mall för GitHub

I det här exemplet i självstudien den [101 – vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) snabbstartsmall för Azure Stack används för att distribuera en Ubuntu 16.04-dator på Azure Stack som kör Minikube att hantera ett Kubernetes-kluster.

Minikube är ett verktyg som gör det enkelt att köra Kubernetes lokalt. Minikube körs ett Kubernetes-kluster som ska användas som en nod i en virtuell dator, så att du kan prova att använda Kubernetes eller utveckla den dagliga. Det stöder en enkel, en nod Kubernetes-kluster som körs på en Linux-VM. Minikube är det snabbaste och enklaste sättet att få ett fullt fungerande Kubernetes-kluster som körs. Det gör det möjligt för utvecklare att utveckla och testa sina distributioner för Kubernetes-baserade program på sina lokala datorer. Arkitektoniskt sett likadant kör Minikube VM både Master och agenten nod-komponenter lokalt:

* Huvudnod komponenter, till exempel API-servern, Scheduler, och [etcd Server](https://coreos.com/etcd/) körs i en enda Linux-process som kallas **LocalKube**.
* Komponenter för Agent-noden körs i docker-behållare exakt som de skulle köra på en normal Agent-nod. Ur ett programs synvinkel för distribution finns det ingen skillnad mellan när programmet har distribuerats på en Minikube eller i en vanlig Kubernetes-kluster.

Den här mallen installeras följande komponenter:

* Dator med Ubuntu 16.04 LTS
* [Docker-CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Ubuntu VM-avbildning (Ubuntu Server 16.04 LTS, i det här exemplet) måste redan ha lagts till i Azure Stack marketplace innan du utför de här stegen.

1. Välj **+ skapa en resurs**, sedan **anpassade**, sedan **malldistributionen**.

    ![Skapa en mall](media/azure-stack-create-vm-template/1.PNG)

2. Välj **redigera mallen**.

    ![Redigera en mall](media/azure-stack-create-vm-template/2.PNG)

3. Välj **snabbstartsmall**.

    ![Snabbstartsmall](media/azure-stack-create-vm-template/3.PNG)

4. Välj **101 – vm-linux-minikube** från de tillgängliga mallarna med hjälp av den **Välj en mall** listrutan och klicka sedan på **OK**.

    ![Välj en mall](media/azure-stack-create-vm-template/4.PNG)

5. Om du vill göra ändringar i mallen JSON kan du göra det. Om inte, eller när du är klar, Välj **spara** att Stäng den **redigera mallen** dialogrutan.

    ![Spara mallen](media/azure-stack-create-vm-template/5.PNG)

6. Välj **parametrar**, Fyll i eller ändra tillgängliga fält vid behov och klicka sedan på **OK**. Välj prenumerationen som ska använda, skapa eller välj en befintlig resursgruppens namn och välj sedan **skapa** att initiera malldistributionen.

    ![Parametrar](media/azure-stack-create-vm-template/6.PNG)

7. Välj prenumerationen som ska använda, skapa eller välj en befintlig resursgruppens namn och välj sedan **skapa** att initiera malldistributionen.

    ![Välj prenumeration](media/azure-stack-create-vm-template/7.PNG)

8. Resursgruppsdistribution tar flera minuter att skapa anpassade mall-baserad virtuell dator. Du kan övervaka installationsstatus via meddelanden och från resursgruppens egenskaper.

    ![Distribuera](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > Den virtuella datorn körs när distributionen är klar.

## <a name="start-minikube-and-install-an-application"></a>Starta Minikube och installera ett program

Nu när Linux VM har skapats, kan du logga in på Starta Minikube och installera ett program.

1. När distributionen är klar väljer du **Connect** att visa den offentliga IP-adressen som ska användas för att ansluta till Linux-VM.

    ![Anslut](media/azure-stack-create-vm-template/9.PNG)

2. Från en upphöjd kommandotolk kör **mstsc.exe** att öppna anslutning till fjärrskrivbord och ansluta till Linux VM offentliga IP-adress identifieras i föregående steg. När du uppmanas att logga in på xRDP, använder du de autentiseringsuppgifter du angav när du skapar den virtuella datorn.

    ![Fjärr](media/azure-stack-create-vm-template/10.PNG)

3. Öppna Terminal-emulatorn och ange följande kommandon för att starta Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Kör kommandon](media/azure-stack-create-vm-template/11.PNG)

4. Öppna webbläsaren och gå till adressen för Kubernetes-instrumentpanelen. Grattis, nu har du en fullständigt arbeta Kubernetes-installation med hjälp av Minikube!

    ![Instrumentpanel](media/azure-stack-create-vm-template/12.PNG)

5. Besök sidan officiella Kubernetes-dokumentationen för att distribuera ett exempelprogram, och hoppa över avsnittet ”Skapa Minikube kluster” som du redan har skapat en i ett föregående steg. Hoppa till avsnittet ”Skapa Node.js-programmet” på https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lär dig mer om Azure Stack-snabbstartsmallar
> * Skapa en virtuell dator med en anpassad mall för GitHub
> * Starta minikube och installera ett program
