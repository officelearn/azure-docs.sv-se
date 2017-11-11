---
title: OpenShift i Azure krav | Microsoft Docs
description: "Förutsättningar för att distribuera OpenShift i Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: c6758e8e1a9d9595ae8efb0b8c5aba0b81b0dc38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Gemensamma krav för distribution av OpenShift i Azure

Den här artikeln beskrivs vanliga krav för distribution av OpenShift ursprung eller OpenShift behållare plattform i Azure.

Installationen av OpenShift använder Ansible playbooks. Ansible använder SSH (Secure Shell) för att ansluta till alla klustervärdar för att slutföra följande steg.

Du kan inte ange ett lösenord när du initierar SSH-anslutningen till de fjärranslutna värddatorerna. Därför kan den privata nyckeln kan inte ha ett lösenord som är associerade med den eller distributionen misslyckas.

Eftersom de virtuella datorerna (VM) distribuera via Azure Resource Manager-mallar, används samma offentliga nyckel för åtkomst till alla virtuella datorer. Du behöver mata in motsvarande privata nyckel i den virtuella datorn som kör alla playbooks samt. Om du vill göra detta på ett säkert sätt använder du ett Azure key vault för att skicka den privata nyckeln till den virtuella datorn.

Om det behövs en för beständig lagring för behållare, krävs beständiga volymer. OpenShift stöder Azure virtuella hårddiskar (VHD) för den här funktionen, men Azure måste först vara konfigurerad som molnleverantören. 

I den här modellen OpenShift:

- Skapar en VHD-objekt i ett Azure Storage-konto.
- Monterar den virtuella Hårddisken till en virtuell dator och formatera volymen.
- Monterar volymen för att baljor.

För den här konfigurationen ska fungera måste OpenShift behörigheter för att utföra uppgifter i Azure. Du kan åstadkomma det med en tjänstens huvudnamn. Tjänstens huvudnamn är ett säkerhetskonto i Azure Active Directory som har beviljats behörigheter för resurser.

Tjänstens huvudnamn måste ha åtkomst till virtuella datorer som utgör klustret och storage-konton. Om alla OpenShift klusterresurser distribuerar till en enskild resursgrupp, kan huvudnamn för tjänsten ha behörighet till resursgruppen.

Den här guiden beskriver hur du skapar artefakter som är associerade med kraven.

> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud Solution Provider.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure-prenumerationen med den [az inloggningen](/cli/azure/#login) kommando och följa den på skärmen anvisningarna eller klicka på **prova** att använda molnet Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Du kan använda en dedikerad resursgrupp som värd för nyckelvalvet. Den här gruppen är separat från resursgruppen som klusterresurser OpenShift distribuera. 

I följande exempel skapas en resursgrupp med namnet *keyvaultrg* i den *eastus* plats:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa ett nyckelvalv för att lagra SSH-nycklar för klustret med det [az keyvault skapa](/cli/azure/keyvault#create) kommando. Nyckelvalv namn måste vara globalt unika.

I följande exempel skapas ett nyckelvalv med namnet *keyvault* i den *keyvaultrg* resursgrupp:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel krävs för säker åtkomst till klustret OpenShift ursprung. Skapa en SSH-nyckel med hjälp av den `ssh-keygen` (på Linux- eller macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH-nyckel kan inte ha ett lösenord.

Mer information om SSH-nycklar i Windows finns [skapa SSH-nycklar i Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Lagra privata SSH-nyckeln i Azure Key Vault
OpenShift distributionen använder SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift huvudservern. Om du vill aktivera distributionen för att på ett säkert sätt hämta SSH-nyckeln, lagra nyckeln i Nyckelvalvet med hjälp av följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure med ett användarnamn och lösenord eller ett huvudnamn för tjänsten. En Azure-tjänstens huvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och verktyg för automatisering som OpenShift. Du styr och definiera behörigheter för vilka åtgärder tjänstens huvudnamn kan utföra i Azure. För att förbättra säkerheten utöver bara att ange ett användarnamn och lösenord, kan det här exemplet skapar en grundläggande service principal.

Skapa en tjänstens huvudnamn med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) och de autentiseringsuppgifter som OpenShift måste utdata.

I följande exempel skapar en tjänst huvudnamn och tilldelar det deltagarbehörighet till en resursgrupp med namnet myResourceGroup. Om du använder Windows kör ```az group show --name myResourceGroup --query id``` separat och använda utdata för att mata in alternativet--scope.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Anteckna egenskapen appId returnerades från kommandot:
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
 > Se till att skapa ett säkert lösenord. Följ vägledningen med [regler för lösenord och begränsningar i Azure AD](/azure/active-directory/active-directory-passwords-policy).

Mer information om tjänstens huvudnamn finns [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i följande avsnitt:
> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud Solution Provider.

Distribuera ett OpenShift kluster:

- [Distribuera OpenShift ursprung](./openshift-origin.md)
- [Distribuera OpenShift behållare plattform](./openshift-container-platform.md)

