---
title: 'Snabbstart: Skapa en virtuell datavetenskapsdator för Data Science'
titleSuffix: Azure Data Science Virtual Machine
description: Konfigurera och skapa en virtuell datavetenskapdator för Linux (CentOS) för analys och maskininlärning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77526033"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Snabbstart: Konfigurera en virtuell virtuell datavetenskapsdator för CentOS (Linux) i Azure

Kom igång med en Virtuell datavetenskapsdator med CentOS-baserad datavetenskap.

## <a name="prerequisites"></a>Krav

Om du vill skapa en virtuell CentOS-datavetenskapsdator måste du ha en **Azure-prenumeration**. [Skapa en kostnadsfri prenumeration](https://azure.com/free).

## <a name="create-your-centos-data-science-virtual-machine"></a>Skapa din virtuella centos-datavetenskapsdator

Här är stegen för att skapa en instans av Virtual Machine för CentOS Data Science:

1. Gå till [Azure-portalen](https://portal.azure.com). Du kan bli ombedd att logga in på ditt Azure-konto om du inte redan är inloggad. 
1. Skriv "virtuell datavetenskapdator" i sökfältet och välj CentOS DSVM.

    ![CentOS-sökresultat](./media/linux-dsvm-intro/search-centos.png)

1. Välj **Skapa**i det efterföljande fönstret .

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Du bör omdirigeras till bladet "Skapa en virtuell dator".
   
   ![Fliken Grunderna motsvarar CentOS Virtual Machine](./media/linux-dsvm-intro/review-create-centos.png)

1. Ange följande information för att konfigurera varje steg i guiden:

    1. **Grunderna:**
    
       * **Prenumeration**: Om du har mer än en prenumeration väljer du den prenumeration på vilken datorn ska skapas och faktureras. Du måste ha behörighet att skapa resurser för prenumerationen.
       * **Resursgrupp**: Skapa en ny grupp eller använd en befintlig.
       * **Namn på virtuell dator:** Ange namnet på den virtuella datorn. Så här visas den i din Azure-portal.
       * **Region**: Välj det datacenter som är mest lämpligt. För snabbast nätverksåtkomst är det datacentret som har de flesta av dina data eller som ligger närmast din fysiska plats. Läs mer om [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Bild**: Lämna standardvärdet.
       * **Storlek**: Detta bör fyllas automatiskt med en storlek som är lämplig för allmänna arbetsbelastningar. Läs mer om [storlekar på virtuella Linux-datorer i Azure](../../virtual-machines/linux/sizes.md).
       * **Autentiseringstyp**: Välj "Lösenord" för snabbare installation. 
         
         > [!NOTE]
         > Om du tänker använda JupyterHub, se till att välja "Lösenord", som JupyterHub *inte* är konfigurerad för att använda SSH offentliga nycklar.

       * **Användarnamn**: Ange administratörens användarnamn. Detta är användarnamnet som du använder för att logga in på din virtuella dator och behöver inte vara samma som ditt Azure-användarnamn. Använd *inte* bokstäver med versaler.
         
         > [!NOTE]
         > Om du använder bokstäver med versaler i ditt användarnamn kommer JupyterHub inte att fungera, och du kommer att stöta på ett 500 internt serverfel.

       * **Lösenord:** Ange det lösenord du ska använda för att logga in på din virtuella dator.    
    
   1. Välj **Granska + skapa**.
   1. **Granska+skapa**
      * Kontrollera att all information du har angett är korrekt. 
      * Välj **Skapa**.
    
    Etableringen bör ta cirka 5 minuter. Statusen visas i Azure-portalen.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Så här kommer du åt Virtual Machine för CentOS Data Science

Du kan komma åt CentOS DSVM på ett av tre sätt:

  * SSH för terminalsessioner
  * X2Go för grafiska sessioner
  * JupyterHub och JupyterLab för Jupyter Notebook

Du kan också koppla en virtuell datavetenskapdator till Azure-anteckningsböcker för att köra Jupyter-anteckningsböcker på den virtuella datorn och kringgå begränsningarna för den kostnadsfria tjänstnivån. Mer information finns i [Hantera och konfigurera Azure Notebooks-projekt](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

När den virtuella datorn har skapats, om den har konfigurerats med SSH-åtkomst, kan du logga in på den med hjälp av SSH. Använd de kontoautentiseringsuppgifter som du skapade i avsnittet **Grunderna** i steg 3 för textskalgränssnittet. På Windows kan du ladda ner ett SSH-klientverktyg som [PuTTY](https://www.putty.org). Om du föredrar ett grafiskt skrivbord (X Window System) kan du använda X11-vidarebefordran på PuTTY.

> [!NOTE]
> X2Go-klienten presterade bättre än X11-vidarebefordran i testningen. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt skrivbordsgränssnitt.

### <a name="x2go"></a>X2Go (pågående)

Linux-vm är redan etablerat med X2Go Server och redo att acceptera klientanslutningar. Så här ansluter du till det grafiska skrivbordet för Linux VM:

1. Ladda ned och installera X2Go-klienten för din klientplattform från [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Anteckna den virtuella datorns offentliga IP-adress, som du hittar i Azure-portalen genom att öppna den virtuella datorn som du just skapade.

   ![CentOS-maskinens IP-adress](./media/linux-dsvm-intro/centos-ip-address.png)

1. Kör X2Go-klienten. Om fönstret Ny session inte visas automatiskt går du till Session -> Ny session.

1. Ange följande konfigurationsparametrar i det resulterande konfigurationsfönstret:
   * **Fliken Session**:
     * **Värd**: Ange IP-adressen för den virtuella datorn, som du har noterat tidigare.
     * **Logga in**: Ange användarnamnet på Linux VM.
     * **SSH Port:** Lämna den på 22, standardvärdet.
     * **Sessionstyp:** Ändra värdet till **XFCE**. För närvarande stöder Linux-VM endast XFCE-skrivbordet.
   * **Fliken Media**: Du kan stänga av ljudstöd och utskrift av klienter om du inte behöver använda dem.
   * **Delade mappar:** Om du vill ha kataloger från klientdatorerna monterade på Linux-datorn lägger du till klientmaskinskataloger som du vill dela med den virtuella datorn på den här fliken.

   ![X2go-konfiguration](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Välj **OK**.
1. Klicka på rutan i den högra rutan i X2Go-fönstret för att visa inloggningsskärmen för din virtuella dator.
1. Ange lösenordet för den virtuella datorn.
1. Välj **OK**.
1. Du kan behöva ge X2Go behörighet att kringgå brandväggen för att slutföra anslutningen.
1. Du bör nu se det grafiska gränssnittet för din CentOS DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub och JupyterLab

CentOS DSVM kör [JupyterHub](https://github.com/jupyterhub/jupyterhub), en multianvändare Jupyter server. Så här ansluter du:

   1. Anteckna den offentliga IP-adressen för den virtuella datorn genom att söka efter och välja din virtuella dator i Azure-portalen.

       ![CentOS-maskinens IP-adress](./media/linux-dsvm-intro/centos-ip-address.png)

   1. Från din lokala dator öppnar du en\/webbläsare och navigerar till https: /your-vm-ip:8000, som ersätter "your-vm-ip" med den IP-adress du noterade tidigare.
   1. Ange användarnamn och lösenord som du använde för att skapa den virtuella datorn och logga in. 

      ![Ange Jupyter-inloggning](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Bläddra bland de många exempel anteckningsböcker som är tillgängliga.

JupyterLab, nästa generation av Jupyter bärbara datorer och JupyterHub, finns också. För att komma åt den loggar du in på JupyterHub och bläddrar sedan till webbadressen https:\//your-vm-ip:8000/user/your-username/lab och ersätter "ditt användarnamn" med det användarnamn du valde när du konfigurerade den virtuella datorn.

Du kan ställa in JupyterLab som standardserver `/etc/jupyterhub/jupyterhub_config.py`för bärbara datorer genom att lägga till den här raden i:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Nästa steg

Så här kan du fortsätta ditt lärande och utforskande:

* Genomgången Data [science på Data Science Virtual Machine för Linux](linux-dsvm-walkthrough.md) visar hur du gör flera vanliga datavetenskap uppgifter med Linux DSVM som etablerats här. 
* Utforska de olika datascience-verktygen på DSVM genom att prova de verktyg som beskrivs i den här artikeln. Du kan `dsvm-more-info` också köra i skalet i den virtuella datorn för en grundläggande introduktion och för pekare till mer information om de verktyg som är installerade på DSVM.  
* Lär dig hur du bygger analyslösningar från slutna till slutna år med hjälp av [Team Data Science Process](https://aka.ms/tdsp).
* Besök [Azure AI Gallery](https://gallery.azure.ai/) för exempel på maskininlärning och dataanalys som använder Azure AI-tjänsterna.
* Se lämplig [referensdokumentation](./reference-centos-vm.md) för den här virtuella datorn.