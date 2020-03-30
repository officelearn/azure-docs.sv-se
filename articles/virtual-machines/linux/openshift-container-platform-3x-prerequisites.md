---
title: OpenShift Container Platform 3.11 i Azure-förutsättningar
description: Förutsättningar för att distribuera OpenShift Container Platform 3.11 i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: b2b34a6fdf96613c5bc372e585598fabbe43d53d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066609"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Vanliga förutsättningar för distribution av OpenShift Container Platform 3.11 i Azure

I den här artikeln beskrivs vanliga förutsättningar för distribution av OpenShift Container Platform eller OKD i Azure.

Installationen av OpenShift använder Ansible-spelböcker. Ansible använder Secure Shell (SSH) för att ansluta till alla klustervärdar för att slutföra installationsstegen.

När ansible gör SSH-anslutningen till fjärrvärdarna kan den inte ange ett lösenord. Därför kan den privata nyckeln inte ha ett lösenord (lösenfras) associerat med det eller distributionen misslyckas.

Eftersom virtuella datorer distribueras via Azure Resource Manager-mallar används samma offentliga nyckel för åtkomst till alla virtuella datorer. Motsvarande privata nyckel måste vara på den virtuella datorn som kör alla spelböcker också. För att utföra den här åtgärden på ett säkert sätt används ett Azure-nyckelvalv för att skicka den privata nyckeln till den virtuella datorn.

Om det finns ett behov av beständig lagring för behållare krävs beständiga volymer. OpenShift stöder virtuella Azure-hårddiskar (VHD) för beständiga volymer, men Azure måste först konfigureras som molnprovider.

I den här modellen, OpenShift:

- Skapar ett VHD-objekt i ett Azure-lagringskonto eller en hanterad disk.
- Monterar den virtuella hårddisken till en virtuell dator och formaterar volymen.
- Monterar volymen på kapseln.

För att den här konfigurationen ska fungera behöver OpenShift behörigheter för att utföra dessa uppgifter i Azure. Ett huvudnamn för tjänsten används för detta ändamål. Tjänsthuvudhuvudet är ett säkerhetskonto i Azure Active Directory som beviljas behörigheter till resurser.

Tjänstens huvudnamn måste ha åtkomst till lagringskonton och virtuella datorer som utgör klustret. Om alla OpenShift-klusterresurser distribueras till en enda resursgrupp kan tjänstens huvudnamn beviljas behörigheter till den resursgruppen.

I den här guiden beskrivs hur du skapar de artefakter som är associerade med förutsättningarna.

> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett tjänsthuvudnamn som kan användas av Azure Cloud Provider.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på din Azure-prenumeration med kommandot [az login](/cli/azure/reference-index) och följ anvisningarna på skärmen, eller klicka på **Prova** för att använda Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Du bör använda en dedikerad resursgrupp som värd för nyckelvalvet. Den här gruppen är skild från den resursgrupp som OpenShift-klusterresurserna distribuerar till.

I följande exempel skapas en resursgrupp med namnet *keyvaultrg* på *eastus-platsen:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa ett nyckelvalv för att lagra SSH-tangenterna för klustret med kommandot [az keyvault create.](/cli/azure/keyvault) Nyckelvalvets namn måste vara globalt unikt och måste aktiveras för malldistribution eller så misslyckas distributionen med felet "KeyVaultParameterReferenceSecretRetrieveFailed".

I följande exempel skapas ett nyckelvalv med namnet *keyvault* i resursgruppen *keyvaultrg:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel behövs för att skydda åtkomsten till OpenShift-klustret. Skapa ett SSH-nyckelpar `ssh-keygen` med kommandot (på Linux eller macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Nyckelparet SSH kan inte ha ett lösenord/lösenfras.

Mer information om SSH-nycklar i Windows finns i [Så här skapar du SSH-nycklar i Windows](/azure/virtual-machines/linux/ssh-from-windows). Var noga med att exportera den privata nyckeln i OpenSSH-format.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Lagra den privata SSH-nyckeln i Azure Key Vault
OpenShift-distributionen använder SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift-hanteraren. Om du vill att distributionen ska kunna hämta SSH-nyckeln på ett säkert sätt lagrar du nyckeln i Key Vault med följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure med hjälp av ett användarnamn och lösenord eller ett tjänsthuvudnamn. Ett Azure-tjänsthuvudnamn är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som OpenShift. Du styr och definierar behörigheterna för vilka åtgärder tjänstens huvudnamn kan utföra i Azure. Det är bäst att begränsa behörigheterna för tjänstens huvudnamn till specifika resursgrupper i stället för hela prenumerationen.

Skapa ett tjänsthuvudnamn med [az ad sp create-for-rbac](/cli/azure/ad/sp) och mata ut de autentiseringsuppgifter som OpenShift behöver.

I följande exempel skapas ett tjänsthuvudnamn och den tilldelas deltagarebehörigheter till en resursgrupp med namnet *openshiftrg*.

Skapa först resursgruppen *openshiftrg:*

```azurecli
az group create -l eastus -n openshiftrg
```

Skapa tjänstens huvudnamn:

```azurecli
az group show --name openshiftrg --query id
```

Spara kommandots utdata och använd i stället för $scope i nästa kommando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Notera egenskapen appId och lösenordet som returneras från kommandot:

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
 > Var noga med att skriva ner det säkra lösenordet eftersom det inte kommer att vara möjligt att hämta det här lösenordet igen.

Mer information om tjänstens huvudnamn finns i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Förutsättningar gäller endast för Resource Manager-mall

Hemligheter måste skapas för SSH privat nyckel **(sshPrivateKey**), Azure AD klient hemlighet **(aadClientSecret),** OpenShift admin lösenord **(openshiftPassword),** och Red Hat Subscription Manager lösenord eller aktiveringsnyckel **(rhsmPasswordOrActivationKey**).  Dessutom, om anpassade SSL-certifikat används, måste ytterligare sex hemligheter skapas - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**och **masterkeyfile**.  Dessa parametrar kommer att förklaras mer i detalj.

Mallen refererar till specifika hemliga namn så att du **måste** använda de fetstilta namnen ovan (skiftlägeskänsliga).

### <a name="custom-certificates"></a>Anpassade certifikat

Som standard distribuerar mallen ett OpenShift-kluster med självsignerade certifikat för OpenShift-webbkonsolen och routningsdomänen. Om du vill använda anpassade SSL-certifikat anger du routingCertType till "custom" och 'masterCertType' till 'custom'.  Du behöver filerna CA, Cert och Key i PEM-format för certifikaten.  Det är möjligt att använda anpassade certifikat för det ena men inte det andra.

Du måste lagra dessa filer i Key Vault hemligheter.  Använd samma Key Vault som det som används för den privata nyckeln.  I stället för att kräva ytterligare 6 indata för de hemliga namnen är mallen hårdkodad för att använda specifika hemliga namn för var och en av SSL-certifikatfilerna.  Lagra certifikatdata med hjälp av informationen från följande tabell.

| Hemligt namn      | Certifikatfil   |
|------------------|--------------------|
| mastercafile (mastercafile)     | huvud-CA-fil     |
| mastercerte   | huvud-CERT-fil   |
| masterkeyfile    | huvudnyckelfil    |
| routingcafile    | routnings-CA-fil    |
| routingcertfile  | routning CERT-fil  |
| routningsnyckelfil   | routning nyckelfil   |

Skapa hemligheterna med Hjälp av Azure CLI. Nedan är ett exempel.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Nästa steg

Denna artikel omfattade följande ämnen:
> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett tjänsthuvudnamn som kan användas av Azure Cloud Solution Provider.

Distribuera sedan ett OpenShift-kluster:

- [Distribuera OpenShift-behållarplattform](./openshift-container-platform-3x.md)
- [Distribuera självhanterad marketplace-erbjudande för OpenShift-behållarplattform](./openshift-container-platform-3x-marketplace-self-managed.md)
