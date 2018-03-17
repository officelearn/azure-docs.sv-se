---
title: "I den här självstudiekursen skapar du en Azure-stacken VM som använder en mall | Microsoft Docs"
description: "Beskriver hur du använder ASDK för att skapa en virtuell dator med hjälp av en mall för predfined och en anpassad mall för GitHub."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec44fc879abfe2a457e80f27db972ac4d7570dbd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Självstudier: skapa en virtuell dator med en community-mall
Som Azure Stack operator eller användare, du kan skapa en virtuell dator med hjälp av [anpassade mallar i GitHub-Snabbstart](https://github.com/Azure/AzureStack-QuickStart-Templates) i stället för att distribuera en manuellt från stacken för Azure marketplace.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Lär dig mer om Azure Stack-snabbstartsmallar 
> * Skapa en virtuell dator med en anpassad mall för GitHub
> * Starta minikube och installera ett program

## <a name="learn-about-azure-stack-quickstart-templates"></a>Lär dig mer om Azure Stack-snabbstartsmallar
Azure Stack-snabbstartsmallar lagras i den [offentliga AzureStack Snabbstartsmallar lagringsplats](https://github.com/Azure/AzureStack-QuickStart-Templates) på GitHub. Den här lagringsplatsen innehåller mallar för Azure Resource Manager distribution som har testats med Microsoft Azure Stack Development Kit (ASDK). Du kan använda dem för att göra det enklare för dig att utvärdera Azure Stack och använda ASDK-miljö. 

Med tiden har många GitHub-användare bidragit till databasen, vilket resulterar i en stor samling mallar för distribution av fler än 400. Den här databasen är en utmärkt utgångspunkt för att få en bättre förståelse för hur du kan distribuera olika typer av miljöer till Azure-stacken. 

>[!IMPORTANT]
> Vissa av dessa mallar skapas av medlemmar i gruppen och inte av Microsoft. Varje mall licensieras enligt ett licensavtal av sin ägare, inte Microsoft. Microsoft ansvarar inte för dessa mallar och står inte för säkerhet, kompatibilitet eller prestanda. Community-mallar stöds inte Microsoft support-program eller-tjänster och görs tillgängliga ”som är” utan garanti av något slag.

Om du vill bidra med dina Azure Resource Manager-mallar i GitHub, bör du se dina bidrag till den [azure snabbstartsmallar databasen](https://github.com/Azure/AzureStack-QuickStart-Templates).

Läs mer om GitHub-databas och hur du kan bidra till den i den [Lagringsplatsens Readme-filen](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Skapa en virtuell dator med en anpassad mall för GitHub
I självstudierna exempel den [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Stack Azure quickstart mallen används för att distribuera en virtuell dator i Ubuntu 16.04 på AzureStack kör Minikube att hantera kubenetes kluster.

Minikube är ett verktyg som gör det enkelt att köra Kubernetes lokalt. Minikube körs ett kluster med nod Kubernetes inuti en virtuell dator för användare som vill prova Kubernetes eller utveckla med den dagliga. Det stöder en enkel, en nod Kubernetes kluster som kör på en Linux-VM. Det är det snabbaste och mest direkt framåt sättet att få ett fullt fungerande Kubernetes kluster som kör. Det gör att utvecklare kan utveckla och testa sina program baserat på Kubernetes distributioner på sina lokala datorer. Arkitektoniskt sett likadant körs Minikube VM huvud- och noden agentkomponenter lokalt:
- Huvudnod komponenter som API-servern, Schemaläggaren och etcd servern körs i en enda Linux-process som kallas LocalKube.
- Noden agentkomponenter körs i behållare med docker exakt som de körs på en normal Agent-nod. Ur ett programs synvinkel för distribution finns det ingen skillnad när programmet har distribuerats på en Minikube eller regelbundna Kubernetes kluster.

Den här mallen installeras följande komponenter:

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Ubuntu VM-avbildning (Ubuntu Server 16.04 LTS i det här exemplet) måste redan ha varit [lagts till i Azure-stacken marketplace](asdk-marketplace-item.md) innan du påbörjar de här stegen.

1.  Klicka på **+ ny** > **anpassad** > **malldistribution**.

    ![](media/asdk-create-vm-template/1.PNG) 

2. Klicka på **redigera mallen**.

   ![](media/asdk-create-vm-template/2.PNG) 

3.  Klicka på **Quickstart mallen**.

       ![](media/asdk-create-vm-template/3.PNG)

4. Välj **101-vm-linux-minikube** bland mallarna med hjälp av den **Välj en mall** nedrullningsbara listan och klicka **OK**.  

   ![](media/asdk-create-vm-template/4.PNG)

5. Om du vill göra ändringar i mallens JSON som du kan göra det, om inte, eller när du är klar klickar du på **spara** att stänga dialogrutan Redigera mallen.

   ![](media/asdk-create-vm-template/5.PNG) 

6.  Klicka på **parametrar**, Fyll i eller ändra tillgängliga fält vid behov och klicka sedan på **OK**. Välj prenumerationen att använda, skapa eller välj en befintlig resursgruppens namn och klicka sedan på **skapa** att initiera mallen distribueras.

       ![](media/asdk-create-vm-template/6.PNG)

7. Välj prenumerationen att använda, skapa eller välj en befintlig resursgruppens namn och klicka sedan på **skapa** att initiera mallen distribueras.

   ![](media/asdk-create-vm-template/7.PNG)

8. Resurs gruppdistributionen tar flera minuter att skapa anpassade mall-baserad virtuell dator. Du kan övervaka installationsstatus via meddelanden och från gruppen resursegenskaper. 

   ![](media/asdk-create-vm-template/8.PNG)

   >[!NOTE]
   > Den virtuella datorn körs när distributionen är klar. 

## <a name="start-minikube-and-install-an-application"></a>Starta minikube och installera ett program
Nu när Linux VM har skapats, kan du logga in att starta minikube och installera ett program. 

1. När distributionen är klar klickar du på **Anslut** att visa den offentliga IP-adressen som ska användas för att ansluta till Linux-VM. 

   ![](media/asdk-create-vm-template/9.PNG)

2. Från en upphöjd kommandotolk kör **mstsc.exe** Öppna anslutning till fjärrskrivbord och ansluter till Linux VM offentlig IP-adress som identifierats i föregående steg. När du uppmanas att logga in på xRDP, använder du de autentiseringsuppgifter du angav när du skapar den virtuella datorn.

   ![](media/asdk-create-vm-template/10.PNG)

3. Öppna Terminal emulatorn och ange följande kommandon för att starta minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/asdk-create-vm-template/11.PNG)

4. Öppna webbläsaren och besöker adressen kubernetes instrumentpanelen. Grattis, du har nu en fullt fungerande kubernetes installation med hjälp av minikube!

   ![](media/asdk-create-vm-template/12.PNG)

5. Om du vill distribuera ett exempelprogram Besök sidan officiella dokumentation av kubernetes, hoppa över avsnittet ”Skapa Minikube kluster” som du redan har skapat en ovan. Bara hoppa till avsnittet ”Skapa Node.js-programmet” på https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lär dig mer om Azure Stack-snabbstartsmallar 
> * Skapa en virtuell dator med en anpassad mall för GitHub
> * Starta minikube och installera ett program


> [!div class="nextstepaction"]
> [Lär dig mer om avancerad utvärderingsaktiviteter](asdk-advanced-eval.md)