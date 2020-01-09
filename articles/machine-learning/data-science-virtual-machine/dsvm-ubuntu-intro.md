---
title: 'Snabb start: skapa en Ubuntu-DSVM'
description: Konfigurera och skapa en Data Science Virtual Machine för Linux (Ubuntu) för att göra analyser och maskin inlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 263c12b344e8634c639167aa3e455032f0817e2f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612095"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Snabb start: Konfigurera Data Science Virtual Machine för Linux (Ubuntu)

Kom igång med Ubuntu 18,04 Data Science Virtual Machine.

## <a name="prerequisites"></a>Krav

Om du vill skapa en Ubuntu 18,04-Data Science Virtual Machine måste du ha en Azure-prenumeration. [Prova Azure kostnads fritt](https://azure.com/free).
Observera att de kostnads fria Azure-kontona inte stöder GPU-aktiverade virtuella datorer SKU: er.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Skapa din virtuella dator för datavetenskap för Linux

Här följer stegen för att skapa en instans av Data Science Virtual Machine Ubuntu 18,04:

1. Gå till [Azure Portal](https://portal.azure.com) du kan uppmanas att logga in på ditt Azure-konto om du inte redan är inloggad.
1. Hitta listan med virtuella datorer genom att skriva in "data science Virtual Machine" och välja "Data Science Virtual Machine-Ubuntu 18,04 Preview".

1. I det efterföljande fönstret väljer du **skapa**.

1. Du bör omdirigeras till bladet "skapa en virtuell dator".
   
1. Ange följande information för att konfigurera varje steg i guiden:

    1. **Grunderna**:
    
       * **Prenumeration**: om du har mer än en prenumeration väljer du den som datorn ska skapas i och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
       * **Resurs grupp**: skapa en ny grupp eller Använd en befintlig grupp.
       * **Namn på virtuell dator**: Ange namnet på den virtuella datorn. Så här kommer det att visas i Azure Portal.
       * **Region**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats. Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Bild**: låt standardvärdet vara kvar.
       * **Storlek**: detta bör fyllas i automatiskt med en storlek som passar för allmänna arbets belastningar. Läs mer om [storlekar för virtuella Linux-datorer i Azure](../../virtual-machines/linux/sizes.md).
       * **Autentiseringstyp**: Välj "lösen ord" för snabbare installation. 
         
         > [!NOTE]
         > Om du tänker använda JupyterHub väljer du "Password", eftersom JupyterHub *inte* har kon figurer ATS för att använda offentliga SSH-nycklar.

       * **Användar namn**: Ange administratörens användar namn. Detta är det användar namn som du ska använda för att logga in på den virtuella datorn och behöver inte vara samma som ditt Azure-användarnamn. Använd *inte* versala bokstäver.
         
         > [!NOTE]
         > Om du använder versaler i ditt användar namn kommer JupyterHub inte att fungera och du kommer att stöta på ett internt 500-server fel.

       * **Lösen ord**: Ange det lösen ord som du ska använda för att logga in på den virtuella datorn.    
    
   1. Välj **Granska + skapa**.
   1. **Granska + skapa**
      * Kontrollera att all information som du angett är korrekt. 
      * Välj **Skapa**.
    
    Etableringen tar cirka 5 minuter. Statusen visas i Azure Portal.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Så här kommer du åt Ubuntu-Data Science Virtual Machine

Du kan komma åt Ubuntu-DSVM på ett av tre sätt:

  * SSH för terminal sessioner
  * X2Go för grafiska sessioner
  * JupyterHub och JupyterLab för Jupyter-anteckningsböcker

Du kan också bifoga en Data Science Virtual Machine till Azure Notebooks för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna i den kostnads fria tjänst nivån. Mer information finns i [Hantera och konfigurera Azure Notebooks projekt](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

När den virtuella datorn har skapats, om den har kon figurer ATS med SSH-åtkomst, kan du logga in till den med hjälp av SSH. Använda de autentiseringsuppgifter som du skapade i den **grunderna** avsnittet i steg 3 för text shell-gränssnittet. I Windows kan du ladda ned ett SSH client-verktyg som [SparaTillFil](https://www.putty.org). Om du föredrar ett grafiskt skriv bord (X Window-System) kan du använda begäran om x11-vidarebefordran på SparaTillFil.

> [!NOTE]
> Klienten X2Go presterade bättre än X11 vidarebefordran i testet. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.

### <a name="x2go"></a>X2Go

Den virtuella Linux-datorn är redan etablerad med X2Go-servern och redo att ta emot klient anslutningar. Slutför följande procedur på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Ladda ned och installera klienten för din klientplattform från X2Go [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Anteckna den virtuella datorns offentliga IP-adress, som du hittar i Azure Portal genom att öppna den virtuella dator som du nyss skapade.

   ![Ubuntu dator-IP-adress](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Kör X2Go-klienten. Om fönstret "ny session" inte visas automatiskt går du till session-> ny session.

1. I fönstret resulterande konfiguration anger du följande konfigurations parametrar:
   * **Sessionen fliken**:
     * **Värd**: Ange IP-adressen för din virtuella dator, som du antecknade tidigare.
     * **Inloggning**: Ange användar namnet på den virtuella Linux-datorn.
     * **SSH-porten**: lämnar det på 22, standardvärdet.
     * **Sessionstyp**: ändra värdet till **XFCE**. Den virtuella Linux-datorn har för närvarande endast stöd för XFCE Desktop.
   * **Media-flik**: du kan inaktivera stöd för ljud och skriva ut om du inte behöver använda dem-klienten.
   * **Delade mappar**: Om du vill kataloger från dina klientdatorer som är monterad på Linux-VM, lägger du till de klienten datorn kataloger som du vill dela med den virtuella datorn på den här fliken.

   ![X2go-konfiguration](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Välj **OK**.
1. Klicka på rutan i den högra rutan i fönstret X2Go för att öppna inloggnings skärmen för den virtuella datorn.
1. Ange lösen ordet för den virtuella datorn.
1. Välj **OK**.
1. Du kan behöva ge X2Go-behörighet för att kringgå brand väggen för att kunna slutföra anslutningen.
1. Nu bör du se det grafiska gränssnittet för din Ubuntu-DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

Ubuntu-DSVM kör [JupyterHub](https://github.com/jupyterhub/jupyterhub), en Jupyter-Server. Följ stegen nedan för att ansluta:

   1. Anteckna den offentliga IP-adressen för den virtuella datorn genom att söka efter och välja den virtuella datorn i Azure Portal.
      ![Ubuntu-datorns IP-adress](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. Från din lokala dator öppnar du en webbläsare och navigerar till https:\//Your-VM-IP: 8000, ersätter "Your-VM-IP" med IP-adressen som du antecknade tidigare.
   1. Ange det användar namn och lösen ord som du använde för att skapa den virtuella datorn och logga in. 

      ![Ange Jupyter-inloggning](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Bläddra bland de många exempel antecknings böcker som är tillgängliga.

JupyterLab, är nästa generations Jupyter notebooks och JupyterHub, också tillgängligt. För att få åtkomst till den loggar du in på JupyterHub och bläddrar sedan till URL: en https:\//Your-VM-IP: 8000/User/din-username/Lab och ersätter "ditt-username" med det användar namn som du valde när du konfigurerade den virtuella datorn.

Du kan ange JupyterLab som standard server för bärbara datorer genom att lägga till den här raden till `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Nästa steg

Här är hur du kan fortsätta din inlärning och undersökning:

* [Data vetenskap i data science Virtual Machine för Linux](linux-dsvm-walkthrough.md) -genom gången visar hur du utför flera vanliga data vetenskaps uppgifter med Linux-DSVM som tillhandahålls här. 
* Utforska de olika data vetenskaps verktygen på DSVM genom att prova de verktyg som beskrivs i den här artikeln. Du kan också köra `dsvm-more-info` på gränssnittet i den virtuella datorn för grundläggande introduktion och pekare till mer information om de verktyg som är installerade på den virtuella datorn.  
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](https://aka.ms/tdsp).
* Gå till den [Azure AI-galleriet](https://gallery.azure.ai/) för machine learning och data analytics-exempel som använder Azure AI-tjänster.
* Se lämplig [referens dokumentation](./reference-ubuntu-vm.md) för den här virtuella datorn.
