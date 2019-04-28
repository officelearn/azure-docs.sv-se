---
title: OpenShift i förutsättningar för Azure | Microsoft Docs
description: Förutsättningar för att distribuera OpenShift i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473893"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Vanliga krav för distribution av OpenShift i Azure

Den här artikeln beskriver vanliga krav för distribution av OpenShift Container Platform eller OKD i Azure.

Installationen av OpenShift använder Ansible-spelböcker. Ansible använder Secure Shell (SSH) för att ansluta till alla klustervärdar Slutför installationsstegen.

När ansible gör SSH-anslutningen till fjärrvärdar, kan inte den ange ett lösenord. Därför den privata nyckeln kan inte ha ett lösenord (lösenfras) som är kopplade till den eller distributionen misslyckas.

Eftersom de virtuella datorerna (VM) distribuera via Azure Resource Manager-mallar, används samma offentliga nyckel för åtkomst till alla virtuella datorer. Motsvarande privata nyckel måste vara på den virtuella datorn som kör alla spelböcker samt. Om du vill utföra den här åtgärden på ett säkert sätt, används ett Azure key vault till att överföra den privata nyckeln på den virtuella datorn.

Om det finns ett behov av lagringsutrymme för behållare, krävs beständiga volymer. OpenShift stöder Azure virtuella hårddiskar (VHD) för beständig volymer, men Azure måste först konfigureras som molnleverantör.

I den här modellen OpenShift:

- Skapar en VHD-objekt i ett Azure storage-konto eller en hanterad disk.
- Monterar den virtuella Hårddisken till en virtuell dator och formateras volymen.
- Monterar volymen till en pod.

Den här konfigurationen ska fungera måste OpenShift behörighet att utföra dessa uppgifter i Azure. Ett huvudnamn för tjänsten används för detta ändamål. Tjänstens huvudnamn är en security-konto i Azure Active Directory som har beviljats behörighet till resurser.

Tjänstens huvudnamn måste ha åtkomst till lagringskonton och virtuella datorer som ingår i klustret. Om alla OpenShift-klusterresurser distribuerar till en enskild resursgrupp, kan tjänstens huvudnamn beviljas behörigheter till resursgruppen.

Den här guiden beskriver hur du skapar de artefakter som associeras med kraven.

> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud-providern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 
Logga in på Azure-prenumerationen med den [az-inloggning](/cli/azure/reference-index) och följer den på skärmen riktningar eller klicka på **prova** att använda Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Du bör använda en dedikerad resursgrupp som värd för nyckelvalvet. Den här gruppen är separat från resursgruppen dit distribuera OpenShift klusterresurserna.

I följande exempel skapas en resursgrupp med namnet *keyvaultrg* i den *eastus* plats:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
Skapa ett nyckelvalv för att lagra SSH-nycklar för klustret med den [az keyvault skapa](/cli/azure/keyvault) kommando. Nyckelvalvets namn måste vara globalt unikt.

I följande exempel skapas ett nyckelvalv med namnet *keyvault* i den *keyvaultrg* resursgrupp:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Skapa en SSH-nyckel 
En SSH-nyckel krävs för att skydda åtkomsten till OpenShift-klustret. Skapa en SSH-nyckelpar med hjälp av den `ssh-keygen` (på Linux eller macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> SSH-nyckelpar kan inte ha ett lösenord / lösenfras.

Läs mer på SSH-nycklar på Windows, [hur du skapar SSH-nycklar på Windows](/azure/virtual-machines/linux/ssh-from-windows). Glöm inte att exportera den privata nyckeln i OpenSSH-format.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store privat SSH-nyckeln i Azure Key Vault
OpenShift-distributionen använder SSH-nyckeln som du skapade för att skydda åtkomsten till OpenShift master. Om du vill aktivera distributionen att på ett säkert sätt hämta SSH-nyckeln, lagra nyckeln i Key Vault med hjälp av följande kommando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 
OpenShift kommunicerar med Azure genom att använda ett användarnamn och lösenord eller ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som OpenShift. Du kontrollerar och definiera behörigheter om vilka åtgärder som tjänstens huvudnamn kan utföra i Azure. Det är bäst att begränsa behörigheterna för tjänstens huvudnamn till specifika resursgrupper i stället för hela prenumerationen.

Skapa ett tjänstobjekt med [az ad sp create-for-rbac](/cli/azure/ad/sp) och matar ut de autentiseringsuppgifter som OpenShift behöver.

I följande exempel skapar en tjänst huvudnamn och tilldelar den deltagarbehörighet till en resursgrupp med namnet openshiftrg.

Först skapa resursgruppen med namnet openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Skapa tjänstens huvudnamn:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Om du använder Windows kör ```az group show --name openshiftrg --query id``` och använda utdata i stället för $scope.

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

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Krav som gäller endast för Resource Manager-mall

Hemligheter måste skapas för den privata SSH-nyckeln (**sshPrivateKey**), Azure AD-klienthemlighet (**aadClientSecret**), OpenShift administratörslösenord (**openshiftPassword** ), Red Hat prenumeration Manager lösenord och aktivering när (**rhsmPasswordOrActivationKey**).  Om anpassade SSL-certifikat används sedan sex ytterligare hemligheter kommer måste dessutom skapas - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**, och **masterkeyfile**.  Parametrarna förklaras i detalj.

Mallen refererar till specifika hemliga namn, så du **måste** används fetstil namnen som anges ovan (skiftlägeskänsligt).

### <a name="custom-certificates"></a>Anpassade certifikat

Som standard distribuerar mallen en OpenShift-kluster som använder självsignerade certifikat för webbkonsolen OpenShift och routningsdomän. Om du vill använda anpassade SSL-certifikat, ange 'routingCertType' till 'custom' och 'masterCertType' till 'custom'.  Du behöver CA-certifikat och nyckel filerna i PEM-format för certifikaten.  Det är möjligt att använda anpassade certifikat för en men inte på den andra.

Du måste lagra filerna i Key Vault-hemligheter.  Använd samma Nyckelvalv som den som används för den privata nyckeln.  I stället för att kräva 6 ytterligare indata för hemliga namn, är mallen hårdkodad att använda specifika hemliga namn för filer för SSL-certifikat.  Store certifikatdata med hjälp av informationen i följande tabell.

| Hemligt namn      | Certifikatfil   |
|------------------|--------------------|
| mastercafile     | huvudfilen för CA: N     |
| mastercertfile   | huvudfilen för certifikat   |
| masterkeyfile    | huvudnyckelfilen    |
| routingcafile    | Routning CA-fil    |
| routingcertfile  | Routning CERT-fil  |
| routingkeyfile   | Routning nyckelfil   |

Skapa hemligheter med Azure CLI. Nedan visas ett exempel.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs i följande avsnitt:
> [!div class="checklist"]
> * Skapa ett nyckelvalv för att hantera SSH-nycklar för OpenShift-klustret.
> * Skapa ett huvudnamn för tjänsten för användning av Azure Cloud Solution Provider.

Distribuera ett OpenShift-kluster:

- [Distribuera OpenShift Container Platform](./openshift-container-platform.md)
- [Distribuera OpenShift Container Platform självhanterade Marketplace-erbjudandet](./openshift-marketplace-self-managed.md)