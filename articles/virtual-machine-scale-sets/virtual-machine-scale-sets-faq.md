---
title: "Skala virtuell Azure-dator Anger vanliga frågor och svar | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om virtuella datorer."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 1d7d6200196eee96186dc5e597abc84fa0aa86c5
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Skala virtuell Azure-dator Anger vanliga frågor och svar

Få svar på vanliga frågor och svar om virtuella datorer i Azure.

## <a name="autoscale"></a>Automatisk skalning

### <a name="what-are-best-practices-for-azure-autoscale"></a>Vad är bästa praxis för Azure Autoskala?

Bästa praxis för Autoskala finns [bästa praxis för autoskalning virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Var hittar jag mått namn för autoskalning som använder värdbaserad mått?

Tjänstmåttets namn för autoskalning som använder värdbaserad mått finns [stöds mått med Azure-Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Finns det några exempel på autoskalning baserat på en Azure Service Bus-avsnittet och kön längd?

Ja. Exempel på autoskalning baserat på en Azure Service Bus-avsnittet och kön längd finns [Azure-Monitor autoskalning vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Använd följande JSON för Service Bus-kö:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

För en kö för lagring, använder du följande JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Ersätt exempelvärden med din resurs Uniform Resource Identifier (URI: er).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Bör jag Autoskala med hjälp av värdbaserad mått eller ett tillägg för diagnostik?

Du kan skapa en autoskalningsinställning på en virtuell dator att använda värdnivå mått eller gäst-OS-baserade mått.

En lista över stöds mått finns [Azure-Monitor autoskalning vanliga mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

En fullständig exempelfil för skalningsuppsättningar i virtuella datorer finns [avancerade Autoskala konfiguration med hjälp av Resource Manager-mallar för virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

Används på värdnivå CPU måttet och ett meddelande count-mått.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hur ställer jag in Varningsregler i en skaluppsättning för virtuell dator?

Du kan skapa aviseringar för mått för skalningsuppsättningar i virtuella datorer via PowerShell eller Azure CLI. Mer information finns i [Azure-Monitor PowerShell snabb start prover](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) och [Azure-Monitor plattformsoberoende CLI snabb start exempel](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId för virtuella datorns skaluppsättning ser ut så här: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Du kan välja alla VM-prestandaräknaren som mått för att aktivera en avisering för. Mer information finns i [Gästoperativsystem mätvärden för Resource Manager-baserade virtuella Windows-datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) och [Gästoperativsystem mätvärden för virtuella Linux-datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) i den [Azure-Monitor autoskalning vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) artikel.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hur ställer jag in Autoskala på en virtuell dator skala in med hjälp av PowerShell?

Om du vill konfigurera Autoskala på en virtuell dator skala in med hjälp av PowerShell finns i bloggposten [lägga till Autoskala i en skaluppsättning för virtuell dator i Azure](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Certifikat

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Hur skickar jag ett certifikat för att den virtuella datorn på ett säkert sätt? Hur jag för att etablera en virtuell dator skala inställd på att köras en webbplats där SSL för webbplats levereras på ett säkert sätt från en Certifikatkonfiguration för? (Vanliga certifikat rotation åtgärden skulle vara nästan samma sätt som en uppdateringsåtgärd konfiguration.) Har du ett exempel på hur du gör detta? 

För att leverera ett certifikat till den virtuella datorn på ett säkert sätt, kan ett kund-certifikat installeras direkt till ett Windows-certifikatarkiv från kundens nyckelvalvet.

Använd följande JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Kod som har stöd för Windows och Linux.

Mer information finns i [skapa eller uppdatera en virtuell dator skaluppsättning](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Exempel på självsignerat certifikat

1.  Skapa ett självsignerat certifikat i en nyckelvalvet.

    Använd följande PowerShell-kommandon:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Login-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    Det här kommandot ger indata för Azure Resource Manager-mallen.

    Ett exempel på hur du skapar ett självsignerat certifikat i en nyckelvalvet finns [säkerhetsscenarier för Service Fabric-kluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Ändra Resource Manager-mallen.

    Lägg till den här egenskapen till **virtualMachineProfile**, som en del av den virtuella datorn skaluppsättning för resursen:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan jag ange en SSH-nyckel ska användas för SSH-autentisering med en Linux skaluppsättningen för virtuell dator från en Resource Manager-mall?  

Ja. REST API för **osProfile** liknar standard VM REST API. 

Inkludera **osProfile** i mallen:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Det här JSON-blocket används i [101-vm-sshkey GitHub Snabbstart mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
OS-profil används också i [grelayhost.json GitHub quick start mallen](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Mer information finns i [skapa eller uppdatera en virtuell dator skaluppsättning](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Hur tar jag bort föråldrad certifikat? 

Ta bort det gamla certifikatet från listan över certifikat valvet för att ta bort föråldrade certifikat. Lämna de certifikat som du vill ska finnas kvar på datorn i listan. Certifikatet tas inte bort från dina virtuella datorer. Dessutom lägger inte till certifikatet till nya virtuella datorer som skapas i virtuella datorns skaluppsättning. 

Ta bort certifikatet från befintliga virtuella datorer genom att skriva ett tillägg för anpassat skript för att ta bort certifikaten manuellt från certifikatarkivet.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Hur jag mata in en befintlig offentlig SSH-nyckel i virtuella scale set SSH lagret under etablering? Jag vill lagra SSH offentlig nyckelvärdena i Azure Key Vault och använda dem i Resource Manager-mall.

Om du anger de virtuella datorerna med en offentlig SSH-nyckel, behöver du inte placera de offentliga nycklarna i Nyckelvalvet. Offentliga nycklar är inte hemliga.
 
Du kan ange offentliga SSH-nycklar i klartext när du skapar en Linux VM:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
linuxConfiguration elementnamn | Krävs | Typ | Beskrivning
--- | --- | --- | --- |  ---
SSH | Nej | Samling | Anger den nyckel SSH-konfigurationen för ett Linux-operativsystem
sökväg | Ja | Sträng | Anger sökväg till Linux där SSH-nycklar eller certifikat ska hittas
nyckeldata | Ja | Sträng | Anger en base64-kodad SSH offentlig nyckel

Ett exempel finns [101-vm-sshkey GitHub Snabbstart mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>När kör `Update-AzureRmVmss` efter att lägga till fler än ett certifikat från samma nyckelvalvet, visas följande meddelande:
 
>Update-AzureRmVmss: Listan hemlighet innehåller upprepade förekomster av /subscriptions/ < min-prenumerations-id > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, vilket inte är tillåtet.
 
Detta kan inträffa om du försöker lägga till samma valvet istället för att använda ett nytt certifikat för valvet för befintliga källa valvet igen. Den `Add-AzureRmVmssSecret` kommandot fungerar inte korrekt om du lägger till ytterligare hemligheter.
 
Uppdatera listan över $vmss.properties.osProfile.secrets[0].vaultCertificates för att lägga till flera hemligheter från samma nyckelvalvet.
 
Förväntade indatastrukturen finns [skapa eller uppdatera en virtuell dator ange](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Hitta hemligheten i virtuella Skala uppsättningsobjekt som finns i nyckelvalvet. Lägg sedan till Certifikatreferens (URL: en och hemliga store-namn) i listan som är kopplad till valvet.

> [!NOTE] 
> För närvarande kan du ta bort certifikat från virtuella datorer med virtuella scale set API.
>

Nya virtuella datorer har inte det gamla certifikatet. Virtuella datorer som har certifikatet och som redan har distribuerats har dock det gamla certifikatet.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan jag push-certifikat till skaluppsättningen för virtuell dator utan att ange lösenord, när certifikatet finns i arkivet hemliga?

Du behöver inte hårdkoda lösenord i skript. Du kan dynamiskt hämta lösenord med de behörigheter som används för att köra skriptet för distribution. Om du har ett skript som flyttar ett certifikat från den hemliga Lagernyckeln valvet, arkivet hemliga `get certificate` kommandot också matar ut lösenordet för PFX-filen.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hur in arbete i egenskapen hemligheter för virtualMachineProfile.osProfile för en virtuell dator skala? Varför behöver jag sourceVault värdet när jag måste ange en absolut URI för ett certifikat med hjälp av egenskapen certificateUrl? 

En referens för Windows Remote Management (WinRM)-certifikat måste finnas i egenskapen hemligheter för OS-profil. 

Syftet med som anger valvet källa är att genomdriva åtkomstkontrollistan (ACL) principer för åtkomstkontroll som finns i en användares Azure Cloud Service model. Om käll-valvet har inte angetts, skulle användare som inte har behörighet att distribuera eller få åtkomst till nyckeln till en nyckelvalv kunna via en Compute Resource Provider (CRP). ACL: er finns även för resurser som inte finns.

Om du anger ett felaktigt källa valvet ID men en giltig key vault-URL, rapporteras ett fel när du söka igen.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Om jag lägger till hemligheter i en befintlig ange virtuella datorn, är hemligheter som läggs in i befintliga virtuella datorer eller bara nya? 

Certifikat har lagts till alla dina virtuella datorer, även redan befintliga viktiga. Om din virtuella skaluppsättning upgradePolicy egenskap är inställd på **manuell**, certifikatet har lagts till den virtuella datorn när du utför en manuell uppdatering på den virtuella datorn.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Där placera certifikat för Linux virtuella datorer?

Om du vill lära dig mer om att distribuera certifikat för Linux virtuella datorer, se [distribuera certifikat till virtuella datorer från en kundhanterad nyckelvalv](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hur lägger jag till ett nytt certifikat för valvet till ett nytt certifikatobjekt?

Om du vill lägga till ett certifikat för valvet i en befintlig hemlighet finns i följande PowerShell-exempel. Använd endast hemliga objekt.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Vad händer med certifikat om du återavbilda en virtuell dator?

Om du återavbilda en virtuell dator tas certifikat bort. Återställning av avbildning borttagningar i hela OS-disk. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Vad händer om du tar bort ett certifikat från nyckelvalvet?

Om hemligheten som tas bort från nyckelvalvet och sedan kör `stop deallocate` för dina virtuella datorer och starta sedan om dem igen, inträffar ett fel. Felet beror på att CRP måste hämta hemligheterna från nyckelvalvet, men den inte kan. I det här scenariot kan du ta bort certifikat från den virtuella datorn skala modellen. 

CRP-komponenten inte har behållits kunden hemligheter. Om du kör `stop deallocate` för alla virtuella datorer i virtuella datorns skaluppsättning cacheminnet har tagits bort. I det här scenariot hämtas hemligheter från nyckelvalvet.

Det uppstår inte här problemet när skala ut eftersom det inte finns en cachelagrad kopia av hemlighet i Azure Service Fabric (i en fabric-klient-modellen).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Varför måste jag ange den exakta platsen för certifikatet URL (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), som anges i [säkerhetsscenarier för Service Fabric-klustret](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Azure Key Vault-dokumentationen om att hämta hemligheten REST API ska returnera den senaste versionen av hemligheten som om versionen inte har angetts.
 
Metod | Webbadress
--- | ---
HÄMTA | https://mykeyvault.Vault.Azure.NET/secrets/ {hemlighet name} / {hemlighet version}? api-version = {api-version}

Ersätt {*hemlighet namn*} med namnet och Ersätt {*hemlighet version*} med versionen av den hemlighet som du vill hämta. Den hemliga versionen kan inte uteslutas. I så fall hämtas den aktuella versionen.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Varför måste jag ange certifikatet version när jag använder Key Vault?

Syftet med Key Vault-krav anger versionen certifikat är att göra det användarna vilka certifikat som har distribuerats på sina virtuella datorer.

Om du skapar en virtuell dator och uppdatera din hemlighet i nyckelvalvet hämtas inte det nya certifikatet till dina virtuella datorer. Men dina virtuella datorer visas att referera till den, och nya virtuella datorer får den nya hemligheten. Om du vill undvika detta krävs att referera till en hemliga versionen.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Min grupp fungerar med flera certifikat som har distribuerats till oss som .cer offentliga nycklar. Vad är den rekommenderade metoden för att distribuera dessa certifikat till en virtuell dator skala inställd?

Offentliga nycklar till en virtuell dator skala inställd för att distribuera .cer och du kan skapa en .pfx-fil som innehåller CER-filer. Det gör du genom att använda `X509ContentType = Pfx`. Till exempel läsa in den .cer-fil som x509Certificate2 objekt i C# eller PowerShell och sedan anropa metoden. 

Mer information finns i [X509Certificate.Export-metoden (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Jag ser inte ett alternativ för användare att skicka in certifikat som base64-strängar. De flesta andra resursleverantörer har det här alternativet.

Du kan extrahera senaste version URL-Adressen i en Resource Manager-mall för att emulera om ett certifikat som en base64-sträng. Inkludera följande JSON-egenskap i hanteraren för filserverresurser mallen:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Måste jag omsluter certifikat i JSON-objekt i nyckelvalv?

I skalningsuppsättningar i virtuella datorer och virtuella datorer, certifikaten omslutas i JSON-objekt. 

Vi stöder också innehållstypen application/x-pkcs12. Anvisningar om hur du använder application/x-pkcs12 finns [PFX-certifikat i Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Vi stöder för närvarande inte CER-filer. Om du vill använda CER-filer, exportera dem till .pfx-behållare.



## <a name="compliance-and-security"></a>Efterlevnad och säkerhet

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Är virtuella datorn anger PCI-kompatibel?

VM-skalningsuppsättningar är ett tunt API-läger ovanpå CRP. Båda komponenterna är en del av beräkningsplattformen i Azure-tjänsteträdet.

Från ett kompatibilitetsperspektiv är VM-skalningsuppsättningar en grundläggande del av Azure Compute-plattformen. De delar ett team, verktyg, processer, distributionsmetodik, säkerhetskontroller, JIT-kompilering (just-in-time), övervakning, aviseringar och så vidare med själva CRP:n. VM-skalningsuppsättningar är PCI-kompatibla (Payment Card Industry) eftersom CRP:n är en del av den aktuella attesteringen för PCI Data Security Standard (DSS).

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-vm-scale-sets"></a>Har [Azure hanterade tjänstidentiteten](https://docs.microsoft.com/azure/active-directory/msi-overview) fungerar med skalningsuppsättningar?

Ja. Du kan se några exempel MSI-mallar i Azure Quickstart-mallar. Linux: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). Windows: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Tillägg

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hur tar jag bort en virtuell dator skala utökas?

Använd följande PowerShell-exempel för att ta bort en virtuell dator skala utökas:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Du hittar en Tilläggsnamn värdet i `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-operations-management-suite"></a>Finns det en virtuell dator skala ange mall-exempel som kan integreras med Operations Management Suite?

En virtuell dator skala ange mall-exempel som kan integreras med Operations Management Suite, finns det andra exemplet i [distribuera ett Azure Service Fabric-kluster och aktivera övervakning genom att använda Log Analytics](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Det verkar som om tillägg körs parallellt på virtuella datorer. Detta leder till min tillägget för anpassat skript misslyckas. Vad kan jag göra för att åtgärda detta?

Mer information om tillägg ordningsföljd i skalningsuppsättningar i virtuella datorer, se [tillägget ordningsföljd i skalningsuppsättningar i virtuella Azure-datorn](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hur Återställ lösenordet för virtuella datorer i min skaluppsättning för virtuell dator?

Det finns två huvudsakliga sätt att ändra lösenordet för virtuella datorer i skalningsuppsättningar.

1. Ändra modellen VMSS direkt. Tillgängligt med Compute API 2017-12-01 och senare.

Uppdatera administratörsautentiseringsuppgifter direkt i scale set-modellen (till exempel med resursutforskaren i Azure, PowerShell eller CLI). När skaluppsättning är uppdaterad och alla nya har virtuella datorer de nya autentiseringsuppgifterna. Befintliga virtuella datorer har bara de nya autentiseringsuppgifterna om de avbildade. 

2. Återställa lösenordet med hjälp av access-tillägg för virtuell dator.

Använd följande PowerShell-exempel:

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hur lägger jag till ett tillägg till alla virtuella datorer i min skaluppsättning för virtuell dator?

Om principen anges till **automatisk**, omdistribuera mallen med de nya egenskaperna för webbtjänsttillägg uppdaterar alla virtuella datorer.

Om principen anges till **manuell**först uppdatera tillägget och uppdatera alla instanser i dina virtuella datorer manuellt.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Om de tillägg som är associerade med en befintlig skaluppsättning för virtuell dator uppdateras befintliga virtuella datorer som påverkas? (Det vill säga att de virtuella datorerna *inte* matchar den virtuella datorn skala modellen?) Eller ignoreras de? När en befintlig dator har service-läkt eller avbildade, är de skript som konfigurerats på virtuella datorns skaluppsättning körs eller är de skript som konfigurerades när den virtuella datorn skapades användas?

Om resurstilläggsdefinitionen i virtuella datorns skaluppsättning modellen uppdateras och upgradePolicy-egenskap är inställd på **automatisk**, uppdateras de virtuella datorerna. Om egenskapen upgradePolicy anges till **manuell**, tillägg har flaggats som inte matchar modellen. 

Om en befintlig virtuell dator är tjänsten lagats, visas den som en omstart och tillägg finns inte på nytt. Om det avbildade, är det som ersätter enhetens OS med källbilden. Alla specialisering från den senaste modellen, till exempel tillägg, körs.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-azure-ad-domain"></a>Hur jag ansluta till en virtuell dator skala en Azure AD-domän

Du kan definiera ett tillägg för att ansluta till en virtuell dator skala ett Azure Active Directory (Azure AD)-domän. 

Definiera ett tillägg genom att använda egenskapen JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Min virtuella skala utökas försöker installera något som kräver en omstart. Till exempel ”commandToExecute” ”: powershell.exe - ExecutionPolicy obegränsad Install-WindowsFeature-Name FS-Resource-Manager – IncludeManagementTools”

Om din virtuella skala utökas försöker installera något som kräver omstart, kan du använda Azure Automation Desired State Configuration (Automation DSC)-tillägget. Om operativsystemet är Windows Server 2012 R2, Azure hämtar i installationsprogrammet för Windows Management Framework (WMF) 5.0, omstarter, och sedan fortsätta med konfigurationen. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hur aktiverar jag program mot skadlig kod i min skaluppsättning för virtuell dator?

Om du vill aktivera program mot skadlig kod på virtuella datorns skaluppsättning, Använd följande PowerShell-exempel:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Jag behöver köra ett anpassat skript som finns i ett privat storage-konto. Skriptet har körts när lagring är offentlig, men när jag försöker använda en delad signatur åtkomst (SAS), misslyckas. Det här meddelandet visas: ”obligatoriska parametrar saknas för giltig signatur för delad åtkomst”. Länken + SAS fungerar bra i min lokala webbläsare.

Om du vill köra ett anpassat skript som finns i ett privat lagringskonto konfigurerat skyddade inställningarna lagringskontonyckel och namn. Mer information finns i [anpassade skript tillägget för Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Nätverk
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-will-apply-to-all-the-vm-nics-in-the-set"></a>Är det möjligt att tilldela en Nätverkssäkerhetsgrupp (NSG) till en skalningsuppsättning så att den gäller för alla Virtuella nätverkskort i uppsättningen?

Ja. En Nätverkssäkerhetsgrupp kan tillämpas direkt på en skala som refererar till den i avsnittet networkInterfaceConfigurations i nätverksprofilen. Exempel:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hur gör jag en VIP-växling för skalningsuppsättningar i virtuella datorer i samma prenumeration och samma region?

Om du har två skalningsuppsättningar i virtuella datorn med Azure belastningsutjämnare framför-servrar och de finns i samma prenumeration och region, kan du frigöra de offentliga IP-adresserna från var och en och tilldela till den andra. Se [VIP-växling: blå-grön distribution i Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) t.ex. Detta innebär en fördröjning nivå om resurserna är frigjord/fördelas på nätverket. Ett snabbare alternativ är att använda Azure Application Gateway med två serverdelspooler och en regel för vidarebefordran. Du kan också värd för ditt program med [Azure App service](https://azure.microsoft.com/services/app-service/) som ger stöd för snabb växling mellan olika platser för mellanlagring och produktion.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hur jag för att ange ett intervall med privata IP-adresser som ska användas för statisk privat IP-adressallokering?

IP-adresser har markerats från ett undernät som du anger. 

Allokeringsmetoden virtuella skala uppsättning IP-adresser är alltid ”dynamic”, men det betyder att du kan ändra dessa IP-adresser. I det här fallet betyder ”dynamic” bara att du inte anger IP-adressen i en PUT-begäran. Ange den statiska ange med undernätet. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hur distribuerar en virtuell dator skala inställd på befintliga Azure-nätverk? 

För att distribuera en virtuell dator skala inställd på befintliga Azure-nätverk finns [distribuera en virtuell dator skala har angetts till ett befintligt virtuellt nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Hur lägger jag till IP-adressen för den första virtuella datorn i en virtuell dator skala inställd på utdata från en mall

Om du vill lägga till IP-adressen för den första virtuella datorn i en virtuell dator skala inställd på utdata från en mall finns [ARM: hämta VMSS privata IP-adresser](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan jag använda skaluppsättningar med snabbare nätverk?

Ja. Om du vill använda snabbare nätverksfunktioner, ange enableAcceleratedNetworking till true i ditt skala uppsättningens networkInterfaceConfigurations inställningar. T.ex.
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hur kan jag konfigurera DNS-servrar som används av en skalningsuppsättning?

Lägg till ett dnsSettings JSON-paket till avsnittet scale set networkInterfaceConfigurations för att skapa en VM-skala med en anpassad DNS-konfiguration. Exempel:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hur konfigurerar en skala som anger att tilldela en offentlig IP-adress till varje virtuell dator?

Om du vill skapa en skaluppsättning för virtuell dator som tilldelar en offentlig IP-adress till varje virtuell dator, se till att API-versionen av resursen Microsoft.Compute/virtualMAchineScaleSets 2017-03-30 och Lägg till en _publicipaddressconfiguration_ JSON-paketet till skalan ange ipConfigurations avsnitt. Exempel:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Konfigurerar en skala som ska fungera med flera Programgatewayer?

Ja. Du kan lägga till resurs-id's för flera Programgateway serverdelsadresspooler till den _applicationGatewayBackendAddressPools_ lista i den _ipConfigurations_ delen av din skala inställd nätverksprofil.

## <a name="scale"></a>Skala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>I vilket fall skulle jag skapa en virtuell dator-skala med färre än två virtuella datorer?

En orsak till att skapa en virtuell dator-skala med färre än två virtuella datorer är att använda elastisk egenskaperna för en skaluppsättning för virtuell dator. Du kan till exempel distribuera en virtuell dator skala ange med noll virtuella datorer för att definiera din infrastruktur utan att betala VM driftskostnader. Sedan när du är redo att distribuera virtuella datorer, öka ”kapaciteten” för virtuella datorns skaluppsättning inställd på instansantalet produktion.

En annan orsak kan du skapa en virtuell dator-skala med färre än två virtuella datorer är om du vill göra mindre med tillgänglighet än med en tillgänglighetsuppsättning med diskreta virtuella datorer. Skaluppsättningar för den virtuella datorn ger dig möjlighet att arbeta med odifferentierad beräknings-enheter som är fungibla. Denna enhetlighet är nyckelfaktorn för skalningsuppsättningar i virtuella datorer jämfört med tillgänglighetsuppsättningar. Många tillståndslösa arbetsbelastningar spåra inte enskilda enheter. Om arbetsbelastningen släpper du skala till en datornod enhet och sedan att skala upp många när belastningen ökar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hur ändrar numret för virtuella datorer i en skaluppsättning för virtuell dator?

Om du vill ändra hur många virtuella datorer i en virtuell dator skaluppsättningen i Azure-portalen från skalan VM ställer egenskapsavsnittet, klickar du på bladet ”skalning” och använder skjutreglaget. Andra sätt att ändra instansantal, se [ändra instansantalet för en virtuella datorns skaluppsättning](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hur jag för att definiera anpassade aviseringar för när vissa trösklar har uppnåtts?

Har du viss flexibilitet i hur du hanterar aviseringar för angivna tröskelvärden. Du kan till exempel definiera anpassade webhooks. I följande exempel webhook är från en Resource Manager-mall:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

I det här exemplet skickas en avisering till Pagerduty.com när ett tröskelvärde uppnås.



## <a name="patching-and-operations"></a>Korrigering och åtgärder

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Hur skapar jag en skala som angetts i en befintlig resursgrupp

Skapa skalningsuppsättningar i en befintlig resurs grupp är ännu inte möjligt från Azure-portalen, men du kan ange en befintlig resursgrupp när distribuera en skala från en Azure Resource Manager-mall. Du kan också ange en befintlig resursgrupp när du skapar en skala som anges med Azure PowerShell eller CLI.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Kan vi flytta en skala som har angetts till en annan resursgrupp?

Ja, kan du flytta skaluppsättning resurser till en ny prenumeration eller resursgrupp.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jag kan hur Uppdatera mina virtuella skaluppsättningen till en ny avbildning till? Hur hanterar jag korrigering?

Uppdatera din virtuella skaluppsättningen till en ny avbildning och hantera korrigering finns [uppgradera en virtuella datorns skaluppsättning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan jag använda avbildningsåterställning igen för att återställa en virtuell dator utan att ändra bilden? (Det vill säga jag vill återställa en virtuell dator till fabriksinställningarna i stället för till en ny avbildning.)

Ja, du kan använda avbildningsåterställning åtgärden för att återställa en virtuell dator utan att ändra bilden. Men om din virtuella skalan refererar till en plattformsavbildning med `version = latest`, den virtuella datorn kan uppdatera till en senare OS-avbildningen när du anropar `reimage`.

Mer information finns i [hantera alla virtuella datorer i en skaluppsättning för virtuell dator](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-oms-operations-management-suite"></a>Är det möjligt att integrera skaluppsättningar med Azure OMS (Operations Management Suite)?

Ja, du kan installera tillägget OMS på skalan som virtuella datorer. Här är ett exempel på Azure CLI:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Du hittar den nödvändiga workspaceId och workspaceKey i OMS-portalen. Klicka på panelen inställningar på sidan Översikt. Klicka på fliken anslutna källor längst upp.

Obs: om din skalan _upgradePolicy_ anges till manuellt måste du tillämpa tillägget till alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. Detta skulle i CLI vara _az vmss update-instanser_.

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hur aktiverar jag startdiagnostikinställningar?

Om du vill aktivera startdiagnostikinställningar först skapa ett lagringskonto. Placera sedan JSON blocket i din virtuella datorns skaluppsättning **virtualMachineProfile**, och uppdatera virtuella datorns skaluppsättning:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

När en ny virtuell dator skapas visar egenskapen InstanceView för den virtuella datorn för skärmbild och så vidare. Här är ett exempel:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hur skaffar egenskapsinformation för varje virtuell dator utan att göra flera anrop? Till exempel hur kan jag feldomänen för varje 100 virtuella datorer i min skaluppsättning för virtuell dator?

För att få information om egenskaper för varje virtuell dator utan att göra flera anrop, kan du anropa `ListVMInstanceViews` genom att göra ett REST-API `GET` på följande resurs-URI:

/subscriptions/ < PRENUMERATIONSID > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan jag skicka annat tillägg argument till olika virtuella datorer i en skaluppsättning för virtuell dator?

Nej, kan du ange olika tillägg argument för olika virtuella datorer i en skaluppsättning för virtuell dator. Tillägg kan dock fungera baserat på unika egenskaperna för den virtuella datorn körs på, så som på namnet på datorn. Tillägg kan också fråga instans metadata på http://169.254.169.254 för mer information om den virtuella datorn.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Varför är det glapp mellan min Virtuella datornamn på virtuell dator skala och VM-ID: N? Till exempel: 0, 1, 3...

Det förekommer glapp mellan din Virtuella datornamn på virtuell dator skala och VM-ID: N eftersom skalan för din virtuella **overprovision** egenskap är inställd på standardvärdet **SANT**. Om överetablering anges till **SANT**, flera virtuella datorer än begärt skapas. Extra VM: ar så tas bort. I så fall måste du få bättre distribution tillförlitlighet, men på bekostnad av sammanhängande namngivning och sammanhängande (Network Address Translation Translation) regler. 

Du kan ange egenskapen **FALSKT**. För små virtuella skaluppsättningar påverkar detta avsevärt inte distribution tillförlitlighet.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Vad är skillnaden mellan att ta bort en virtuell dator i en skaluppsättning för virtuell dator och det har frigjorts den virtuella datorn? När ska jag välja rätt scenario?

Den största skillnaden mellan att ta bort en virtuell dator i en skaluppsättning för virtuell dator och det har frigjorts den virtuella datorn är att `deallocate` inte ta bort de virtuella hårddiskarna (VHD). Finns kostnader för lagring som är associerade med körs `stop deallocate`. Du kan använda en av av något av följande skäl:

- Vill du avbryta betalar beräkning kostnader, men du vill behålla diskstatusen på de virtuella datorerna.
- Du vill starta en uppsättning virtuella datorer snabbare än vad du kan skala ut en skaluppsättning för virtuell dator.
  - Relaterad till det här scenariot kan har du skapat egna Autoskala motor och vill en snabbare skala för slutpunkt till slutpunkt.
- Du har en skaluppsättning för virtuell dator som är ojämnt fördelad över feldomäner eller uppdatera domäner. Detta kan bero på att du selektivt ta bort virtuella datorer eller eftersom virtuella datorer har tagits bort efter överetablering. Kör `stop deallocate` följt av `start` på den virtuella datorn skaluppsättningen jämnt distribuerar de virtuella datorerna i feldomäner eller uppdatera domäner.

