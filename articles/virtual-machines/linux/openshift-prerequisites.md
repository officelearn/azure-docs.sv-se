---
title: OpenShift i förutsättningar för Azure | Microsoft Docs
description: Förutsättningar för att distribuera OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: 36271116d697e5ee6c6ed08d5fdc6063a511e820
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984352"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Vanliga krav för distribution av OpenShift i Azure

Den här artikeln beskriver vanliga krav för distribution av OpenShift Origin eller OpenShift Container Platform i Azure.

Installationen av OpenShift använder Ansible-spelböcker. Ansible använder Secure Shell (SSH) för att ansluta till alla klustervärdar Slutför installationsstegen.

När du har initierat SSH-anslutningen till de fjärranslutna värdarna kan ange du inte ett lösenord. Den privata nyckeln kan inte ha ett lösenord som är associerade med det därför eller distributionen misslyckas.

Eftersom de virtuella datorerna (VM) distribuera via Azure Resource Manager-mallar, används samma offentliga nyckel för åtkomst till alla virtuella datorer. Du måste mata in motsvarande privata nyckel i den virtuella datorn som kör alla spelböcker samt. Om du vill göra detta på ett säkert sätt använder du ett Azure key vault för att skicka den privata nyckeln till den virtuella datorn.

Om det finns ett behov av lagringsutrymme för behållare, krävs beständiga volymer. OpenShift stöder Azure virtuella hårddiskar (VHD) för den här funktionen, men Azure måste först konfigureras som molnleverantör. 

I den här modellen OpenShift:

- Skapar en VHD-objekt i ett Azure Storage-konto.
- Monterar den virtuella Hårddisken till en virtuell dator och formatera volymen.
- Monterar volymen till en pod.

Den här konfigurationen ska fungera OpenShift måste du ha behörighet att utföra de föregående aktiviteterna i Azure. Du kan åstadkomma detta med ett huvudnamn för tjänsten. Tjänstens huvudnamn är en security-konto i Azure Active Directory som har beviljats behörighet till resurser.

Tjänstens huvudnamn måste ha åtkomst till lagringskonton och virtuella datorer som ingår i klustret. Om alla OpenShift-klusterresurser distribuerar till en enskild resursgrupp, kan tjänstens huvudnamn beviljas behörigheter till resursgruppen.

Den här guiden beskriver hur du skapar de artefakter som associeras med kraven.

> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud Solution Provider.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure-prenumerationen med den [az-inloggning](/cli/azure/reference-index#az_login) och följer den på skärmen riktningar eller klicka på **prova** att använda Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Du kan använda en dedikerad resursgrupp för att vara värd för nyckelvalvet. Den här gruppen är separat från resursgruppen dit distribuera OpenShift klusterresurserna. 

I följande exempel skapas en resursgrupp med namnet *keyvaultrg* i den *eastus* plats:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa ett nyckelvalv för att lagra SSH-nycklar för klustret med den [az keyvault skapa](/cli/azure/keyvault#az_keyvault_create) kommando. Nyckelvalvets namn måste vara globalt unikt.

I följande exempel skapas ett nyckelvalv med namnet *keyvault* i den *keyvaultrg* resursgrupp:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel krävs för att skydda åtkomsten till OpenShift Origin-klustret. Skapa en SSH-nyckelpar med hjälp av den `ssh-keygen` (på Linux eller macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH-nyckelpar kan inte ha ett lösenord.

Läs mer på SSH-nycklar på Windows, [hur du skapar SSH-nycklar på Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store privat SSH-nyckeln i Azure Key Vault
OpenShift-distributionen använder SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift master. Om du vill aktivera distributionen att på ett säkert sätt hämta SSH-nyckeln, lagra nyckeln i Key Vault med hjälp av följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure genom att använda ett användarnamn och lösenord eller ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som OpenShift. Du kontrollerar och definiera behörigheter om vilka åtgärder som tjänstens huvudnamn kan utföra i Azure. För att förbättra säkerheten utöver bara ange användarnamn och lösenord, kan det här exemplet skapar en grundläggande tjänst huvudnamn.

Skapa ett tjänstobjekt med [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) och matar ut de autentiseringsuppgifter som OpenShift behöver.

I följande exempel skapar en tjänst huvudnamn och tilldelar den deltagarbehörighet till en resursgrupp med namnet myResourceGroup. Om du använder Windows kör ```az group show --name myResourceGroup --query id``` separat och använda utdata för att mata in alternativet--omfång.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Anteckna appId-egenskapen som returneras från kommandot:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Glöm inte att skapa ett säkert lösenord. Följ vägledningen med [regler för lösenord och begränsningar i Azure AD](/azure/active-directory/active-directory-passwords-policy).

Mer information om tjänstens huvudnamn finns i [skapa Azure-tjänstens huvudnamn med Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i följande avsnitt:
> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud Solution Provider.

Distribuera ett OpenShift-kluster:

- [Distribuera OpenShift Origin](./openshift-origin.md)
- [Distribuera OpenShift Container Platform](./openshift-container-platform.md)

