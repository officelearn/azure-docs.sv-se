---
title: Azure Stack Snabbstart – Skapa virtuell dator med Portal
description: Azure Stack-Snabbstart – skapa en Linux VM med portalen
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: ef2d59393902194af3e257ce0fd5bf403b293af1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245431"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>Snabbstart: skapa en Linux-server-dator med Azure Stack-portalen

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan skapa en Ubuntu Server 16.04 LTS-dator med hjälp av Azure Stack-portalen. Följ stegen i den här artikeln för att skapa och använda en virtuell dator. Den här artikeln ger dig också hur du:

* Anslut till den virtuella datorn med en fjärransluten klient.
* Installera en NGINX-webbserver.
* Rensa dina resurser.

> [!NOTE]  
> Skärmbilder i den här artikeln har uppdaterats för att matcha ändringar som införs med Azure Stack-version 1808. 1808 lägger till stöd för användning av *hanterade diskar* förutom ohanterade diskar. Om du använder en tidigare version, kommer vissa bilder för uppgifter som valet av disk skilja sig från vad som visas i den här artikeln.  


## <a name="prerequisites"></a>Förutsättningar

* **En Linux-avbildning i Azure Stack marketplace**

   I Azure Stack marketplace har inte en Linux-avbildning som standard. Kontrollera att du har Azure Stack operatorn innehåller det **Ubuntu Server 16.04 LTS** bild i din marketplace. Operatören kan använda stegen som beskrivs i den [hämta marketplace-objekt från Azure till Azure Stack](../azure-stack-download-azure-marketplace-item.md) artikeln.

* **Åtkomst till en SSH-klient**

   Om du använder Azure Stack Development Kit (ASDK), kanske du inte har åtkomst till en SSH-klient. Om du behöver en klient, finns det flera paket som innehåller en SSH-klient. PuTTY innehåller till exempel en SSH-klient och en SSH-nyckelgenerator (puttygen.exe). Mer information om tillgängliga paket finns i följande Azure artikel: [Så här använder du SSH-nycklar med Windows på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients).

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
   
   ![Välj Linux-servern](media/azure-stack-quick-linux-portal/select.png)
1. Klicka på **Skapa**.

4. Ange informationen om virtuella datorn. Välj **Offentlig SSH-nyckel** som **Autentiseringstyp**. Klistra in den offentliga SSH-nyckeln som du sparade och klicka sedan på **OK**.

   >[!NOTE]
 Kontrollera att du tar bort eventuella inledande eller avslutande blanksteg för de nyckeln.

   ![Grunderna i panelen – konfigurera en virtuell dator](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. Välj **D1** för den virtuella datorn.

   ![Ändra storlek på panelen – Välj en storlek på virtuell dator](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. På den **inställningar** sidan, gör ändringarna av standardinställningarna.
   
    - Från och med Azure Stack-version 1808, kan du konfigurera **Storage** där du kan välja att använda *hanterade diskar*. Före version 1808 kan endast ohanterade diskar användas.    
      ![Konfigurera lagring för hanterade diskar](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
    När dina konfigurationer är klar, Välj **OK** att fortsätta.

7. På den **sammanfattning** klickar du på **OK** att starta distributionen av virtuella datorn.  
   ![Distribuera](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

1. Klicka på **Connect** på sidan virtuell dator. Du hittar den SSH-anslutningssträng som du behöver ansluta till den virtuella datorn. 

2. Öppna PuTTY.

3. PuTTY-konfigurationsskärmen du kommer att använda den **kategori** fönster för att rulla uppåt eller nedåt. Rulla ned till **SSH**, expandera **SSH**, och klicka sedan på **Auth**. Klicka på **Bläddra** och välj filen för privat nyckel som du sparade.
   ![Ansluta virtuell dator](media/azure-stack-quick-linux-portal/putty03.PNG)

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

![Välkomstsidan för NGINX web server](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du inte behöver längre. Om du vill ta bort den virtuella datorn och dess resurser, väljer du resursgruppen på sidan virtuell dator och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har distribuerat du en grundläggande server för Linux VM med en webbserver. Om du vill veta mer om Azure Stack-datorer kan fortsätta att [överväganden för virtuella datorer i Azure Stack](azure-stack-vm-considerations.md).
