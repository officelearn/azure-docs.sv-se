---
title: Azure stacken Snabbstart - skapa VM-portalen
description: Azure Stack Quick Start - skapa en Linux VM som använder portalen
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 2ea07f04d4c566c0add39d75cad3d3a4ed81c6c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Snabbstart: skapa en virtuell dator Linux server med Azure Stack-portalen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan skapa en virtuell Ubuntu Server 16.04 LTS-dator med hjälp av Azure Stack-portalen. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln kan du också stegen för att:

* Ansluta till den virtuella datorn med en fjärransluten klient.
* Installera en NGINX-webbserver.
* Rensa dina resurser.

## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure-stacken marketplace**

   Stacken för Azure marketplace innehåller inte en Linux-avbildning som standard. Innan du kan skapa en virtuell dator Linux server måste du se till att Azure Stack-operatorn innehåller det **Ubuntu Server 16.04 LTS** bilden som du behöver. Operatorn kan använda stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure-stacken](../azure-stack-download-azure-marketplace-item.md) artikel.

* **Åtkomst till en SSH-klient**

   Om du använder Azure Stack Development Kit (ASDK), kanske du inte har åtkomst till en SSH-klient. Om du behöver en klient, finns det flera paket som innehåller en SSH-klienten. PuTTY innehåller till exempel en SSH-klienten och SSH nyckelgenerator (puttygen.exe). Mer information om tillgängliga paket finns i följande artikel i Azure: [så Använd SSH-nycklar med Windows på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Denna Snabbstart använder PuTTY för att skapa SSH-nycklar och att ansluta till den virtuella datorn på Linux. Om du vill hämta och installera PuTTY, gå till [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Du behöver en SSH-nyckel att slutföra alla steg i den här artikeln. Om du har ett befintligt SSH-nyckelpar, kan du hoppa över det här steget.

1. Navigera till installationsmappen för PuTTY (standardsökvägen är ```C:\Program Files\PuTTY```) och kör ```puttygen.exe```.
2. I fönstret PuTTY nyckel Generator kontrollerar den **typ av nyckel att generera** är inställd på **RSA**, och **antal bitar i en genererad nyckel** är inställd på **2048**. När du är klar klickar du på **generera**.

   ![PuTTY-nyckeln Generator konfiguration](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Om du vill generera en nyckel flyttar du pekaren slumpmässigt i fönstret PuTTY nyckel Generator.
4. När nyckelgenerering är klar klickar du på **spara offentlig nyckel** och klicka sedan på **Spara privat nyckel** att spara dina nycklar i filer.

   ![PuTTY-nyckeln Generator resultat](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen till stacken för Azure-portalen beror på vilken Azure Stack-produkt som du ansluter till:

* För Azure Stack Development Kit (ASDK) Gå till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatorn för ett Azure-stacken integrerat system.

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

1. Klicka på **skapar du en resurs** i det övre vänstra hörnet i Azure Stack-portalen.

2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Klicka på **Skapa**.

4. Ange informationen om virtuella datorn. Välj **Offentlig SSH-nyckel** som **Autentiseringstyp**. Klistra in den offentliga SSH-nyckeln som du sparade och klicka sedan på **OK**.

   >[!NOTE]
 Kontrollera att du tar bort alla inledande eller avslutande blanksteg de nyckeln.

   ![Grunderna i panelen – konfigurera en virtuell dator](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Välj **D1_V2** för den virtuella datorn.

   ![Ändra storlek på panelen - Välj en storlek på virtuell dator](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. På den **inställningar** behålla standardinställningarna och klickar på **OK**.

7. På den **sammanfattning** klickar du på **OK** att starta distributionen av virtuella datorer.

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Klicka på **Anslut** på sidan virtuella datorn. Detta visar en SSH-anslutningssträng som du behöver för att ansluta till den virtuella datorn.

   ![Anslut virtuell dator](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Öppna PuTTY.
3. På den **PuTTY-konfiguration** skärm som du vill använda den **kategori** fönster för att rulla uppåt eller nedåt. Rulla ned till **SSH**, expandera **SSH**, och klicka sedan på **Auth**. Klicka på **Bläddra** och välj den fil för privat nyckel som du sparade.

   ![Välj PuTTY privata nyckel](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Rulla uppåt i den **kategori** fönstret och klicka sedan på **Session**.
5. I den **värdnamn (eller IP-adress)** klistra in anslutningssträngen som visas i Azure Stack-portal. I det här exemplet strängen är ```asadmin@192.168.102.34```.

   ![Anslutningssträngen för puTTY-konfiguration](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klicka på **öppna** öppna en session för den virtuella datorn.

   ![Linux-session](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Installera NGINX-webbserver

Använd följande bash-kommandon för att uppdatera paketet källor och installera det senaste NGINX-paketet på den virtuella datorn.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När du har installerat NGINX, Stäng SSH-session och öppna den på översiktssidan för virtuell dator i Azure Stack-portalen.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

En nätverkssäkerhetsgrupp (NSG) säkrar ingående och utgående trafik. När en virtuell dator skapas i Azure Stack-portalen, skapas en regel för inkommande trafik på port 22 för SSH-anslutningar. Eftersom den här virtuella datorn är värd för en webbserver, måste en NSG-regel skapas för att tillåta Internet-trafik på port 80.

1. På den virtuella datorn **översikt** klickar du på namnet på den **resursgruppen**.
2. Välj den **nätverkssäkerhetsgruppen** för den virtuella datorn. Du kan identifiera NSG med kolumnen **Typ**.
3. På den vänstra menyn under **inställningar**, klickar du på **inkommande säkerhetsregler**.
4. Klicka på **Lägg till**.
5. Skriv **http** i fältet **Namn**. Kontrollera att 80 har angetts för **Portintervall** och att **Tillåt** har angetts för **Åtgärd**.
6. Klicka på **OK**

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

Med NGINX installerat, och port 80 som är öppna på den virtuella datorn, kan du komma åt webbservern med hjälp av den virtuella datorns offentliga IP-adressen. (Den offentliga IP-adressen visas på översiktssidan för den virtuella datorn.)

Öppna en webbläsare och gå till ```http://<public IP address>```.

![Välkomstsida för NGINX web server](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Om du vill ta bort den virtuella datorn och dess resurser, välja en resursgrupp på sidan virtuella datorn och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide distribuerade virtuella datorn för grundläggande Linux-server med en webbserver. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).
