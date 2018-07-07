---
title: Azure Stack Snabbstart – Skapa virtuell dator med Portal
description: Azure Stack-Snabbstart – skapa en Linux VM med portalen
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
ms.openlocfilehash: c364a9ba5a9606967fa86262417ccd1ac3aeffc3
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866278"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Snabbstart: skapa en Linux-server-dator med Azure Stack-portalen

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan skapa en Ubuntu Server 16.04 LTS-dator med hjälp av Azure Stack-portalen. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln ger dig också hur du:

* Anslut till den virtuella datorn med en fjärransluten klient.
* Installera en NGINX-webbserver.
* Rensa dina resurser.

## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure Stack marketplace**

   I Azure Stack marketplace innehåller inte en Linux-avbildning som standard. Innan du kan skapa en Linux-server-dator måste du se till att Azure Stack-operatör kan den **Ubuntu Server 16.04 LTS** avbildning som du behöver. Operatören kan använda stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure Stack](../azure-stack-download-azure-marketplace-item.md) artikeln.

* **Åtkomst till en SSH-klient**

   Om du använder Azure Stack Development Kit (ASDK), kanske du inte har åtkomst till en SSH-klient. Om du behöver en klient, finns det flera paket som innehåller en SSH-klient. PuTTY innehåller till exempel en SSH-klient och en SSH-nyckelgenerator (puttygen.exe). Mer information om tillgängliga paket finns i följande Azure artikel: [så här använder du SSH-nycklar med Windows på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

   Den här snabbstarten använder PuTTY för att generera SSH-nycklar och att ansluta till den virtuella datorn på Linux. Om du vill hämta och installera PuTTY, gå till [ http://www.putty.org/ ](http://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Du behöver ett SSH-nyckelpar att slutföra alla steg i den här artikeln. Om du har ett befintligt SSH-nyckelpar kan du hoppa över det här steget.

1. Navigera till installationsmappen för PuTTY (standardplatsen är ```C:\Program Files\PuTTY```) och kör ```puttygen.exe```.
2. I fönstret PuTTY-Nyckelgenerator, se till att den **typen av nyckel att generera** är inställd på **RSA**, och **antalet bitar i en genererad nyckel** är inställd på **2048**. När du är klar klickar du på **generera**.

   ![PuTTY-Nyckelgenerator konfiguration](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. Om du vill generera en nyckel flyttar du pekaren slumpmässigt i fönstret PuTTY-Nyckelgenerator.
4. När nyckelgenerering är klar klickar du på **spara offentlig nyckel** och klicka sedan på **Spara privat nyckel** att spara dina nycklar i filer.

   ![PuTTY-Nyckelgenerator resultat](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Logga in på Azure Stack-portalen

Logga in på Azure Stack-portalen. Adressen för Azure Stack portal beror på vilken Azure Stack-produkt som du ansluter till:

* För Azure Stack Development Kit (ASDK) går du till: https://portal.local.azurestack.external.
* Gå till den URL som tillhandahålls av Azure Stack-operatör för ett integrerat Azure Stack-system.

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

1. Klicka på **skapa en resurs** i det övre vänstra hörnet i Azure Stack-portalen.

2. Välj **Compute** och välj sedan **Ubuntu Server 16.04 LTS**.
3. Klicka på **Skapa**.

4. Ange informationen om virtuella datorn. Välj **Offentlig SSH-nyckel** som **Autentiseringstyp**. Klistra in den offentliga SSH-nyckeln som du sparade och klicka sedan på **OK**.

   >[!NOTE]
 Kontrollera att du tar bort eventuella inledande eller avslutande blanksteg för de nyckeln.

   ![Grunderna i panelen – konfigurera en virtuell dator](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Välj **D1_V2** för den virtuella datorn.

   ![Ändra storlek på panelen – Välj en storlek på virtuell dator](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. På den **inställningar** , behåller du standardvärdena och klicka på **OK**.

7. På den **sammanfattning** klickar du på **OK** att starta distributionen av virtuella datorn.

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Klicka på **Connect** på sidan virtuell dator. Då visas en SSH-anslutningssträng som du behöver ansluta till den virtuella datorn.

   ![Ansluta virtuell dator](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. Öppna PuTTY.
3. På den **PuTTY-konfiguration** skärmen som du kommer att använda den **kategori** fönster för att rulla uppåt eller nedåt. Rulla ned till **SSH**, expandera **SSH**, och klicka sedan på **Auth**. Klicka på **Bläddra** och välj filen för privat nyckel som du sparade.

   ![Välj PuTTY privata nyckel](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. Rulla uppåt i den **kategori** , och klicka på **Session**.
5. I den **värdnamn (eller IP-adress)** rutan, klistra in anslutningssträngen som visas i Azure Stack-portalen. I det här exemplet strängen är ```asadmin@192.168.102.34```.

   ![PuTTY-konfiguration-anslutningssträng](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. Klicka på **öppna** att öppna en session för den virtuella datorn.

   ![Linux-session](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Installera NGINX-webbservern

Använd följande bash-kommandon för att uppdatera paketkällor och installera det senaste NGINX-paketet på den virtuella datorn.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När du är klar med att installera NGINX Stäng SSH-sessionen och öppna sidan Översikt för virtuell dator i Azure Stack-portalen.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

En nätverkssäkerhetsgrupp (NSG) säkrar ingående och utgående trafik. När en virtuell dator har skapats i Azure Stack-portalen, skapas en regel för inkommande trafik på port 22 för SSH-anslutningar. Eftersom den här virtuella datorn är värd för en webbserver, måste en NSG-regel som ska skapas för att tillåta webbtrafik på port 80.

1. På den virtuella datorn **översikt** klickar du på namnet på den **resursgrupp**.
2. Välj den **nätverkssäkerhetsgrupp** för den virtuella datorn. Du kan identifiera NSG med kolumnen **Typ**.
3. I den vänstra menyn under **inställningar**, klickar du på **ingående säkerhetsregler**.
4. Klicka på **Lägg till**.
5. Skriv **http** i fältet **Namn**. Kontrollera att 80 har angetts för **Portintervall** och att **Tillåt** har angetts för **Åtgärd**.
6. Klicka på **OK**

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

När NGINX är installerat och port 80 är öppen på den virtuella datorn kan du komma åt webbservern med hjälp av den virtuella datorns offentliga IP-adressen. (Den offentliga IP-adressen visas på översiktssidan för den virtuella datorn.)

Öppna en webbläsare och gå till ```http://<public IP address>```.

![Välkomstsidan för NGINX web server](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Om du vill ta bort den virtuella datorn och dess resurser, väljer du resursgruppen på sidan virtuell dator och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du en grundläggande server för Linux VM med en webbserver. Om du vill veta mer om Azure Stack-datorer kan fortsätta att [överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md).
