---
title: 'Snabbstart: Skapa en Windows-DSVM'
description: Konfigurera och skapa en virtuell dator för datavetenskap på Azure för analys och maskininlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: f543db0240d59cf99f3bd793e059b7985f1ffe22
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170658"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Snabbstart: Konfigurera Data Science Virtual Machine för Windows

Kom igång med en Windows-Data Science Virtual Machine.

## <a name="prerequisite"></a>Krav

Om du vill skapa en Windows-Data Science Virtual Machine måste du ha en Azure-prenumeration. [Prova Azure kostnads fritt](https://azure.com/free).

## <a name="create-your-dsvm"></a>Skapa din DSVM

Så här skapar du en DSVM-instans:

1. Gå till [Azure Portal](https://portal.azure.com) du kan uppmanas att logga in på ditt Azure-konto om du inte redan är inloggad.
1. Hitta listan med virtuella datorer genom att skriva in "data science Virtual Machine" och välja "Data Science Virtual Machine-Windows 2016".
    ![Lista över virtuella Windows-datorer](./media/provision-vm/search-windows.png)
1. Välj knappen **skapa** längst ned.

   ![Lista över virtuella datorer på Azure Portal, med knappen Skapa](./media/provision-vm/create-windows.png)

1. Du bör omdirigeras till bladet "skapa en virtuell dator".
   ![Fliken grunder som motsvarar den virtuella Windows-datorn](./media/provision-vm/review-create-windows.png)

1. Fyll i fliken **grundläggande** :
      * **Prenumeration**: Om du har mer än en prenumeration väljer du den dator där datorn ska skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
      * **Resursgrupp**: Skapa en ny grupp eller Använd en befintlig.
      * **Namn på virtuell dator**: Ange namnet på den virtuella datorn. Så här kommer det att visas i Azure Portal.
      * **Plats**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats. Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Bild**: Låt standardvärdet vara kvar.
      * **Storlek**: Detta ska automatiskt fylla i med en storlek som passar för allmänna arbets belastningar. Läs mer om [storlekar för virtuella Windows-datorer i Azure](../../virtual-machines/windows/sizes.md).
      * **Användarnamn**: Ange administratörens användar namn. Detta är det användar namn som du ska använda för att logga in på den virtuella datorn och behöver inte vara samma som ditt Azure-användarnamn.
      * **Lösenord**: Ange det lösen ord som du ska använda för att logga in på den virtuella datorn.    
1. Välj **Granska + skapa**.
1. **Granska + skapa**
   * Kontrollera att all information som du angett är korrekt. 
   * Välj **Skapa**.


> [!NOTE]
> * Du betalar inte licens avgifter för den program vara som är förinstallerad på den virtuella datorn. Du betalar beräknings kostnaden för den server storlek som du valde i steget **storlek** .
> * Etableringen tar 10 till 20 minuter. Du kan visa statusen för den virtuella datorn på Azure Portal.

## <a name="access-the-dsvm"></a>Åtkomst till DSVM

När den virtuella datorn har skapats och allokerats följer du stegen som visas för att [ansluta till din Azure-baserade virtuella dator](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Använd de autentiseringsuppgifter för administratörs kontot som du konfigurerade i **grundläggande** steg för att skapa en virtuell dator. 

Du är redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. Många av verktygen kan nås via **Start** -menyns paneler och skriv bords ikoner.

Du kan också bifoga en DSVM till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera antecknings projekt](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på DSVM genom att öppna **Start** -menyn.
* Lär dig mer om tjänsten Azure Machine Learning genom att läsa [Vad är Azure Machine Learning tjänsten?](../service/overview-what-is-azure-ml.md) och prova [själv studie kurserna](../index.yml).
* I Utforskaren bläddrar du till C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts for samples som använder RevoScaleR-biblioteket i R som stöder data analys i företags skala. 
* Läs artikeln [tio saker du kan göra på data science Virtual Machine](https://aka.ms/dsvmtenthings).
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](../team-data-science-process/index.yml).
* Gå till den [Azure AI-galleriet](https://gallery.cortanaintelligence.com) för machine learning och data analytics-exempel som använder Azure Machine Learning och relaterade data services på Azure. Vi har också tillhandahållit en ikon för det här galleriet på **Start** -menyn och på Skriv bordet på den virtuella datorn.
* Se lämplig [referens dokumentation](./reference-windows-vm.md) för den här virtuella datorn.

