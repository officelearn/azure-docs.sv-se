---
title: Azure stacken Snabbstart - skapa VM-portalen
description: "Azure Stack Quick Start - skapa en Linux VM som använder portalen"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/11/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1e1732f48de9f95e669d0282d120e48b5fe5f0ef
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Skapa en virtuell Linux-dator med Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack virtuella datorer kan skapas på stacken Azure-portalen. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera en virtuell dator och alla relaterade resurser. Den här snabbstarten visar hur du snabbt skapa en virtuell Linux-dator och installera en webbserver på den.

## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure-stacken marketplace**

   Stacken för Azure marketplace innehåller inte en Linux-avbildning som standard. Innan du kan skapa en virtuell Linux-dator, så kontrollera att Azure Stack-operator har hämtat den **Ubuntu Server 16.04 LTS** avbildningen med hjälp av stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure Stacken](../azure-stack-download-azure-marketplace-item.md) avsnittet.

* **Åtkomst till en SSH-klient**

   Om du använder Azure Stack Development Kit (ASDK), kan du inte har åtkomst till en SSH-klient i din miljö. Om så är fallet kan du välja bland flera paket som innehåller en SSH-klienten. Du kan till exempel installera PuTTY som innehåller en SSH-klienten och SSH nyckelgenerator (puttygen.exe). Mer information om möjliga alternativ finns i följande relaterade Azure-artikel: [så Använd SSH-nycklar med Windows på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Denna Snabbstart använder PuTTY för att generera nycklar för SSH och att ansluta till den virtuella Linux-datorn. Om du vill hämta och installera PuTTY, gå till [http://www.putty.org/](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Du behöver en SSH-nyckel att slutföra denna Snabbstart. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget.

1. Navigera till installationsmappen för PuTTY (standardsökvägen är ```C:\Program Files\PuTTY```) och kör ```puttygen.exe```.
2. I fönstret PuTTY nyckel Generator kontrollerar den **typ av nyckel att generera** är inställd på **RSA**, och **antal bitar i en genererad nyckel** är inställd på **2048**. När du är klar klickar du på **generera**.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Flytta markören i fönstret PuTTY nyckel generatorn för att slutföra processen för nyckelgenerering.
4. När nyckelgenerering är klar klickar du på **spara offentlig nyckel** och **Spara privat nyckel** att spara din offentliga och privata nycklar i filer.

   ![PuTTY-nycklar](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen till stacken för Azure-portalen beror på vilken Azure Stack-produkt som du ansluter till:

* För Azure Stack Development Kit (ASDK) Gå till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatorn för ett Azure-stacken integrerat system.

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

1. Klicka på **skapar du en resurs** i det övre vänstra hörnet i Azure Stack-portalen.

2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Klicka på **Skapa**.

4. Ange informationen om virtuella datorn. Välj **Offentlig SSH-nyckel** som **Autentiseringstyp**. Klistra in i din offentliga SSH-nyckel (som du sparade i en fil tidigare) var noga med att ta bort alla inledande eller avslutande blanksteg. När du är klar klickar du på **OK**.

   ![Grunderna i virtuell dator](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Välj **D1_V2** för den virtuella datorn.

   ![Storleken på datorn](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. På den **inställningar** behålla standardinställningarna och klickar på **OK**.

7. På den **sammanfattning** klickar du på **OK** att starta distributionen av virtuella datorer.


## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Klicka på **Anslut** på sidan virtuella datorn. Detta visar en SSH-anslutningssträng som kan användas för att ansluta till den virtuella datorn.

   ![Anslut virtuell dator](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Öppna PuTTY.
3. På den **PuTTY-konfiguration** skärmen under **kategori**, expandera **SSH** och klicka sedan på **Auth**. Klicka på **Bläddra** och välj den privata nyckeln fil som du sparade tidigare.

   ![PuTTY privata nyckel](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. Under **kategori**rulla uppåt och klicka på **Session**.
5. I den **värdnamn (eller IP-adress)** klistra in anslutningssträngen från stacken Azure-portalen som du såg tidigare. I det här exemplet strängen är ```asadmin@192.168.102.34```.
 
   ![PuTTY-session](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. Klicka på **öppna** öppna en session till den virtuella datorn.

   ![Linus-session](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>Installera NGINX

Använd följande bash-skript om du vill uppdatera paketet källor och installera det senaste NGINX-paketet på den virtuella datorn. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När du är klar avslutar SSH-session och returnerar översiktssidan för virtuell dator i Azure Stack-portalen.


## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

En nätverkssäkerhetsgrupp (NSG) säkrar ingående och utgående trafik. När en virtuell dator skapas från stacken Azure-portalen, skapas en regel för inkommande trafik på port 22 för SSH-anslutningar. Eftersom den här virtuella datorn är värd för en webbserver, måste en NSG-regel skapas för port 80.

1. På den virtuella datorn **översikt** klickar du på namnet på den **resursgruppen**.
2. Välj den **nätverkssäkerhetsgruppen** för den virtuella datorn. Du kan identifiera NSG med kolumnen **Typ**. 
3. På den vänstra menyn under **inställningar**, klickar du på **inkommande säkerhetsregler**.
4. Klicka på **Lägg till**.
5. Skriv **http** i fältet **Namn**. Kontrollera att 80 har angetts för **Portintervall** och att **Tillåt** har angetts för **Åtgärd**. 
6. Klicka på **OK**.


## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

Med NGINX installerat, och port 80 som är öppna på den virtuella datorn, kan du nu åt webbservern på den virtuella datorns offentliga IP-adressen. Den offentliga IP-adressen finns på översiktssidan för den virtuella datorn i Azure Stack-portalen.

Öppna en webbläsare och gå till ```http://<public IP address>```.

![NGINX-standardwebbplats](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det, Välj resursgruppen från sidan virtuella datorn och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en enkel virtuell Linux-dator, en grupp nätverkssäkerhetsregeln, och installerat en webbserver i den här snabbstartsguide. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).

