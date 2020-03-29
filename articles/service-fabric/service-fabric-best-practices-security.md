---
title: Metodtips för Azure Service Fabric-säkerhet
description: Metodtips och designöverväganden för att skydda Azure Service Fabric-kluster och program.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258933"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-säkerhet 

Mer information om [metodtips för Azure Security finns](https://docs.microsoft.com/azure/security/)i [metodtips för Azure Service Fabric](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) är den rekommenderade hemligheterhanteringstjänsten för Azure Service Fabric-program och kluster.
> [!NOTE]
> Om certifikat/hemligheter från ett nyckelvalv distribueras till en skalauppsättning för virtuell dator som en hemlighet för skalan för virtuell dator måste skalningsuppsättningen för nyckelvalv och virtuell dator vara samlokalisering.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Skapa certifikatutfärdaren utfärdat Service Fabric-certifikat

Ett Azure Key Vault-certifikat kan antingen skapas eller importeras till ett Nyckelvalv. När ett Key Vault-certifikat skapas skapas den privata nyckeln i Nyckelvalvet och exponeras aldrig för certifikatägaren. Så här skapar du ett certifikat i Key Vault:

- Skapa ett självsignerat certifikat för att skapa ett offentligt-privat nyckelpar och associera det med ett certifikat. Certifikatet signeras med en egen nyckel. 
- Skapa ett nytt certifikat manuellt för att skapa ett offentligt-privat nyckelpar och generera en X.509-begäran om signering av certifikat. Signeringsbegäran kan undertecknas av din registreringsmyndighet eller certifikatutfärdare. Det signerade x509-certifikatet kan slås samman med det väntande nyckelparet för att slutföra KV-certifikatet i Key Vault. Även om den här metoden kräver fler steg ger den dig större säkerhet eftersom den privata nyckeln skapas i och begränsas till Key Vault. Detta förklaras i diagrammet nedan. 

Läs [Azure Keyvault-metoder](https://docs.microsoft.com/azure/key-vault/create-certificate) för att skapa certifikat för ytterligare information.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Distribuera Key Vault-certifikat till skala uppsättningar med virtuell dator för serviceinfrastruktur-kluster

Om du vill distribuera certifikat från en samlokaliseringsnyckelvault till en skalauppsättning för virtuell dator använder du Virtual Machine Scale Set [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Följande är mallegenskaperna för Resource Manager:

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
> Valvet måste vara aktiverat för Resurshanterarens malldistribution.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Använda en åtkomstkontrollista (ACL) på ditt certifikat för ditt Service Fabric-kluster

[Tillägg för skalningsuppsättning för virtuella](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) datorer är utgivaren av Microsoft.Azure.ServiceFabric används för att konfigurera nodsäkerhet.
Om du vill använda en åtkomstkontrollista på dina certifikat för service fabric-klusterprocesser använder du följande Egenskaper för Resource Manager-mall:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Skydda ett klustercertifikat för service fabric med vanligt namn

Om du vill skydda `Common Name`servicetygsklustret efter certifikat använder du egenskapscertifikatet Resource Manager [PropertyCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)enligt följande:

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
> Service Fabric-kluster använder det första giltiga certifikat som hittas i värdens certifikatarkiv. I Windows är detta certifikatet med det senaste utgångsdatumet som matchar ditt gemensamma namn och utfärdare av tumavtryck.

Azure-domäner, till\<exempel *\>DIN UNDERDOMÄN \<.cloudapp.azure.com eller\>DIN UNDERDOMÄN .trafficmanager.net, ägs av Microsoft. Certifikatutfärdare utfärdar inte certifikat för domäner till obehöriga användare. De flesta användare måste köpa en domän från en registrator, eller vara en auktoriserad domänadministratör, för att en certifikatutfärdare ska kunna utfärda ett certifikat med det gemensamma namnet.

Mer information om hur du konfigurerar DNS-tjänsten för att matcha domänen till en Microsoft IP-adress finns i hur du konfigurerar [Azure DNS så att den är värd för din domän](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> När du har delegerat dina domännamnsservrar till dina Azure DNS-zonnamnsservrar lägger du till följande två poster i DNS-zonen:
> - En A-post för domän APEX `Alias record set` som INTE är en till alla IP-adresser din anpassade domän kommer att lösa.
> - En C-post för Microsoft-underdomäner som `Alias record set`du har etablerat och som INTE är en . Du kan till exempel använda Traffic Manager eller Load Balancer DNS-namn.

Om du vill uppdatera portalen för att `"managementEndpoint"`visa ett anpassat DNS-namn för Service Fabric Cluster uppdaterar du mallegenskaperna för Tjänsten Fabric Cluster Resource Manager:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Hemliga värden för kryptera service fabric-paket

Vanliga värden som krypteras i Service Fabric-paket inkluderar ACR-autentiseringsuppgifter (Azure Container Registry), miljövariabler, inställningar och Azure Volume plugin-lagringskontonycklar.

Så här konfigurerar du [ett krypteringscertifikat och krypterar hemligheter i Windows-kluster:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)

Generera ett självsignerat certifikat för att kryptera din hemlighet:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Använd instruktionerna i [Distribuera nyckelvalvscertifikat till service fabric-klusters virtuella datorskalningsuppsättningar](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) för att distribuera Key Vault-certifikat till servicetygsklustrets skalningsuppsättningar för virtuella datorer.

Kryptera din hemlighet med följande PowerShell-kommando och uppdatera sedan ditt Programmanifest för Service Fabric med det krypterade värdet:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Så här konfigurerar du [ett krypteringscertifikat och krypterar hemligheter på Linux-kluster:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)

Generera ett självsignerat certifikat för att kryptera dina hemligheter:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Använd instruktionerna i [Distribuera nyckelvalvscertifikat till service fabric-klusterskalningsuppsättningar](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) för virtuella datorer till servicetygsklustrets skalningsuppsättningar för virtuella datorer.

Kryptera din hemlighet med följande kommandon och uppdatera sedan programmanifestet för Service Fabric med det krypterade värdet:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

När du har krypterat dina skyddade värden [anger du krypterade hemligheter i Service Fabric Application](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)och [dekrypterar krypterade hemligheter från servicekod](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Inkludera certifikat i Service Fabric-program

Om du vill ge programmet åtkomst till hemligheter inkluderar du certifikatet genom att lägga till ett **SecretsCertificate-element** i programmanifestet.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autentisera tjänst fabric-program till Azure-resurser med hjälp av managed service identity (MSI)

Mer information om hanterade identiteter för Azure-resurser finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
Azure Service Fabric-kluster finns på skalningsuppsättningar för virtuella datorer, som stöder [hanterad tjänstidentitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Information om hur du hämtar en lista över tjänster som MSI kan användas för att autentisera till finns i [Azure Services som stöder Azure Active Directory Authentication](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Om du vill aktivera den hanterade systemtilldelade identiteten när en skaluppsättning för `"Microsoft.Compute/virtualMachinesScaleSets"` virtuella datorer skapas eller en befintlig skaluppsättning för virtuella datorer deklarerar du följande egenskap:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Se [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)

Om du har skapat en [användartilldelad hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)deklarerar du följande resurs i mallen för att tilldela den till skaluppsättningen för den virtuella datorn. Ersätt `\<USERASSIGNEDIDENTITYNAME\>` med namnet på den användartilldelade hanterade identitet som du skapade:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Innan ditt Service Fabric-program kan använda en hanterad identitet måste behörigheter beviljas till de Azure-resurser som det behöver autentisera med.
Följande kommandon ger åtkomst till en Azure-resurs:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

I din Tjänst Fabric-programkod [får du en åtkomsttoken](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) för Azure Resource Manager genom att göra en REST som alla liknar följande:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Din Service Fabric-app kan sedan använda åtkomsttoken för att autentisera till Azure-resurser som stöder Active Directory.
I följande exempel visas hur du gör detta för Cosmos DB-resurs:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows-säkerhetsbaslinjer
[Vi rekommenderar att du implementerar en konfiguration av branschstandard som är allmänt känd och väl beprövad, till exempel Microsofts säkerhetsbaslinjer, i stället för att skapa en baslinje själv.](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) Ett alternativ för att etablera dessa på dina skaluppsättningar för virtuella datorer är att använda DSC-tilläggshanteraren (Azure Desired State Configuration) för att konfigurera de virtuella datorerna när de är online, så att de kör produktionsprogrammet.

## <a name="azure-firewall"></a>Azure Firewall
[Azure Firewall är en hanterad, molnbaserad nätverkssäkerhetstjänst som skyddar dina Azure Virtual Network-resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet.](https://docs.microsoft.com/azure/firewall/overview) Detta gör det möjligt att begränsa utgående HTTP/S-trafik till en angiven lista över fullständigt kvalificerade domännamn (FQDN) inklusive jokertecken. Den här funktionen kräver inte SSL-avslutning. Dess rekommenderade att du utnyttja [Azure Firewall FQDN-taggar](https://docs.microsoft.com/azure/firewall/fqdn-tags) för Windows-uppdateringar, och att aktivera nätverkstrafik till Microsoft Windows Update slutpunkter kan flöda genom din brandvägg. [Distribuera Azure-brandväggen med hjälp av en mall](https://docs.microsoft.com/azure/firewall/deploy-template) innehåller ett exempel för Microsoft.Network/azureFirewalls resursmallsdefinition. Brandväggsregler som är gemensamma för Service Fabric-program är att tillåta följande för ditt virtuella klusternätverk:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Dessa brandväggsregler kompletterar dina tillåtna utgående nätverkssäkerhetsgrupper, som skulle inkludera ServiceFabric och lagring, som tillåtna destinationer från det virtuella nätverket.

## <a name="tls-12"></a>TLS 1.2
[Gts](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Som standard installeras Windows Defender antivirus på Windows Server 2016. Mer information finns i [Windows Defender Antivirus på Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Användargränssnittet installeras som standard på vissa SKU:er, men det krävs inte. Om du vill minska prestandapåverkan och omkostnader för resursförbrukning som windows defender ådrar sig, och om dina säkerhetsprinciper gör att du kan utesluta processer och sökvägar för programvara med öppen källkod, deklarerar du följande tilläggsresurs för skalningsuppsättning för virtuell dator Egenskaper för hanterarens mall för att utesluta ditt Service Fabric-kluster från genomsökningar:


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
> I dokumentationen till Antimalware finns konfigurationsregler om du inte använder Windows Defender. Windows Defender stöds inte på Linux.

## <a name="platform-isolation"></a>Isolering av plattform
Som standard beviljas Service Fabric-program åtkomst till själva service fabric-körningen, som manifesterar sig i olika former: [miljövariabler](service-fabric-environment-variables-reference.md) som pekar på filsökvägar på värden som motsvarar program- och Fabric-filer, en interprocess-kommunikationsslutpunkt som accepterar programspecifika begäranden och klientcertifikatet som Fabric förväntar sig att programmet ska använda för att autentisera sig själv. I den eventualitet som tjänsten är värd sig själv opålitlig kod, är det lämpligt att inaktivera denna åtkomst till SF runtime - om inte uttryckligen behövs. Åtkomsten till körningen tas bort med hjälp av följande deklaration i avsnittet Principer i programmanifestet: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Nästa steg

* Skapa ett kluster på virtuella datorer eller datorer som kör Windows Server: [Service Fabric-klusterverktyget för Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Skapa ett kluster på virtuella datorer, eller datorer, som kör Linux: [Skapa ett Linux-kluster](service-fabric-cluster-creation-via-portal.md).
* Läs mer om [supportalternativ för Service Fabric.](service-fabric-support.md)

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
