---
title: Vanliga frågor och svar om Virtual Machine Scale Sets
description: Få svar på de vanligaste frågorna om skalningsuppsättningar för virtuella datorer i Azure.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mimckitt
ms.openlocfilehash: c2db0cca120d08b85229618547a2aaabbba437ad
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870218"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Vanliga frågor och svar om Virtual Machine Scale Sets

Få svar på vanliga frågor om skalningsuppsättningar för virtuella datorer i Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Vanliga frågor och svar om skalningsuppsättningar

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hur många virtuella datorer kan man ha i en skalningsuppsättning?

En skalningsuppsättning kan ha 0 till 1 000 virtuella datorer baserat på plattformsavbildningar, eller 0 till 600 virtuella datorer baserat på anpassade avbildningar.

### <a name="are-data-disks-supported-within-scale-sets"></a>Kan datadiskar användas i skalningsuppsättningar?

Ja. En skalningsuppsättning kan definiera en konfiguration för anslutna datadiskar som gäller för alla virtuella datorer i uppsättningen. Mer information finns i [Azure scale sets and attached data disks (Azure-skalningsuppsättningar och anslutna datadiskar)](virtual-machine-scale-sets-attached-disks.md). Andra alternativ för att lagra data är:

* Azure-filer (delade SMB-enheter)
* OS-enhet
* Temporär enhet (lokal, backas inte upp av Azure Storage)
* Azure-datatjänst (t.ex. Azure-tabeller, Azure-blobbar)
* Extern datatjänst (t.ex. fjärrdatabas)

### <a name="which-azure-regions-support-scale-sets"></a>Vilka Azure-regioner har stöd för skalningsuppsättningar?

Alla regioner stöder skalningsuppsättningar.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hur skapar jag en skalningsuppsättning med en anpassad avbildning?

Skapa och fånga en VM-avbildning och använd den sedan som källa för din skalningsuppsättning. Om du vill ha en självstudiekurs om hur du skapar och använder en anpassad VM-avbildning kan du använda [Azure CLI](tutorial-use-custom-image-cli.md) eller [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Vilka virtuella datorer tas bort om jag minskar skalningsuppsättningens kapacitet från 20 till 15?

Virtuella datorer tas bort från skalningsuppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten. Virtuella datorer med högst ID tas bort först.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens ID från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer balanseras mellan feldomäner och uppdateringsdomäner.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan jag framtvinga en körning av sekvensen när jag använder flera tillägg i en skalningsuppsättning?

Ja, du kan använda [skalningsuppsättningstilläggssekvensering](virtual-machine-scale-sets-extension-sequencing.md).

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Fungerar skalningsuppsättningar med Azures tillgänglighetsuppsättningar?

En regional skaluppsättning (icke-zonal) använder *placeringsgrupper*som fungerar som en implicit tillgänglighetsuppsättning med fem feldomäner och fem uppdateringsdomäner. Skalningsuppsättningar med mer än 100 virtuella datorer sträcker sig över flera placeringsgrupper. Mer information om placeringsgrupper finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma virtuella nätverk som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer (som ofta kräver unika konfigurationer) i en tillgänglighetsuppsättning och placera datanoder i skalningsuppsättningen.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Fungerar skalningsuppsättningar med Azure-tillgänglighetszoner?

Visst! Mer information finns i [skalningsuppsättningszonen doc](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatisk skalning

### <a name="what-are-best-practices-for-azure-autoscale"></a>Vilka är metodtips för Azure Autoscale?

Metodtips för Automatisk skalning finns i [Metodtips för automatisk skalning av virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Var hittar jag måttnamn för automatisk skalning som använder värdbaserade mått?

Måttnamn för automatisk skalning som använder värdbaserade mått finns [i Mått som stöds med Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Finns det några exempel på automatisk skalning baserat på ett Azure Service Bus-ämne och kölängd?

Ja. Exempel på automatisk skalning baserat på ett Azure Service Bus-ämne och kölängd finns i [Azure Monitor för automatisk skalning av vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

För en Service Bus-kö använder du följande JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Använd följande JSON för en lagringskö:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Ersätt exempelvärden med resursen enhetliga resursidentifierare (URI:er).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Ska jag skala automatiskt med hjälp av värdbaserade mått eller ett diagnostiktillägg?

Du kan skapa en automatisk skalningsinställning på en virtuell dator för att använda värden på värdnivå eller gäst-OS-baserade mått.

En lista över mått som stöds finns i [Azure Monitor för automatisk skalning av vanliga mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Ett fullständigt exempel på skaluppsättningar för virtuella datorer finns i [Avancerad konfiguration för automatisk skalning med hjälp av Resource Manager-mallar för skalningsuppsättningar för virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

I exemplet används cpu-måttet på värdnivå och ett mått för antalet meddelanden.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hur ställer jag in varningsregler för en skalningsuppsättning för virtuella datorer?

Du kan skapa aviseringar om mått för skalningsuppsättningar för virtuella datorer via PowerShell eller Azure CLI. Mer information finns i [Snabbstartsexmplar för Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) och [snabbstartsexempel för Azure Monitor på flera plattformar](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId för skalningsuppsättningen för virtuella datorer ser ut så här:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname /subscription/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname /subscription/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname /subscription/subscription

Du kan välja valfri vm-prestandaräknare som mått att ställa in en avisering för. Mer information finns i [Svarvmått för Gästoperativsystem för Resource Manager-baserade Windows virtuella datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) och [gäst-OS-mått för virtuella Linux-datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) i artikeln [för automatisk skalning av vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) i Azure Monitor.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hur konfigurerar jag automatisk skalning på en skalbar virtuell dator med PowerShell?

Om du vill konfigurera automatisk skalning på en skala för virtuella datorer med PowerShell läser du [automatiskt skala en skalbar för den virtuella datorn](tutorial-autoscale-powershell.md). Du kan också konfigurera automatisk skalning med [Azure CLI-](tutorial-autoscale-cli.md) och [Azure-mallarna](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Om jag har stoppat (deallocated) en virtuell dator, är det VM startas som en del av en automatisk skalning?

Nej. Om regler för automatisk skalning kräver ytterligare VM-instanser som en del av en skalningsuppsättning skapas en ny VM-instans. VM-instanser som stoppas (frigörs) startas inte som en del av en händelse för automatisk skalning. De stoppade (deallocated) virtuella datorer kan dock tas bort som en del av en händelse för automatisk skalning som skalas i antalet instanser, på samma sätt som en VM-instans kan tas bort baserat på ordningen för VM-instans-ID.



## <a name="certificates"></a>Certifikat

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hur skickar jag ett certifikat till den virtuella datorn på ett säkert sätt?

Om du vill leverera ett certifikat till den virtuella datorn på ett säkert sätt kan du installera ett kundcertifikat direkt i ett Windows-certifikatarkiv från kundens nyckelvalv.

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

Koden stöder Windows och Linux.

Mer information finns i [Skapa eller uppdatera en skaluppsättning för den virtuella datorn](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hur använder jag självsignerade certifikat som har etablerats för Azure Service Fabric Clusters?
I det senaste exemplet används följande azure CLI-sats i azure shell, läs Dokumentation för Service Fabrics CLI-modul Exempel, som skrivs ut till stdout:

```azurecli
az sf cluster create -h
```

Självsignerade certifikat kan inte användas för distribuerat förtroende som tillhandahålls av en certifikatutfärdare och bör inte användas för något Service Fabric-kluster som är avsett att vara värd för företagets produktionslösningar. Om du vill ha ytterligare säkerhetsvägledning för serviceinfrastruktur kan du läsa [säkerhetsscenarier](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) för Azure Service Fabric Security Och [Säkerhetsscenarier för serviceinfrastruktur](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan jag ange ett SSH-nyckelpar som ska användas för SSH-autentisering med en skala från en Virtuell Datoruppsättning för Linux från en Resource Manager-mall?

Ja. REST API för **osProfile** liknar standard-VM REST API.

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

Det här JSON-blocket används i [den här Azure-snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Mer information finns i [Skapa eller uppdatera en skaluppsättning för den virtuella datorn](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Hur tar jag bort inaktuella certifikat?

Om du vill ta bort föråldrade certifikat tar du bort det gamla certifikatet från listan över valvcertifikat. Lämna alla certifikat som du vill behålla på datorn i listan. Detta tar inte bort certifikatet från alla dina virtuella datorer. Det lägger inte heller till certifikatet i nya virtuella datorer som skapas i skalningsuppsättningen för den virtuella datorn.

Om du vill ta bort certifikatet från befintliga virtuella datorer använder du ett anpassat skripttillägg för att manuellt ta bort certifikaten från certifikatarkivet.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hur injicerar jag en befintlig SSH-offentlig nyckel i SSH-lagret för virtuell dators skalning under etableringen?

Om du bara tillhandahåller de virtuella datorerna med en offentlig SSH-nyckel behöver du inte placera de offentliga nycklarna i Key Vault. Offentliga nycklar är inte hemliga.

Du kan ange offentliga SSH-nycklar i oformaterad text när du skapar en virtuell Linux-dator:

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
}
```

linuxKonfiguration element namn | Krävs | Typ | Beskrivning
--- | --- | --- | ---
Ssh | Inga | Samling | Anger SSH-nyckelkonfigurationen för ett Linux OS
path | Ja | Sträng | Anger den Linux-filsökväg där SSH-nycklarna eller certifikatet ska finnas
keyData (keyData) | Ja | Sträng | Anger en base64-kodad SSH-offentlig nyckel

Ett exempel finns [i snabbstartsmallen 101-vm-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>När jag `Update-AzVmss` körs efter att jag har lagt till mer än ett certifikat från samma nyckelvalv visas följande meddelande:

>Update-AzVmss: List secret innehåller upprepade instanser\<av /subscriptions/ my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, som inte tillåts.

Detta kan inträffa om du försöker lägga till samma valv igen i stället för att använda ett nytt valvcertifikat för det befintliga källvalvet. Kommandot `Add-AzVmssSecret` fungerar inte korrekt om du lägger till ytterligare hemligheter.

Om du vill lägga till fler hemligheter från samma nyckelvalv uppdaterar du listan $vmss.properties.osProfile.secrets[0].vaultCertificates.

Den förväntade indatastrukturen finns i [Skapa eller uppdatera en uppsättning för virtuella datorer](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Hitta hemligheten i det virtuella datorskaleobjektet som finns i nyckelvalvet. Lägg sedan till certifikatreferensen (URL:en och det hemliga butiksnamnet) i listan som är associerad med valvet.

> [!NOTE]
> För närvarande kan du inte ta bort certifikat från virtuella datorer med hjälp av API:et för skalningsuppsättning för virtuell dator.
>

Nya virtuella datorer har inte det gamla certifikatet. Virtuella datorer som har certifikatet och som redan har distribuerats har dock det gamla certifikatet.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan jag skicka certifikat till den virtuella datorns skalningsuppsättning utan att ange lösenordet när certifikatet finns i det hemliga arkivet?

Du behöver inte hårdkoda lösenord i skript. Du kan dynamiskt hämta lösenord med de behörigheter du använder för att köra distributionsskriptet. Om du har ett skript som flyttar ett certifikat från `get certificate` det hemliga arkivets nyckelvalv, skriver kommandot Secret Store också ut lösenordet för PFX-filen.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hur fungerar egenskapen Secrets för virtualMachineProfile.osProfile för en skalningsuppsättning för virtuella datorer? Varför behöver jag sourceVault-värdet när jag måste ange den absoluta URI:n för ett certifikat med egenskapen certificateUrl?

En WinRM-certifikatreferens (Windows Remote Management) måste finnas i egenskapen Secrets för OS-profilen.

Syftet med att ange källvalvet är att tillämpa ACL-principer (Access Control List) som finns i en användares Azure Cloud Service-modell. Om källvalvet inte anges kan användare som inte har behörighet att distribuera eller komma åt hemligheter till ett nyckelvalv via en COMPUTE Resource Provider (CRP). ACL finns även för resurser som inte finns.

Om du anger ett felaktigt källvalvs-ID men en giltig url för nyckelvalv rapporteras ett fel när du avsöker åtgärden.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Om jag lägger till hemligheter i en befintlig uppsättning skala för virtuella datorer, injiceras hemligheterna i befintliga virtuella datorer eller bara i nya?

Certifikat läggs till i alla dina virtuella datorer, även befintliga. Om egenskapen virtual machine scale set upgradePolicy är inställd på **manuell**läggs certifikatet till i den virtuella datorn när du utför en manuell uppdatering på den virtuella datorn.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Var lägger jag certifikat för virtuella Linux-datorer?

Mer information om hur du distribuerar certifikat för virtuella Linux-datorer finns i [Distribuera certifikat till virtuella datorer från ett kundhanterad nyckelvalv](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hur lägger jag till ett nytt valvcertifikat i ett nytt certifikatobjekt?

Information om hur du lägger till ett valvcertifikat i en befintlig hemlighet finns i följande PowerShell-exempel. Använd bara ett hemligt objekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Vad händer med certifikat om du vill ha en virtuell dator igen?

Om du vill ha en virtuell dator igen tas certifikat bort. Om du vill ta bort hela OS-disken tas bort.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Vad händer om du tar bort ett certifikat från nyckelvalvet?

Om hemligheten tas bort från nyckelvalvet `stop deallocate` och sedan kör för alla dina virtuella datorer och sedan startar dem igen, stöter du på ett fel. Felet beror på att CRP måste hämta hemligheterna från nyckelvalvet, men det kan inte. I det här fallet kan du ta bort certifikaten från den virtuella datorns skaluppsättningsmodell.

CRP-komponenten är inte beständiga kundhemligheter. Om du `stop deallocate` kör för alla virtuella datorer i skalningsuppsättningen för den virtuella datorn tas cachen bort. I det här fallet hämtas hemligheter från nyckelvalvet.

Du stöter inte på det här problemet när du skalar ut eftersom det finns en cachelagd kopia av hemligheten i Azure Service Fabric (i klientmodellen med en enda infrastruktur).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Varför måste jag ange certifikatversionen när jag använder Key Vault?

Syftet med key vault-kravet att ange certifikatversionen är att klargöra för användaren vilket certifikat som distribueras på deras virtuella datorer.

Om du skapar en virtuell dator och sedan uppdaterar din hemlighet i nyckelvalvet hämtas inte det nya certifikatet till dina virtuella datorer. Men dina virtuella datorer verkar referera till det, och nya virtuella datorer får den nya hemligheten. För att undvika detta måste du referera till en hemlig version.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mitt team arbetar med flera certifikat som distribueras till oss som .cer offentliga nycklar. Vilken är den rekommenderade metoden för att distribuera dessa certifikat till en skalningsuppsättning för virtuella datorer?

Om du vill distribuera .cer-offentliga nycklar till en skaluppsättning för virtuella datorer kan du generera en PFX-fil som bara innehåller .cer-filer. Använd för att `X509ContentType = Pfx`göra detta . Läs till exempel in .cer-filen som ett x509Certificate2-objekt i C# eller PowerShell och anropa sedan metoden.

Mer information finns i [X509Certificate.Export Method (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Hur skickar jag in certifikat som base64-strängar?

Om du vill emulera att skicka in ett certifikat som en base64-sträng kan du extrahera den senaste versions-URL:en i en Resource Manager-mall. Inkludera följande JSON-egenskap i resource manager-mallen:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Måste jag radbrytas certifikat i JSON-objekt i nyckelvalv?

I skalningsuppsättningar för virtuella datorer och virtuella datorer måste certifikaten radbryts i JSON-objekt.

Vi stöder även innehållstypen application/x-pkcs12.

Vi stöder för närvarande inte .cer-filer. Om du vill använda .cer-filer exporterar du dem till PFX-behållare.



## <a name="compliance-and-security"></a>Efterlevnad och säkerhet

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Är skalningsuppsättningar för virtuella datorer PCI-kompatibla?

VM-skalningsuppsättningar är ett tunt API-läger ovanpå CRP. Båda komponenterna är en del av beräkningsplattformen i Azure-tjänsteträdet.

Från ett kompatibilitetsperspektiv är VM-skalningsuppsättningar en grundläggande del av Azure Compute-plattformen. De delar ett team, verktyg, processer, distributionsmetodik, säkerhetskontroller, JIT-kompilering (just-in-time), övervakning, aviseringar och så vidare med själva CRP:n. VM-skalningsuppsättningar är PCI-kompatibla (Payment Card Industry) eftersom CRP:n är en del av den aktuella attesteringen för PCI Data Security Standard (DSS).

Mer information finns [i Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Fungerar [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/msi-overview) med skaluppsättningar för virtuella datorer?

Ja. Du kan se några exempel PÅ MSI-mallar i Azure Quickstart-mallar för [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) och [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Ta bort 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Kommer de lås som jag har angett på plats på skalningsinstanser för virtuella datorer att respekteras när instanser tas bort?

I Azure Portal har du möjlighet att ta bort en enskild instans eller massborttagning genom att välja flera instanser. Om du försöker ta bort en enskild instans som har ett lås på plats respekteras låset och du kommer inte att kunna ta bort instansen. Men om du massval flera instanser och någon av dessa instanser har ett lås på plats, kommer låsen /s) inte respekteras och alla valda instanser kommer att tas bort. 
 
I Azure CLI har du bara möjlighet att ta bort en enskild instans. Om du försöker ta bort en enskild instans som har ett lås på plats respekteras låset och du kommer inte att kunna ta bort den instansen. 

## <a name="extensions"></a>Tillägg

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hur tar jag bort ett skalet för virtuell datoruppsättning?

Om du vill ta bort ett tillägg för skaluppsättningar för virtuella datorer använder du följande PowerShell-exempel:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Du hittar värdet extensionName `$vmss`i .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Finns det ett mallexempel för en virtuell datorskala som integreras med Azure Monitor-loggar?

Ett exempel på en uppsättning mall för en virtuell dator som integreras med Azure Monitor-loggar finns i det andra exemplet i [Distribuera ett Azure Service Fabric-kluster och aktivera övervakning med hjälp av Azure Monitor-loggar](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hur lägger jag till ett tillägg till alla virtuella datorer i min skalningsuppsättning för virtuella datorer?

Om uppdateringsprincipen är inställd **på automatisk**uppdateras alla virtuella datorer om du distribuerar om mallen med de nya tilläggsegenskaperna.

Om uppdateringsprincipen är inställd på **manuell**uppdaterar du tillägget och uppdaterar sedan alla instanser manuellt i de virtuella datorerna.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Om tilläggen som är associerade med en befintlig skaluppsättning för virtuella datorer uppdateras, påverkas befintliga virtuella datorer?

Om tilläggsdefinitionen i skaluppsättningsmodellen för den virtuella datorn uppdateras och egenskapen upgradePolicy är inställd **på automatisk**uppdateras de virtuella datorerna. Om egenskapen upgradePolicy är inställd på **manuell**flaggas tillägg som inte matchar modellen.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Körs tillägg igen när en befintlig maskin är tjänstläkt eller ominställd?

Om en befintlig virtuell dator är tjänstläkt visas den som en omstart och tilläggen körs inte igen. Om en virtuell dator är omimaged, är processen liknande ersätta OS-enheten med källavbildningen. All specialisering från den senaste modellen, till exempel tillägg, körs igen.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hur ansluter jag en skala för en virtuell dator till en Active Directory-domän?

Om du vill ansluta en skala för virtuella datorer till en AD-domän (Active Directory) kan du definiera ett tillägg.

Om du vill definiera ett tillägg använder du egenskapen JsonADDomainExtension:

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Min virtuella dator skala uppsättning förlängning försöker installera något som kräver en omstart.

Om tillägget för skalningsuppsättning för virtuella datorer försöker installera något som kräver en omstart kan du använda tillägget Azure Automation Desired State Configuration (Automation DSC). Om operativsystemet är Windows Server 2012 R2 hämtar Azure installationen av Windows Management Framework (WMF) 5.0, startar om och fortsätter sedan med konfigurationen.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hur aktiverar jag skadlig kod i min skalningsuppsättning för virtuella datorer?

Om du vill aktivera skadlig kod på din skalningsuppsättning för virtuella datorer använder du följande PowerShell-exempel:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hur kör jag ett anpassat skript som finns i ett privat lagringskonto?

Om du vill köra ett anpassat skript som finns i ett privat lagringskonto konfigurerar du skyddade inställningar med lagringskontonyckeln och namnet. Mer information finns i [Anpassat skripttillägg](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Lösenord

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hur återställer jag lösenordet för virtuella datorer i min skalningsuppsättning för virtuella datorer?

Det finns två huvudsakliga sätt att ändra lösenordet för virtuella datorer i skalningsuppsättningar.

- Ändra skaluppsättningen för den virtuella datorn direkt. Tillgänglig med API 2017-12-01 och senare.

    Uppdatera administratörsautentiseringsuppgifterna direkt i skalningsuppsättningsmodellen (till exempel med hjälp av Azure Resource Explorer, PowerShell eller CLI). När skalningsuppsättningen har uppdaterats har alla nya virtuella datorer de nya autentiseringsuppgifterna. Befintliga virtuella datorer har bara de nya autentiseringsuppgifterna om de är omgjorda.

- Återställ lösenordet med hjälp av vm-åtkomsttilläggen. Se till att följa lösenordskraven som beskrivs [här](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).

    Använd följande PowerShell-exempel:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Nätverk

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Är det möjligt att tilldela en NSG (Network Security Group) till en skalningsuppsättning, så att den gäller för alla nätverkskort för virtuella datorer i uppsättningen?

Ja. En nätverkssäkerhetsgrupp kan tillämpas direkt på en skala som anges genom att referera till den i avsnittet NetworkInterfaceConfigurations i nätverksprofilen. Exempel:

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
                            },
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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hur gör jag en VIP-swap för skalningsuppsättningar för virtuella datorer i samma prenumeration och samma region?

Om du har två skalningsuppsättningar för virtuella datorer med Azure Load Balancer-front-slutar och de finns i samma prenumeration och region, kan du frigöra offentliga IP-adresser från var och en och tilldela den andra. Se [VIP-växling: Blågrön distribution i Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) till exempel. Detta innebär en fördröjning men eftersom resurserna är dislokaliserade / tilldelas på nätverksnivå. Ett snabbare alternativ är att använda Azure Application Gateway med två backend-pooler och en routningsregel. Alternativt kan du vara värd för ditt program med [Azure App-tjänsten](https://azure.microsoft.com/services/app-service/) som ger stöd för snabb växling mellan mellanlagring och produktionsplatser.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hur anger jag ett intervall med privata IP-adresser som ska användas för statisk privat IP-adressallokering?

IP-adresser väljs från ett undernät som du anger.

Allokeringsmetoden för IP-adresser för virtuell datorskala är alltid "dynamisk", men det betyder inte att dessa IP-adresser kan ändras. I det här fallet betyder "dynamisk" bara att du inte anger IP-adressen i en PUT-begäran. Ange den statiska uppsättningen med hjälp av undernätet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hur distribuerar jag en skala för virtuella datorer till ett befintligt virtuellt Azure-nätverk?

Information om hur du distribuerar en skala för virtuella datorer till ett befintligt virtuellt Azure-nätverk finns i [Distribuera en skala för virtuell dator till ett befintligt virtuellt nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan jag använda skalningsuppsättningar med Accelerated Networking?

Ja. Om du vill använda accelererade nätverk ställer du in enableAcceleratedNetworking till true i skalningsuppsättningens inställningar för nätverksgränssnittskonfigurationer. Exempel

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
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hur konfigurerar jag DNS-servrarna som används av en skalningsuppsättning?

Om du vill skapa en skalningsuppsättning för virtuella datorer med en anpassad DNS-konfiguration lägger du till ett dnsSettings JSON-paket i avsnittet skalauppsättning networkInterfaceConfigurations. Exempel:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hur kan jag konfigurera en skalningsuppsättning för att tilldela en offentlig IP-adress till varje virtuell dator?

Om du vill skapa en skalningsuppsättning för virtuella datorer som tilldelar en offentlig IP-adress till varje virtuell dator kontrollerar du att API-versionen av Microsoft.Compute/virtualMachineScaleSets-resursen är 2017-03-30 och lägger till ett _JSON-paket_ för offentlig konfiguration av den skalade ipKonfigurationerna. Exempel:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan jag konfigurera en skalningsuppsättning så att den fungerar med flera programgateways?

Ja. Du kan lägga till resurs-ID:erna för flera backend-adresspooler för Application Gateway i listan _applicationGatewayDAddressPools_ i avsnittet _ipConfigurations_ i din skalningsuppsättning nätverksprofil.

## <a name="scale"></a>Skala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>I vilket fall skulle jag skapa en virtuell dator skala uppsättning med färre än två virtuella datorer?

En anledning till att skapa en skalningsuppsättning för virtuella datorer med färre än två virtuella datorer skulle vara att använda de elastiska egenskaperna för en skaluppsättning för virtuella datorer. Du kan till exempel distribuera en skalningsuppsättning för virtuella datorer med noll virtuella datorer för att definiera infrastrukturen utan att betala vm-driftskostnader. När du sedan är redo att distribuera virtuella datorer ökar du "kapaciteten" för den virtuella datorns skala inställd på antalet produktionsinstanser.

En annan orsak till att du kan skapa en skaluppsättning för virtuella datorer med färre än två virtuella datorer är om du är mindre intresserad av tillgänglighet än att använda en tillgänglighetsuppsättning med diskreta virtuella datorer. Skala uppsättningar för virtuella datorer ger dig ett sätt att arbeta med odifferentierade beräkningsenheter som är fungible. Den här enhetligheten är en viktig differentiator för skaluppsättningar för virtuella datorer jämfört med tillgänglighetsuppsättningar. Många tillståndslösa arbetsbelastningar spårar inte enskilda enheter. Om arbetsbelastningen sjunker kan du skala ned till en beräkningsenhet och sedan skala upp till många när arbetsbelastningen ökar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hur ändrar jag antalet virtuella datorer i en skaluppsättning för virtuella datorer?

Om du vill ändra antalet virtuella datorer i en skala för virtuella datorer som anges i Azure-portalen klickar du på bladet "Skalning" från avsnittet Egenskaper för skalning av virtuella datorer och använder skjutreglaget.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hur definierar jag anpassade aviseringar för när vissa tröskelvärden har uppnåtts?

Du har viss flexibilitet i hur du hanterar aviseringar för angivna tröskelvärden. Du kan till exempel definiera anpassade webhooks. Följande webhook-exempel kommer från en Resource Manager-mall:

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
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
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
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Korrigering och drift

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan jag skapa en skalningsuppsättning i en befintlig resursgrupp?

Ja, du kan skapa en skalningsuppsättning i en befintlig resursgrupp.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan jag flytta en skalningsuppsättning till en annan resursgrupp?

Ja, du kan flytta skalningsuppsättningsresurser till en ny prenumeration eller resursgrupp.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hur uppdaterar jag min skala för virtuella datorer till en ny avbildning? Hur hanterar jag korrigeringsfiler?

Om du vill uppdatera skalan för den virtuella datorn till en ny avbildning och hantera korrigering finns i [Uppgradera en skalningsuppsättning för den virtuella datorn](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan jag använda omaktiveringen för att återställa en virtuell dator utan att ändra avbildningen? (Det vill säga, jag vill återställa en virtuell dator till fabriksinställningar snarare än till en ny avbildning.)

Ja, du kan använda ominmatningsåtgärden för att återställa en virtuell dator utan att ändra avbildningen. Men om din virtuella dator skalningsuppsättning `version = latest`refererar till en plattformsavbildning `reimage`med kan den virtuella datorn uppdateras till en senare OS-avbildning när du ringer .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Är det möjligt att integrera skalningsuppsättningar med Azure Monitor-loggar?

Ja, det kan du genom att installera Azure Monitor-tillägget på de virtuella skalningsuppsättningarna. Här är ett Azure CLI-exempel:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

Du hittar den arbetsyta Som krävs och workspaceKey i Log Analytics-arbetsytan i Azure-portalen. Klicka på panelen Inställningar på sidan Översikt. Klicka på fliken Anslutna källor högst upp.

> [!NOTE]
> Om din scale set _upgradePolicy_ är inställd på Manuell måste du använda tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgradering på dem. I CLI detta skulle vara _az vmss uppdatering-instanser_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hur aktiverar jag startdiagnostik?

Om du vill aktivera startdiagnostik skapar du först ett lagringskonto. Placera sedan det här JSON-blocket i den virtuella datorns skalningsuppsättning **virtualMachineProfile**och uppdatera skaluppsättningen för den virtuella datorn:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

När en ny virtuell dator skapas visar egenskapen InstanceView för den virtuella datorn detaljerna för skärmbilden och så vidare. Här är ett exempel:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Egenskaper för virtuella datorer

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hur får jag egenskapsinformation för varje virtuell dator utan att ringa flera samtal? Hur skulle jag till exempel få feldomänen för var och en av de 100 virtuella datorerna i min virtuella datorskalauppsättning?

Om du vill hämta egenskapsinformation för varje `ListVMInstanceViews` virtuell dator utan `GET` att ringa flera samtal kan du anropa genom att göra ett REST API på följande resurs-URI:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceVisa&$select=instanceVisa

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan jag skicka olika tilläggsargument till olika virtuella datorer i en skalningsuppsättning för virtuella datorer?

Nej, du kan inte skicka olika tilläggsargument till olika virtuella datorer i en skalningsuppsättning för virtuella datorer. Tillägg kan dock fungera baserat på de unika egenskaperna för den virtuella datorn som de körs på, till exempel på datornamnet. Tillägg kan också fråga http://169.254.169.254 instansmetadata på för att få mer information om den virtuella datorn.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Varför finns det luckor mellan mina vm-datornamn för virtuella datorer och VM-ID:er? Till exempel: 0, 1, 3...

Det finns luckor mellan den virtuella datorns skalningsuppsättning VM-datornamn och VM-ID:er eftersom **egenskapen** för virtuell datorskala har angetts till standardvärdet **för true**. Om överetablering är inställt på **true**skapas fler virtuella datorer än vad som begärs. Extra virtuella datorer tas sedan bort. I det här fallet får du ökad distributionstillförlitlighet, men på bekostnad av sammanhängande namngivning och sammanhängande NAT-regler (Network Address Translation).

Du kan ange den här egenskapen till **false**. För små skaluppsättningar för virtuella datorer påverkar detta inte distributionssäkerhet nämnvärt.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Vad är skillnaden mellan att ta bort en virtuell dator i en skalauppsättning för virtuella datorer och att frigöra den virtuella datorn? När ska jag välja det ena framför det andra?

Den största skillnaden mellan att ta bort en virtuell dator i `deallocate` en virtuell dator skala uppsättning och deallocating den virtuella datorn är att inte ta bort virtuella hårddiskar (VHDs). Det finns lagringskostnader `stop deallocate`i samband med att köra . Du kan använda det ena eller det andra av någon av följande skäl:

- Du vill sluta betala beräkningskostnader, men du vill behålla disktillståndet för de virtuella datorerna.
- Du vill starta en uppsättning virtuella datorer snabbare än du kan skala ut en skalningsuppsättning för den virtuella datorn.
  - I samband med det här scenariot kan du ha skapat en egen motor för automatisk skalning och vill ha en snabbare skala från på slutna till slutna dagar.
- Du har en skalningsuppsättning för den virtuella datorn som är ojämnt fördelad över feldomäner eller uppdateringsdomäner. Det kan bero på att du selektivt har tagit bort virtuella datorer eller på att virtuella datorer togs bort efter överetablering. Körs `stop deallocate` följt `start` av på den virtuella datorn skala som jämnt distribuerar virtuella datorer över fel domäner eller uppdatera domäner.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hur tar jag en ögonblicksbild av en skalningsuppsättningsinstans för virtuell dator?
Skapa en ögonblicksbild från en instans av en skaluppsättning för virtuella datorer.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Skapa en hanterad disk från ögonblicksbilden.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
