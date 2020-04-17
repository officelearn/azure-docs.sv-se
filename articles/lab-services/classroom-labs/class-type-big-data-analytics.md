---
title: Konfigurera ett labb för att lära ut stordataanalys med Azure Lab Services | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb för att lära ut stordataanalysen med Docker-distribution av Hortonworks Data Platform (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538783"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Konfigurera ett labb för stordataanalys med Docker-distribution av HortonWorks Data Platform

Den här artikeln visar hur du konfigurerar ett labb för att lära ut en stordataanalysklass.  Med den här typen av klass lär sig eleverna hur de hanterar stora mängder data och använder maskin- och statistikinlärningsalgoritmer för att härleda datainsikter.  Ett viktigt mål för eleverna är att lära sig att använda dataanalysverktyg, till exempel [Apache Hadoops programpaket](https://hadoop.apache.org/) med öppen källkod som tillhandahåller verktyg för lagring, hantering och bearbetning av stordata.

I det här labbet kommer eleverna att använda en populär kommersiell version av Hadoop från [Cloudera](https://www.cloudera.com/), kallad [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  Specifikt kommer eleverna att använda [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) som är en förenklad, lätt att använda version av plattformen som är gratis och avsedd för lärande och experiment.  Även om den här klassen kan använda virtuella Windows- eller Linux-datorer (VM) med HDP Sandbox distribuerad, visar den här artikeln hur du använder Windows.

En annan intressant aspekt av detta labb, är att vi kommer att distribuera HDP Sandbox på labbet virtuella datorer med [Docker](https://www.docker.com/) behållare.  Varje Docker-behållare ger sin egen isolerade miljö för program som ska köras inuti.  Begreppsmässigt är Docker-behållare som kapslade virtuella datorer och kan användas för att enkelt distribuera och köra en mängd olika program baserade på behållaravbildningar som tillhandahålls på [Docker Hub](https://www.docker.com/products/docker-hub).  Clouderas distributionsskript för HDP Sandbox hämtar automatiskt [HDP Sandbox 3.0.1 Docker-avbildningen](https://hub.docker.com/r/hortonworks/sandbox-hdp) från Docker Hub och kör två Docker-behållare:
  - sandlåda-hdp
  - sandlåda-proxy

## <a name="lab-configuration"></a>Labbkonfiguration

För att konfigurera det här labbet behöver du en Azure-prenumeration och labbkonto för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har fått en Azure-prenumeration kan du skapa ett nytt labbkonto i Azure Lab Services. Mer information om hur du skapar ett nytt labbkonto finns i [Självstudiekurs för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labbkonto.

### <a name="lab-account-settings"></a>Inställningar för labbkonto

Aktivera inställningarna som beskrivs i tabellen nedan för labbkontot. Mer information om hur du aktiverar marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labbskapare](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Inställning av labbkonto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-bild| Aktivera Windows 10 Pro-avbildningen för användning i ditt labbkonto.|

### <a name="lab-settings"></a>Labbinställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klassrumslabb.  Mer information om hur du skapar ett klassrumslabb finns i [konfigurera en självstudiekurs för klassrumslabb](tutorial-setup-classroom-lab.md).

| Labbinställningar | Värde/instruktioner |
| ------------ | ------------------ |
|Storlek på virtuell dator| Medium (kapslad virtualisering). Den här vm-storleken passar bäst för relationsdatabaser, cachelagring i minnet och analys.  Den här storleken stöder också kapslad virtualisering.|  
|Bild av virtuell dator| Windows 10 Pro|

> [!NOTE] 
> Vi måste använda Medium (Kapslad virtualisering) eftersom distributionen av HDP Sandbox med Docker kräver:
>   - Windows Hyper-V med kapslad virtualisering
>   - Minst 10 GB RAM

## <a name="template-machine-configuration"></a>Konfiguration av mallmaskin

För att ställa in mallen maskinen kommer vi att:
- Installera Docker
- Distribuera HDP-sandlåda
- Använd PowerShell och Windows Schemaläggaren för att automatiskt starta Docker-behållarna

### <a name="install-docker"></a>Installera Docker

Stegen i det här avsnittet baseras på [Clouderas instruktioner för distribution med Docker-behållare](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Om du vill använda Docker-behållare måste du först installera Docker Desktop på mallen VM:

1. Följ stegen i [avsnittet Förutsättningar för](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) att installera [Docker för Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Kontrollera att konfigurationsalternativet **Använd Windows-behållare i stället för Linux-behållare** lämnas omarkerat.

1. Kontrollera att **Windows Containers och Hyper-V-funktioner** är aktiverade.
   ![Aktivera eller inaktivera Windows-funktioner](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Följ stegen i avsnittet [Minne för Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) för att konfigurera Dockers minneskonfiguration.

    > [!WARNING]
    > Om du av misstag kontrollerar alternativet **Använd Windows-behållare i stället för Linux-behållare** när du installerar Docker visas inte konfigurationsinställningarna för minne.  För att åtgärda detta kan du växla till att använda Linux-behållare genom [att klicka på Docker-ikonen i Windows System-facket](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); När Docker Desktop-menyn öppnas väljer du **Växla till Linux-behållare**.
 
### <a name="deploy-hdp-sandbox"></a>Distribuera HDP-sandlåda

I det här avsnittet distribuerar du HDP Sandbox och sedan även tillgång till HDP Sandbox med hjälp av webbläsaren.

1. Kontrollera att du har installerat [Git Bash](https://gitforwindows.org/) enligt [avsnittet Förutsättningar](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) i guiden eftersom detta rekommenderas för att slutföra nästa steg.

1. Med [Clouderas distributions- och installationsguide för Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)utför du stegen i följande avsnitt:
   
   -    Distribuera HDP-sandlåda
   -    Verifiera HDP-sandlåda

    > [!WARNING] 
    > När du hämtar den senaste ZIP-filen för HDP kontrollerar du att du *inte* sparar ZIP-filen i en katalogsökväg som innehåller blanktecken.

    > [!NOTE] 
    > Om du får ett undantag under distributionen anger **Drive inte har delats,** måste du dela din C-enhet med Docker så att HDP: s Linux-behållare kan komma åt lokala Windows-filer.  För att åtgärda detta [genom att klicka på Docker-ikonen i Windows System-facket](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) för att öppna Menyn Docker Desktop och välj **Inställningar**.  När dialogrutan **Inställningar för Docker** öppnas väljer du **Resurser > Fildelning** och kontrollerar **C-enheten.**  Du kan sedan upprepa stegen för att distribuera HDP Sandbox.

1. När Docker-behållarna för HDP-sandlådan har distribuerats och körs kan du komma åt miljön genom att starta webbläsaren och följa Clouderas instruktioner för att öppna välkomstsidan för [sandlådan](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) och starta HDP Dashboard.

    > [!NOTE] 
    > De här instruktionerna förutsätter att du först har mappat den lokala IP-adressen för sandlådemiljön till sandbox-hdp.hortonworks.com i värdfilen på mallen VM.  Om du **inte** gör den här mappningen kan du komma [http://localhost:8080](http://localhost:8080)åt välkomstsidan för begränsat läge genom att navigera till .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Starta Docker-behållare automatiskt när eleverna loggar in

För att ge en lätt att använda för eleverna använder vi ett PowerShell-skript som automatiskt:
  - Startar HDP Sandbox Docker-behållarna när en elev startar och ansluter till sin virtuella labbdator.
  - Startar webbläsaren och navigerar till välkomstsidan för sandlådan.
Vi använder också Windows Schemaläggaren för att automatiskt köra skriptet när en elev loggar in på sin virtuella dator.
Så här konfigurerar du detta: [Big Data Analytics scripting](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Kostnadsuppskattning

Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel.

För en klass på 25 elever med 20 timmars schemalagd lektionstid och 10 timmars kvot för läxor eller uppgifter, skulle priset för labbet vara:
  - 25 studenter * (20 + 10) timmar * 55 lab enheter * 0,01 USD per timme = 412,50 USD

Mer information om prissättning finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

Den här artikeln gick igenom de steg som krävs för att skapa ett labb för en stordataanalysklass som använder Hortonworks dataplattform som distribueras med Docker.  Inställningen för den här klasstypen kan användas för liknande dataanalysklasser.  Den här inställningen kan också vara tillämplig på andra typer av klasser som använder Docker för distribution.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
