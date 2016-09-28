<properties
    pageTitle="Skapa en virtuell Linux-dator med hjälp av Azure Portal | Microsoft Azure"
    description="Skapa en virtuell Linux-dator med hjälp av Azure Portal."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/18/2016"
    ms.author="v-livech"
/>


# Skapa en virtuell Linux-dator i Azure med hjälp av portalen


I den här artikeln lär du dig hur du använder [Azure Portal](https://portal.azure.com/) för att snabbt skapa en virtuell Linux-dator. De enda kraven är [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/) och [filer för offentliga och privata SSH-nycklar](virtual-machines-linux-mac-create-ssh-keys.md).


1. När du är inloggad på Azure Portal med din Azure-kontoidentitet klickar du på **+ Nytt** i det övre vänstra hörnet:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Klicka på **Virtual Machines** på **Marketplace** och sedan på **Ubuntu Server 14.04 LTS** i listan **Aktuella appar**.  Kontrollera längst ned att distributionsmodellen är `Resource Manager` och klicka sedan på **Skapa**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. På sidan **Grundläggande inställningar** anger du:
    - ett namn för den virtuella datorn
    - ett användarnamn för administratören
    - autentiseringstypen till **Offentlig SSH-nyckel**
    - din offentliga SSH-nyckel som en sträng (från `~/.ssh/`-katalogen)
    - ett resursgruppsnamn eller väljer en befintlig grupp

    och klickar på **OK** för att fortsätta och välja en storlek för den virtuella datorn. Det bör se ut ungefär så här:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Välj storleken **DS1** som installerar Ubuntu på en Premium SSD och klicka på **Välj** för att konfigurera inställningar.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. I **Inställningar** lämnar du standardinställningarna för Storage- och nätverksvärdena och klickar på **OK** för att visa sammanfattningen.  Observera att disktypen har angetts till Premium SSD eftersom du valde DS1. Bokstaven **S** representerar SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Bekräfta inställningarna för din nya virtuella Ubuntu-dator och klicka på **OK**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Öppna portalens instrumentpanel och välj ditt nätverkskort i **Nätverksgränssnitt**

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Öppna menyn Offentliga IP-adresser under inställningarna för nätverkskortet

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH till den offentliga IP-adressen med hjälp av den offentliga SSH-nyckeln

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Nästa steg

Nu har du snabbt skapat en virtuell Linux-dator för testnings- eller demonstrationsändamål. Om du vill skapa en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar.

- [Skapa en virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-cli-deploy-templates.md)
- [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Skapa en virtuell Linux-dator med hjälp av Azure CLI](virtual-machines-linux-create-cli-complete.md)



<!--HONumber=Sep16_HO3-->


