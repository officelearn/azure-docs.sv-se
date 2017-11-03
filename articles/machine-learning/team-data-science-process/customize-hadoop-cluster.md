---
title: "Anpassa Hadoop-kluster för Team av vetenskapliga data | Microsoft Docs"
description: "Populära Python-moduler göras tillgänglig i anpassade Azure HDInsight Hadoop-kluster."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0c115dca-2565-4e7a-9536-6002af5c786a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
ms.openlocfilehash: 522e33b399f2648427464b439bc4405e9e8097cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-hdinsight-hadoop-clusters-for-the-team-data-science-process"></a>Anpassa Azure HDInsight Hadoop-kluster för Team Data Science Process
Den här artikeln beskrivs hur du anpassar ett HDInsight Hadoop-kluster genom att installera 64-bitars Anaconda (Python 2.7) på varje nod när klustret har tillhandahållits som ett HDInsight-tjänst. Den visar även hur du kommer åt headnode att skicka anpassade jobb till klustret. Den här anpassningen gör många populära Python-moduler, som ingår i Anaconda, enkelt kan användas i användardefinierade funktioner (UDF) som utformats för att bearbeta Hive poster i klustret. Mer information om procedurer som används i det här scenariot finns [så skicka Hive-frågor](move-hive-tables.md#submit).

Följande menyn länkar till avsnitt som beskriver hur du ställer in de olika datavetenskap miljöer som används av den [Team Data vetenskap processen (TDSP)](overview.md).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

## <a name="customize"></a>Anpassa Azure HDInsight Hadoop-kluster
Om du vill skapa en anpassad HDInsight Hadoop-kluster, starta genom att logga in på [ **klassiska Azure-portalen**](https://manage.windowsazure.com/), klickar du på **ny** längst ned vänstra hörnet och välj sedan DATA SERVICES -> HDINSIGHT -> **skapa anpassade** så att den **klusterinformation** fönster. 

![Skapa arbetsyta](./media/customize-hadoop-cluster/customize-cluster-img1.png)

Ange namnet på klustret som ska skapas på konfigurationssidan 1, och Godkänn standardvärdena för de andra fälten. Klicka på pilen för att gå till nästa konfigurationssida. 

![Skapa arbetsyta](./media/customize-hadoop-cluster/customize-cluster-img1.png)

På konfigurationssidan 2 Ange antalet **DATANODER**, Välj den **REGION/VIRTUELLT nätverk**, och Välj storleken på den **HUVUDNOD** och **DATANODEN**. Klicka på pilen för att gå till nästa konfigurationssida.

> [!NOTE]
> Den **REGION/VIRTUELLT nätverk** måste vara samma som regionen som det lagringskonto som ska användas för HDInsight Hadoop-kluster. Annars i fjärde konfigurationssidan för storage-konto visas inte på den nedrullningsbara listan med **KONTONAMN**.
> 
> 

![Skapa arbetsyta](./media/customize-hadoop-cluster/customize-cluster-img3.png)

Ange ett användarnamn och lösenord för HDInsight Hadoop-kluster på konfigurationssidan 3. **Inte** markerar du den *ange Hive/Oozie Metastore*. Klicka på pilen för att gå till nästa konfigurationssida. 

![Skapa arbetsyta](./media/customize-hadoop-cluster/customize-cluster-img4.png)

Ange lagringskontonamnet standardbehållaren för HDInsight Hadoop-kluster på konfigurationssidan 4. Om du väljer *skapa standardbehållaren* i den **STANDARDBEHÅLLAREN** listrutan listar, en behållare med samma namn som klustret kommer att skapas. Klicka på pilen för att gå till den sista konfigurationssidan.

![Skapa arbetsyta](./media/customize-hadoop-cluster/customize-cluster-img5.png)

På sista **skriptåtgärder** konfigurationssidan, klickar du på **lägga till skriptåtgärd** knappen och fyll textfält med följande värden.

* **NAMNET** -valfri sträng som namn på den här skriptåtgärden
* **NODTYPEN** – Välj **alla noder**
* **SKRIPT-URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
  * *publicscripts* är en offentlig behållare i storage-konto 
  * *getgoing* vi använder för att dela filer för PowerShell-skript för att underlätta användarnas arbete i Azure
* **PARAMETRARNA** -(lämna tomt)

Klicka slutligen på kryssmarkeringen för att starta skapandet av anpassade HDInsight Hadoop-kluster. 

![Skapa arbetsyta](./media/customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a>Åtkomst till huvudnod Hadoop-kluster
Innan du kan använda Hadoop-klustrets huvudnod via RDP måste du aktivera fjärråtkomst till Hadoop-kluster i Azure. 

1. Logga in på den [ **klassiska Azure-portalen**](https://manage.windowsazure.com/)väljer **HDInsight** till vänster, Välj Hadoop-kluster från listan över kluster, klicka på den **CONFIGURATION** fliken och klicka sedan på den **aktivera fjärråtkomst** längst ned på sidan.
   
    ![Skapa arbetsyta](./media/customize-hadoop-cluster/enable-remote-access-1.png)
2. I den **Konfigurera fjärrskrivbord** fönstret Ange fälten användarnamn och lösenord och ange ett sista giltighetsdatum för fjärråtkomst. Klicka sedan på kryssmarkeringen för att aktivera fjärråtkomst till huvudnod i Hadoop-kluster.
   
    ![Skapa arbetsyta](./media/customize-hadoop-cluster/enable-remote-access-2.png)

> [!NOTE]
> Användarnamn och lösenord för fjärråtkomst är inte användarnamn och lösenord som du använder när du skapade det Hadoop-klustret. Det här är en separat uppsättning autentiseringsuppgifter. Dessutom måste utgångsdatum för fjärråtkomst vara inom 7 dagar från det aktuella datumet.
> 
> 

När fjärråtkomst har aktiverats, klickar du på **Anslut** längst ned på sidan för att fjärråtkomst till huvudnoden. Du loggar in på huvudnod Hadoop-kluster genom att ange autentiseringsuppgifterna för användaren för fjärråtkomst som du angav tidigare.

![Skapa arbetsyta](./media/customize-hadoop-cluster/enable-remote-access-3.png)

Nästa steg i processen för avancerade analyser mappas i den [Team Data vetenskap processen (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) och kan innehålla steg som flytta data till HDInsight, och sedan bearbeta och exempel på den där inför learning från data med Azure Machine Learning.

Se [så skicka Hive-frågor](move-hive-tables.md#submit) instruktioner om hur du kommer åt Python-moduler som ingår i Anaconda från huvudnod i klustret i användardefinierade funktioner (UDF) som används för att bearbeta Hive-poster som lagras i klustret.

