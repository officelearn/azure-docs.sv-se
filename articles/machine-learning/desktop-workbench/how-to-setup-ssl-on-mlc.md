---
title: Aktivera SSL på ett kluster i Azure Machine Learning beräkna (MLC) | Microsoft Docs
description: Instruktioner för hur du konfigurerar SSL för poäng anrop i ett kluster i Azure Machine Learning beräkna (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 81649277c0cb2f4b00ce856be5efcce6121b1be4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Aktivera SSL på ett kluster i Azure Machine Learning beräkna (MLC) 

Dessa instruktioner kan du konfigurera SSL för poäng anrop på ett kluster med Machine Learning beräkna (MLC). 

## <a name="prerequisites"></a>Förutsättningar 

1. Besluta om en CNAME-post (DNS-namn) ska användas när realtid bedömningsprofil-anrop.

2. Skapa en *lösenord utan* certifikat med den CNAME-post.

3. Om certifikatet inte PEM-format, konvertera den till PEM med hjälp av verktyg som *openssl*.

Du har två filer när du har slutfört krav:

* En fil för certifikat, till exempel `cert.pem`. Kontrollera att filen har fullständig certifikatkedjan.
* En fil för nyckeln, t.ex. `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Ställ in ett SSL-certifikat på ett nytt ACS-kluster

Med hjälp av Azure Machine Learning CLI kör du följande kommando med den `-c` växel för att skapa en ACS-kluster med ett SSL-certifikat kopplade:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Ställ in ett SSL-certifikat på ett befintligt ACS-kluster

Om du utvecklar ett kluster som har skapats utan SSL, kan du lägga till ett certifikat med hjälp av Azure PowerShell-cmdlets.

Du måste ange den nyckeln och certifikatet i raw PEM-format. Dessa kan läsas in i PowerShell variabler:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Lägga till certifikatet i klustret: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mappa CNAME och IP-adress

Skapa en mappning mellan CNAME som du valde i krav och IP-adressen för den realtid frontend (FE). För att identifiera IP-adressen för FE, kör du kommandot nedan. Utdata visar ett fält med namnet ”publicIpAddress” som innehåller IP-adressen i realtid frontend klustret. Läser du anvisningarna för din DNS-leverantör för att ställa in en post från FQDN som används i CNAME-post till den offentliga IP-adressen.



## <a name="auto-detection-of-a-certificate"></a>Automatisk identifiering av ett certifikat 

När du skapar en realtid webbprogram med hjälp av ”`az ml service create realtime`” kommandot Azure Machine Learning identifieras SSL på klustret och konfigurerar automatiskt bedömningsprofil URL med det angivna certifikatet. 

Kör följande kommando för att se certifikatstatus för. Observera ”https” prefixet för poängberäkningen URI och CNAME i värdnamnet. 

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
