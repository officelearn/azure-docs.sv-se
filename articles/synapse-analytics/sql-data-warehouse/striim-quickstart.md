---
title: Snabbstart i Striim
description: Kom igång snabbt med Striim och Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349950"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Installationsguide för Striim Azure SQL DW Marketplace

Den här snabbstarten förutsätter att du redan har en befintlig instans av SQL Data Warehouse.

Sök efter Striim på Azure Marketplace och välj alternativet Striim för dataintegrering till SQL Data Warehouse (Iscensatt) 

![Installera Striim][install]

Konfigurera den virtuella datorn i Striim med angivna egenskaper och notera Striim-klusternamnet, lösenordet och administratörslösenordet

![Konfigurera Striim][configure]

När du har \<distribuerats klickar du på VM-namn>-masternode i Azure-portalen, klickar på Anslut och kopierar inloggningen med det lokala vm-kontot 

![Anslut Striim till SQL Data Warehouse][connect]

Ladda ner sqljdbc42.jar från <https://www.microsoft.com/en-us/download/details.aspx?id=54671> till din lokala dator. 

Öppna ett kommandoradsfönster och ändra kataloger till där du hämtade JDBC-burken. SCP jar-filen till din Striim VM, hämta adress och lösenord från Azure-portalen

![Kopiera jar-filen till den virtuella datorn][copy-jar]

Öppna ett annat kommandoradsfönster eller använd ett ssh-verktyg för att ssh i Striim-klustret

![SSH i klustret][ssh]

Kör följande kommandon för att flytta JDBC-jarfilen till Striims lib-katalog och starta och stoppa servern.

   1. sudo su (sudo su)
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-nod
   5. systemctl stoppa striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-nod

![Starta Striim-klustret][start-striim]

Öppna nu din favoritwebbläsare och navigera till \<DNS-namn>:9080

![Navigera till inloggningsskärmen][navigate]

Logga in med användarnamnet och lösenordet som du konfigurerar i Azure-portalen och välj önskad guide för att komma igång, eller gå till sidan Appar för att börja använda användargränssnittet för att dra och släppa

![Logga in med serverautentiseringsuppgifter][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
