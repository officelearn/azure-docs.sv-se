---
title: Aktivera SSL på ett kluster i Azure Machine Learning Compute (MLC) | Microsoft Docs
description: Instruktioner för hur du konfigurerar SSL för poängsättningsanrop på ett kluster i Azure Machine Learning Compute (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 2a7733468ec082c8954f623f3ebe2cea1fbad561
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976245"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Aktivera SSL på ett kluster i Azure Machine Learning Compute (MLC) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


De här instruktionerna kan du konfigurera SSL för poängsättningsanrop på en Machine Learning Compute (MLC)-kluster. 

## <a name="prerequisites"></a>Förutsättningar 

1. Besluta om en CNAME-post (DNS-namn) ska användas när i realtid bedömnings anrop.

2. Skapa en *lösenord är kostnadsfria* certifikatet med den CNAME.

3. Om certifikatet inte redan finns i PEM-format, konvertera den till PEM med verktyg som *openssl*.

När du har slutfört förutsättningarna har du två filer:

* En fil för certifikatet, till exempel `cert.pem`. Kontrollera att filen har fullständig certifikatkedjan.
* En fil för nyckeln, till exempel `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Konfigurera ett SSL-certifikat på ett nytt ACS-kluster

Med hjälp av Azure Machine Learning CLI kör du följande kommando med den `-c` växel för att skapa ett ACS-kluster med ett SSL-certifikat som är ansluten:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Konfigurera ett SSL-certifikat i ett befintligt ACS-kluster

Om du arbetar med ett kluster som har skapats utan SSL, kan du lägga till ett certifikat med hjälp av Azure PowerShell-cmdlets.

Du måste ange den nyckeln och certifikatet i obearbetat PEM-format. Dessa kan läsas in i PowerShell variabler:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Lägga till certifikatet i klustret: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mappa CNAME och IP-adress

Skapa en mappning mellan CNAME-post som du valde i krav och IP-adressen för den i realtid frontend (FE). För att identifiera IP-adressen för FE, kör du kommandot nedan. Utdata visar ett fält med namnet ”publicIpAddress” som innehåller IP-adressen för klientdelen i realtid kluster. Se anvisningarna för din DNS-leverantör för att ställa in en post från den fullständiga domännamn som används i CNAME-post till den offentliga IP-adressen.



## <a name="auto-detection-of-a-certificate"></a>Automatisk identifiering av ett certifikat 

När du skapar ett webbprogram i realtid med den ”`az ml service create realtime`” kommandot Azure Machine Learning upptäcker automatiskt SSL på klustret och konfigurerar en bedömnings-URL med det angivna certifikatet automatiskt. 

Kör följande kommando om du vill se certifikatstatus för. Lägg märke till ”https”-prefixet för bedömning URI: N och CNAME i värdnamnet. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
