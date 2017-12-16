---
title: Skydda en webbserver med SSL-certifikat i Azure | Microsoft Docs
description: "Lär dig att skydda NGINX-webbserver med SSL-certifikat på en Linux-VM i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6b333b75f571e367470037ab9ce8b273fcae5498
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Skydda en webbserver med SSL-certifikat på en virtuell Linux-dator i Azure
Ett certifikat senare SSL (Secure Sockets) kan användas för kryptering av webbtrafik säker webbserver. Dessa SSL-certifikat kan lagras i Azure Key Vault och tillåta säker distribution av certifikat till Linux virtuella datorer (VM) i Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera och överföra ett certifikat till Nyckelvalvet
> * Skapa en virtuell dator och installera NGINX-webbserver
> * Mata in certifikatet i den virtuella datorn och konfigurera NGINX med en SSL-bindning

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.22 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Översikt
Azure Key Vault skyddar kryptografiska nycklar och hemligheter, dessa certifikat eller lösenord. Key Vault hjälper till att förenkla hanteringen för certifikatet och gör att du kan behålla kontrollen över nycklar som har åtkomst till dessa certifikat. Du kan skapa ett självsignerat certifikat i Nyckelvalvet eller ladda upp en befintlig, betrodda certifikat som du redan äger.

I stället för att använda en anpassad VM-avbildning som inkluderar certifikat inbyggd-modulen Certifikat Injicera i en aktiv virtuell dator. Den här processen säkerställer att de senaste certifikat installeras på en webbserver under distributionen. Om du vill förnya eller ersätta ett certifikat, behöver du också skapa en ny anpassad VM-avbildning. Senaste certifikaten är automatiskt matas in när du skapar ytterligare virtuella datorer. Under hela processen certifikat aldrig lämna Azure-plattformen eller exponeras i ett skript, kommandoradsverktyget historik eller mall.


## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Innan du kan skapa ett Nyckelvalv och certifikat, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupSecureWeb* i den *eastus* plats:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Skapa sedan ett Nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#create) och aktivera den för användning när du distribuerar en virtuell dator. Varje Key Vault kräver ett unikt namn och bör vara alla gemen. Ersätt * <mykeyvault> * i följande exempel med dina egna unika Key Vault-namn:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generera ett certifikat och lagra i Key Vault
För produktion, ska du importera ett giltigt certifikat som signerats av en betrodd provider med [az keyvault certifikat import](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Den här självstudiekursen i följande exempel visas hur du kan skapa ett självsignerat certifikat med [az keyvault certifikat skapa](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) som använder standardprincipen för certifikat:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Förbereda ett certifikat för användning med en virtuell dator
Använda certifikatet under den virtuella datorn Skapa process, Hämta ID för certifikatet med [az keyvault lista-versionerna](/cli/azure/keyvault/secret#list-versions). Konvertera certifikatet med [az vm format-hemlighet](/cli/azure/vm#format-secret). I följande exempel tilldelas resultatet av dessa kommandon till variabler för att underlätta användning i nästa steg:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Skapa en moln-init-config om du vill skydda NGINX
[Molnet init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en Linux VM när den startas för första gången. Du kan använda molnet init för att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init körs under den ursprungliga startprocessen, det inte finns några ytterligare steg krävs agenter att tillämpa konfigurationen.

När du skapar en virtuell dator, certifikat och nycklar lagras i den skyddade */var/lib/waagent/* directory. Använda molntjänster init för att automatisera lägger till certifikatet till den virtuella datorn och konfigurera webbservern. I det här exemplet, installera och konfigurera webbservern NGINX. Du kan använda samma process för att installera och konfigurera Apache. 

Skapa en fil med namnet *moln-init-webb-server.txt* och klistra in följande konfiguration:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Skapa en säker virtuell dator
Nu skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create). Certifikatdata injekteras från Nyckelvalvet med den `--secrets` parameter. Du skickar i molnet init-config med den `--custom-data` parameter:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Det tar några minuter för den virtuella datorn skapas, paket för att installera och starta appen. När den virtuella datorn har skapats, notera den `publicIpAddress` visas av Azure CLI. Den här adressen används för åtkomst till webbplatsen i en webbläsare.

För att säkra Internet-trafik till den virtuella datorn ska du öppna port 443 från Internet med [az vm öppna port](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testa det säkra webbprogrammet
Nu kan du öppna en webbläsare och ange *https://<publicIpAddress> * i adressfältet. Ange dina egna offentliga IP-adress från den virtuella datorn skapa processen. Acceptera säkerhetsvarningen om du använder ett självsignerat certifikat:

![Acceptera web Säkerhetsvarning för webbläsare](./media/tutorial-secure-web-server/browser-warning.png)

Din NGINX-webbplatsen visas som i följande exempel:

![Visa körs säker NGINX-webbplats](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skyddas en NGINX-webbserver med ett SSL-certifikat som lagras i Azure Key Vault. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera och överföra ett certifikat till Nyckelvalvet
> * Skapa en virtuell dator och installera NGINX-webbserver
> * Mata in certifikatet i den virtuella datorn och konfigurera NGINX med en SSL-bindning

Den här länken om du vill se förskapad virtuella skriptexempel.

> [!div class="nextstepaction"]
> [Windows skriptexempel för virtuell dator](./cli-samples.md)