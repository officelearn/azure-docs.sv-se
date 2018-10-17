---
title: Striims Snabbstart med Azure SQL Data Warehouse | Microsoft Docs
description: Kom igång snabbt med Striims och Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 09f3126725b9a2cd6a8277447c0eed4a16838429
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355576"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striims Azure SQL DW Marketplace erbjuder Installation Guide

Den här snabbstarten förutsätter att du redan har en befintlig instans av SQL Data Warehouse.

Sök efter Striims på Azure Marketplace och välj Striims för dataintegrering till SQL Data Warehouse (mellanlagra) alternativ 

![Installera Striims][install]

Konfigurera Striims VM med angivna egenskaper, noterar Striims klusternamnet, lösenord och administratörslösenord

![Konfigurera Striims][configure]

När distribueras, klickar du på <VM Name>- masternode i Azure-portalen, klicka på Anslut och kopiera inloggningen med lokalt konto för virtuell dator 

![Anslut Striims till SQL Data Warehouse][connect]

Hämta sqljdbc42.jar från <https://www.microsoft.com/en-us/download/details.aspx?id=54671> till den lokala datorn. 

Öppna ett kommandoradsfönster och ändra sökvägen till där du laddade ned JDBC JAR-filen. SCP jar-filen till din Striims VM, komma-adress och lösenord från Azure portal

![Kopiera jar-filen till den virtuella datorn][copy-jar]

Öppna en annan kommandoradsfönstret eller Använd en ssh-verktyg till att ssh till Striims kluster

![SSH till klustret][ssh]

Kör följande kommandon för att flytta JDBC jar-filen till Striims lib directory, och starta och stoppa servern.

   1. sudo su
   2. CD/tmp
   3. MV sqljdbc42.jar /opt/striim/lib
   4. systemctl stoppa striims-nod
   5. systemctl stoppa striims-dbms
   6. systemctl start striims-dbms
   7. systemctl striims-startnoden

![Starta Striims-kluster][start-striim]

Nu kan öppna webbläsaren och navigera till <DNS Name>: 9080

![Gå till inloggningssidan][navigate]

Logga in med användarnamnet och lösenordet du ställer in i Azure-portalen och välj din önskade guiden komma igång eller gå till sidan appar att börja att dra och släpp-gränssnitt

![Logga in med autentiseringsuppgifterna för servern][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png