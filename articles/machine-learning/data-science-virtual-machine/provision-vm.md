---
title: 'Snabbstart: Skapa en virtuell dator för Windows Data Science'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en virtuell datavetenskapdator på Azure för analys och maskininlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: afcb676f68e7be9d3ebef11ea2c6876a86bbd062
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281790"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Snabbstart: Konfigurera virtuell dator för datavetenskap för Windows

Kom igång med en virtuell dator för datavetenskap i Windows Server 2019.

## <a name="prerequisite"></a>Krav

Om du vill skapa en virtuell dator för Windows Data Science måste du ha en Azure-prenumeration. [Prova Azure gratis](https://azure.com/free).
Observera att kostnadsfria Azure-konton inte stöder GPU-aktiverade SKU:er för virtuella datorer.

## <a name="create-your-dsvm"></a>Skapa din DSVM

Så här skapar du en DSVM-instans:

1. Gå till [Azure-portalen](https://portal.azure.com) Du kan bli ombedd att logga in på ditt Azure-konto om du inte redan är inloggad.
1. Hitta den virtuella datorn genom att skriva in "virtuell dator för datavetenskap" och välja "Data Science Virtual Machine - Windows 2019".

1. Välj knappen **Skapa** längst ned.

1. Du bör omdirigeras till bladet "Skapa en virtuell dator".

1. Fyll i fliken **Grunderna:**
      * **Prenumeration**: Om du har mer än en prenumeration väljer du den prenumeration på vilken datorn ska skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
      * **Resursgrupp**: Skapa en ny grupp eller använd en befintlig.
      * **Namn på virtuell dator:** Ange namnet på den virtuella datorn. Så här visas den i din Azure-portal.
      * **Plats**: Välj det datacenter som är mest lämpligt. För snabbast nätverksåtkomst är det datacentret som har de flesta av dina data eller som ligger närmast din fysiska plats. Läs mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Bild**: Lämna standardvärdet.
      * **Storlek**: Detta bör fyllas automatiskt med en storlek som är lämplig för allmänna arbetsbelastningar. Läs mer om [Windows VM-storlekar i Azure](../../virtual-machines/windows/sizes.md).
      * **Användarnamn**: Ange administratörens användarnamn. Detta är användarnamnet som du använder för att logga in på din virtuella dator och behöver inte vara samma som ditt Azure-användarnamn.
      * **Lösenord:** Ange det lösenord du ska använda för att logga in på din virtuella dator.    
1. Välj **Granska + skapa**.
1. **Granska+skapa**
   * Kontrollera att all information du har angett är korrekt. 
   * Välj **Skapa**.


> [!NOTE]
> * Du betalar inte licensavgifter för programvaran som förinstallerats på den virtuella datorn. Du betalar beräkningskostnaden för den serverstorlek som du valde i **storlekssteget.**
> * Etableringen tar 10 till 20 minuter. Du kan visa status för din virtuella dator på Azure-portalen.

## <a name="access-the-dsvm"></a>Få tillgång till DSVM

När den virtuella datorn har skapats och etablerats följer du stegen i listan för att [ansluta till din Azure-baserade virtuella dator](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Använd de administratörskontouppgifter som du har konfigurerat i steget **Grunderna** för att skapa en virtuell dator. 

Du är redo att börja använda de verktyg som är installerade och konfigurerade på den virtuella datorn. Många av verktygen kan nås via **Start-menyns** paneler och skrivbordsikoner.

Du kan också koppla en DSVM till Azure-anteckningsböcker för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna för den kostnadsfria tjänstnivån. Mer information finns i [Hantera och konfigurera projekt för anteckningsböcker](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Nästa steg

* Utforska verktygen på DSVM genom att öppna **Start-menyn.**
* Lär dig mer om Azure Machine Learning genom att [tutorials](../index.yml)läsa [Vad är Azure Machine Learning?](../overview-what-is-azure-ml.md)
* Läs artikeln [Tio saker du kan göra på Data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Besök [Azure AI Gallery](https://gallery.cortanaintelligence.com) för exempel på maskininlärning och dataanalys som använder Azure Machine Learning och relaterade datatjänster på Azure. Vi har också angett en ikon för det här galleriet på **Start-menyn** och på skrivbordet på den virtuella datorn.

