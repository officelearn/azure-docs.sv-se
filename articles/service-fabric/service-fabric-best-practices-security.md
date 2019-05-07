---
title: Azure Service Fabric-säkerhetsmetoder | Microsoft Docs
description: Metodtips för Azure Service Fabric-säkerhet.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 3349abfb1b7cf85247b1bb5de8eb53fa09299b74
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65136490"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-säkerhet 

Mer information om [Azure säkerhetsmetoder](https://docs.microsoft.com/azure/security/), granska [säkerhetsmetoder för Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) är de rekommenderade hemligheterna management-tjänsten för Azure Service Fabric-program och -kluster.
> [!NOTE]
> Om certifikat/hemligheter från ett Key Vault distribueras till en Virtual Machine Scale Sets som en VM Scale Set Secret är måste sedan Key Vault och Virtual Machine Scale Sets samordnas.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Skapa certifikatutfärdare som utfärdade certifikatet för Service Fabric

Ett Azure Key Vault-certifikat kan antingen skapas eller importeras till ett Nyckelvalv. När ett Key Vault-certifikat har skapats kan den privata nyckeln skapas i Key Vault och aldrig exponeras för certifikatets ägare. Här finns sätt att skapa ett certifikat i Key Vault:

- Skapa ett självsignerat certifikat för att skapa ett offentligt / privat nyckelpar och associera det med ett certifikat. Certifikatet ska signeras av en egen nyckel. 
- Skapa ett nytt certifikat manuellt för att skapa ett offentligt / privat nyckelpar och generera ett X.509-certifikat som förfrågan. Signering begäran kan signeras av din registreringsutfärdare eller certifikatutfärdare. Signerade x509 certifikat kan sammanfogas med den väntande nyckeln koppla för att slutföra KV certifikatet i Key Vault. Den här metoden kräver flera steg, tillhandahåller men du med ökad säkerhet eftersom den privata nyckeln skapas i och begränsade till Key Vault. Detta förklaras i diagrammet nedan. 

Granska [metoder för Azure Keyvault Certificate](https://docs.microsoft.com/azure/key-vault/create-certificate) för ytterligare information.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Distribuera Key Vault-certifikat till Service Fabric-kluster VM-skalningsuppsättningar

Använda för att distribuera certifikat från en samordnad keyvault till en Virtual Machine Scale Sets, Virtual Machine Scale Sets [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Följande är i Resource Manager-mallens egenskaper:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Valvet måste vara aktiverat för Resource Manager för malldistribution.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Tillämpa en åtkomstkontrollista (ACL) på dina certifikat för Service Fabric-klustret

[Virtual Machine Scale Sets tillägg](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) publisher Microsoft.Azure.ServiceFabric används för att konfigurera din säkerhet i-noder.
Använd följande mallegenskaper för Resource Manager-för att tillämpa en ACL till dina certifikat för Service Fabric-kluster-processer:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Skydda ett Service Fabric-klustercertifikat genom nätverksnamn

Att skydda ditt Service Fabric-kluster av certifikat `Common Name`, Använd mallegenskapen Resource Manager- [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), enligt följande:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric-kluster använder den första giltigt certifikat hittas i certifikatarkivet för din värd. På Windows blir det här certifikatet med det senaste upphör datum som matchar det vanliga namn och tumavtrycket.

Azure-domäner, till exempel *\<YOUR UNDERDOMÄN\>. cloudapp.azure.com eller \<YOUR UNDERDOMÄN\>. trafficmanager.net, ägs av Microsoft. Certifikatutfärdare utfärdar inte certifikat för domäner för obehöriga användare. De flesta användare behöver du köpa en domän från en registrator eller vara domänadministratör auktoriserad för en certifikatutfärdare att utfärda ett certifikat med det gemensamma namnet.

För ytterligare information om hur du konfigurerar DNS-tjänsten för att lösa din domän till en Microsoft-IP-adress, Läs om hur du konfigurerar [Azure DNS som värd för din domän](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Efter att delegera din domäner namnservrarna för din Azure DNS-zonens namnservrar, lägger du till följande två poster till din DNS-zon:
> - Ett ' en ' poster för domänen APEX som inte är en `Alias record set` till alla IP-adresser din anpassade domän kommer att lösa.
> - En ”C”-post för Microsoft sub domäner du etablerat som inte är en `Alias record set`. Du kan till exempel använda Traffic Manager eller Belastningsutjämnarens DNS-namn.

Uppdatera din portal om du vill visa ett anpassat DNS-namn för Service Fabric-kluster `"managementEndpoint"`, uppdatera Följ mallegenskaper för Service Fabric Cluster Resource Manager:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Kryptera hemliga värden för Service Fabric-paket

Vanliga värden som är krypterade i Service Fabric-paket är autentiseringsuppgifter för Azure Container Registry (ACR), miljövariabler, inställningar och lagringskontonycklar för volymen för Azure-plugin-programmet.

Att [ställa in ett krypteringscertifikat och kryptera hemligheter på Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Generera ett självsignerat certifikat för att kryptera din hemlighet:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Följ instruktionerna i [distribuera Key Vault-certifikat till Service Fabric-kluster, virtuella datorer med skalningsuppsättningar](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) att distribuera Key Vault-certifikat till ditt Service Fabric kluster Virtual Machine Scale Sets.

Kryptera din hemlighet med hjälp av följande PowerShell-kommando och sedan uppdatera Service Fabric programmanifestet med det krypterade värdet:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Att [ställa in ett krypteringscertifikat och kryptera hemligheter i Linux-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Generera ett självsignerat certifikat för att kryptera dina hemligheter:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Följ instruktionerna i [distribuera Key Vault-certifikat till Service Fabric-kluster, virtuella datorer med skalningsuppsättningar](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) till ditt Service Fabric kluster Virtual Machine Scale Sets.

Kryptera din hemlighet med hjälp av följande kommandon och uppdatera sedan ditt Service Fabric Application Manifest med det krypterade värdet:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

När du krypterar dina skyddade värden [ange krypterade hemligheter i Service Fabric-program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application), och [dekryptera krypterade hemligheter från koden](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autentisera Service Fabric-program till Azure-resurser med hjälp av hanterad tjänstidentitet (MSI)

Mer information om hanterade identiteter för Azure-resurser, se [vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Azure Service Fabric-kluster finns på Virtual Machine Scale Sets som stöder [hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Att hämta en lista över tjänster som MSI kan användas för att autentisera sig för att se [Azure-tjänster som stöder Azure Active Directory Authentication](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Om du vill aktivera systemtilldelad hanterad identitet när du skapar en skalningsuppsättning för virtuella datorer eller en befintlig skalningsuppsättning för virtuella datorer, deklarera följande `"Microsoft.Compute/virtualMachinesScaleSets"` egenskapen:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Se [vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) för mer information.

Om du har skapat en [användartilldelade hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), deklarera följande resurs i mallen för att tilldela den till din skalningsuppsättning för virtuell dator. Ersätt `\<USERASSIGNEDIDENTITYNAME\>` hanteras med namnet på den Användartilldelad identitet som du skapade:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Innan din Service Fabric som programmet kan göra användning av en hanterad identitet, behörigheter måste beviljas till Azure-resurser som behövs för att autentisera med.
Följande kommandon bevilja åtkomst till en Azure-resurs:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

I programkoden Service Fabric [hämta en åtkomsttoken](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) för Azure Resource Manager genom att göra ett REST alla liknar följande:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Service Fabric-app kan sedan använda den åtkomst-token för att autentisera mot Azure-resurser som har stöd för Active Directory.
I följande exempel visas hur du gör detta för Cosmos DB-resursen:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```

## <a name="windows-defender"></a>Windows Defender 

Som standard installeras Windows Defender antivirus på Windows Server 2016. Mer information finns i [Windows Defender Antivirus på Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Användargränssnittet installeras som standard på vissa SKU:er, men det krävs inte. För att minska prestanda effekt och resurs förbrukning overhead åsamkar Windows Defender och om dina säkerhetsprinciper kan du undanta processer och sökvägar för programvara med öppen källkod, deklarera följande skala ange tillägg resursen för virtuella datorer Manager mallegenskaper för att undanta Service Fabric-klustret från genomsökningar:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> I ditt program mot skadlig kod-dokumentationen för konfigurationsregler om du inte använder Windows Defender. Windows Defender stöds inte i Linux.

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Skapa för Service Fabric-kluster för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer eller datorer som kör Linux: [Skapa en Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Lär dig mer om [Service Fabric-supportalternativ](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
