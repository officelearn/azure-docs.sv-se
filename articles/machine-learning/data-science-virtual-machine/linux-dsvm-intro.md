---
title: 'Snabbstart: Skapa en CentOS-Data Science Virtual Machine'
description: Konfigurera och skapa en Data Science Virtual Machine för Linux (CentOS) för att göra analyser och maskin inlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 1761673198b3d8ffc3740d64f525c9e70bda9e3e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204171"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Snabbstart: Konfigurera en CentOS (Linux) Data Science Virtual Machine i Azure

Kom igång med en CentOS-baserad Data Science Virtual Machine.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha en **Azure-prenumeration**för att kunna skapa en CentOS-data science Virtual Machine. [Skapa en kostnads fri prenumeration](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Skapa din CentOS-Data Science Virtual Machine

Här följer stegen för att skapa en instans av CentOS-Data Science Virtual Machine:

1. Gå till [Azure-portalen](https://portal.azure.com). Du kan uppmanas att logga in på ditt Azure-konto om du inte redan har loggat in. 
1. Skriv "data science Virtual Machine" i Sök fältet och välj CentOS-DSVM.

    ![CentOS Sök Resultat](./media/linux-dsvm-intro/search-centos.png)

1. I det efterföljande fönstret väljer du **skapa**.

    [![](media/linux-dsvm-intro/create-centos.png "För att skapa en CentOS-dator")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Du bör omdirigeras till bladet "skapa en virtuell dator".
   
   ![Fliken grunder som motsvarar den virtuella CentOS-datorn](./media/linux-dsvm-intro/review-create-centos.png)

1. Ange följande information för att konfigurera varje steg i guiden:

    1. **Grunderna**:
    
       * **Prenumeration**: Om du har mer än en prenumeration väljer du den dator där datorn ska skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
       * **Resursgrupp**: Skapa en ny grupp eller Använd en befintlig.
       * **Namn på virtuell dator**: Ange namnet på den virtuella datorn. Så här kommer det att visas i Azure Portal.
       * **Region**: Välj det data Center som är lämpligast. För snabbast nätverks åtkomst är det data Center som har de flesta data eller som är närmast din fysiska plats. Lär dig mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Bild**: Låt standardvärdet vara kvar.
       * **Storlek**: Detta ska automatiskt fylla i med en storlek som passar för allmänna arbets belastningar. Läs mer om [storlekar för virtuella Linux-datorer i Azure](../../virtual-machines/linux/sizes.md).
       * **Autentiseringstyp**: Välj "lösen ord" för snabbare installation. 
         
         > [!NOTE]
         > Om du tänker använda JupyterHub väljer du "Password", eftersom JupyterHub *inte* har kon figurer ATS för att använda offentliga SSH-nycklar.

       * **Användarnamn**: Ange administratörens användar namn. Detta är det användar namn som du ska använda för att logga in på den virtuella datorn och behöver inte vara samma som ditt Azure-användarnamn. Använd *inte* versala bokstäver.
         
         > [!NOTE]
         > Om du använder versaler i ditt användar namn kommer JupyterHub inte att fungera och du kommer att stöta på ett internt 500-server fel.

       * **Lösenord**: Ange det lösen ord som du ska använda för att logga in på den virtuella datorn.    
    
   1. Välj **Granska + skapa**.
   1. **Granska + skapa**
      * Kontrollera att all information som du angett är korrekt. 
      * Välj **Skapa**.
    
    Etableringen tar cirka 5 minuter. Statusen visas i Azure Portal.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Så här kommer du åt CentOS-Data Science Virtual Machine

Du kan komma åt CentOS-DSVM på ett av tre sätt:

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

   ![CentOS dator-IP-adress](./media/linux-dsvm-intro/centos-ip-address.png)

1. Kör X2Go-klienten. Om fönstret "ny session" inte visas automatiskt går du till session-> ny session.

1. I fönstret resulterande konfiguration anger du följande konfigurations parametrar:
   * **Sessionen fliken**:
     * **Värd**: Ange IP-adressen för din virtuella dator, som du antecknade tidigare.
     * **Inloggning**: Ange användar namnet på den virtuella Linux-datorn.
     * **SSH-port**: Lämna det på 22 standardvärdet.
     * **Typ av session**: Ändra värdet till **xfce**. Den virtuella Linux-datorn har för närvarande endast stöd för XFCE Desktop.
   * **Fliken Media**: Du kan stänga av ljud supporten och klient utskrift om du inte behöver använda dem.
   * **Delade mappar**: Om du vill att kataloger från klient datorerna ska monteras på den virtuella Linux-datorn lägger du till de klient dator kataloger som du vill dela med den virtuella datorn på den här fliken.

   ![X2go-konfiguration](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Välj **OK**.
1. Klicka på rutan i den högra rutan i fönstret X2Go för att öppna inloggnings skärmen för den virtuella datorn.
1. Ange lösen ordet för den virtuella datorn.
1. Välj **OK**.
1. Du kan behöva ge X2Go-behörighet för att kringgå brand väggen för att kunna slutföra anslutningen.
1. Nu bör du se det grafiska gränssnittet för din CentOS-DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

CentOS-DSVM kör [JupyterHub](https://github.com/jupyterhub/jupyterhub), en Jupyter-Server. Följ stegen nedan för att ansluta:

   1. Anteckna den offentliga IP-adressen för den virtuella datorn genom att söka efter och välja den virtuella datorn i Azure Portal.

       ![CentOS dator-IP-adress](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Från din lokala dator öppnar du en webbläsare och navigerar till https:\//Your-VM-IP: 8000, ersätter "Your-VM-IP" med IP-adressen som du antecknade tidigare.
   1. Ange det användar namn och lösen ord som du använde för att skapa den virtuella datorn och logga in. 

      ![Ange Jupyter-inloggning](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Bläddra bland de många exempel antecknings böcker som är tillgängliga.

JupyterLab, är nästa generations Jupyter notebooks och JupyterHub, också tillgängligt. För att få åtkomst till den loggar du in på JupyterHub och bläddrar sedan till URL\/: en https:/Your-VM-IP: 8000/User/your-username/Lab och ersätter "ditt-username" med det användar namn som du valde när du konfigurerade den virtuella datorn.

Du kan ange JupyterLab som standard server för bärbara datorer genom att lägga till `/etc/jupyterhub/jupyterhub_config.py`den här raden till:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Nästa steg

Här är hur du kan fortsätta din inlärning och undersökning:

* Genom gången [data vetenskap på data science Virtual Machine för Linux](linux-dsvm-walkthrough.md) kan du se hur du gör flera vanliga data vetenskaps uppgifter med Linux-DSVM som tillhandahålls här. 
* Utforska de olika data vetenskaps verktygen på DSVM genom att prova de verktyg som beskrivs i den här artikeln. Du kan också köra `dsvm-more-info` i gränssnittet på den virtuella datorn för en grundläggande introduktion och för att visa mer information om de verktyg som är installerade på DSVM.  
* Lär dig att skapa slutpunkt till slutpunkt Analyslösningar systematiskt med hjälp av den [Team Data Science Process](https://aka.ms/tdsp).
* Gå till den [Azure AI-galleriet](https://gallery.azure.ai/) för machine learning och data analytics-exempel som använder Azure AI-tjänster.
* Se lämplig [referens dokumentation](./reference-centos-vm.md) för den här virtuella datorn.