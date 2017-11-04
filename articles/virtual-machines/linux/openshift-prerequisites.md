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
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Gemensamma krav för OpenShift i Azure

När du distribuerar OpenShift i Azure, finns det några vanliga förutsättningar oavsett om du distribuerar OpenShift ursprung eller OpenShift behållare plattform.

Installationen av OpenShift sker via Ansible playbooks. Ansible använder SSH för att ansluta till alla värdar som ska ingå i klustret för att kunna slutföra följande steg.
När SSH-anslutningen initieras till fjärranslutna värddatorer, går det inte att ange en lösenfras. Den privata nyckeln kan inte ha en lösenfras som är associerade med den därför eller distributionen misslyckas.
Eftersom alla virtuella datorer distribueras via Resource Manager-mallar har samma offentliga nyckel används för åtkomst till alla virtuella datorer. Vi behöver mata in motsvarande privata nyckel i den virtuella datorn som kör alla playbooks samt.
Vi använder en Azure Key Vault för att skicka den privata nyckeln till den virtuella datorn för att kunna göra detta på ett säkert sätt.

Om det behövs en för beständig lagring för behållare, behövs beständiga volymer (NV). Dessa PVs behöver säkerhetskopieras av någon form av beständig lagring. OpenShift stöder Azure-diskar (VHD) för den här funktionen, men Azure måste först vara konfigurerad som Molnleverantören. I den här modellen kommer OpenShift

- Skapa en VHD-objekt i ett Azure Storage-konto
- Montera den virtuella Hårddisken till en virtuell dator och formatera volymen
- Montera volymen på baljor

För att detta ska fungera måste OpenShift behörigheter för att utföra uppgifter i Azure. För att uppnå detta krävs ett huvudnamn för tjänsten. Tjänstens huvudnamn (SP) är ett säkerhetskonto i Azure Active Directory som har beviljats behörigheter för resurser.
Tjänstens huvudnamn måste ha åtkomst till virtuella datorer som utgör klustret och Storage-konton. Om alla OpenShift klusterresurser distribueras till en enskild resursgrupp, kan en SP ha behörighet till resursgruppen.

Den här guiden beskriver hur du skapar artefakter som är associerade med kraven.

> [!div class="checklist"]
> * Skapa en KeyVault för att hantera SSH-nycklar för OpenShift klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud-providern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 
Logga in på Azure-prenumerationen med den [az inloggningen](/cli/azure/#login) kommando och följa den på skärmen anvisningarna eller klicka på **prova** att använda molnet Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Det rekommenderas att en dedikerad resursgrupp används som värd för Key Vault - separat från den resursgrupp som klusterresurser OpenShift ska distribueras till. 

I följande exempel skapas en resursgrupp med namnet *keyvaultrg* i den *eastus* plats.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa en KeyVault för att lagra SSH-nycklar för klustret med det [az keyvault skapa](/cli/azure/keyvault#create) kommando. Key Vault-namnet måste vara globalt unika.

I följande exempel skapas en keyvault med namnet *keyvault* i den *keyvaultrg* resursgruppen.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel krävs för säker åtkomst till klustret OpenShift ursprung. Skapa en SSH-nyckel med hjälp av den `ssh-keygen` (på Linux- eller Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH-nyckel som du skapar får inte ha en lösenfras.

Mer information om SSH-nycklar i Windows, [skapa SSH-nycklar i Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Lagra privata SSH-nyckeln i nyckelvalvet
OpenShift distributionen använder SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift huvudservern. Om du vill aktivera distributionen för att på ett säkert sätt hämta SSH-nyckeln, lagra nyckeln i Nyckelvalvet med följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure med ett användarnamn och lösenord eller ett huvudnamn för tjänsten. En Azure-tjänstens huvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och verktyg för automatisering som OpenShift. Du styr och definiera behörigheter för vilka åtgärder som tjänstens huvudnamn kan utföra i Azure. Om du vill förbättra säkerheten bara att ange ett användarnamn och lösenord, kan det här exemplet skapar en grundläggande service principal.

Skapa en tjänstens huvudnamn med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac) och de autentiseringsuppgifter som OpenShift måste utdata.

I följande exempel skapar en tjänst huvudnamn och tilldelar det deltagarbehörighet till en resursgrupp med namnet myResourceGroup. Om du använder Windows, köra ```az group show --name myResourceGroup --query id``` separat och använda utdata för att mata in alternativet--scope.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Anteckna egenskapen appId returnerades från kommandot.
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
 > Skapa inte ett osäkert lösenord.  Följ vägledningen med [regler för lösenord och begränsningar i Azure AD](/azure/active-directory/active-directory-passwords-policy).

Mer information om tjänstens huvudnamn finns [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i följande avsnitt:
> [!div class="checklist"]
> * Skapa en KeyVault för att hantera SSH-nycklar för OpenShift klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud-providern.

Gå distribuera ett kluster som OpenShift

- [Distribuera OpenShift ursprung](./openshift-origin.md)
- [Distribuera OpenShift behållare plattform](./openshift-container-platform.md)

