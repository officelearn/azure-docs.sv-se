---
title: Vanliga frågor och svar för skalningsuppsättningar för virtuella Azure-datorer
description: Få svar på vanliga frågor om skalnings uppsättningar för virtuella datorer i Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 222f26febb7b14c627307295a8cdd68a17694d03
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254162"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Vanliga frågor och svar för skalningsuppsättningar för virtuella Azure-datorer

Få svar på vanliga frågor och svar om skalningsuppsättningar för virtuella datorer i Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Längst upp vanliga frågor om skalningsuppsättningar

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hur många virtuella datorer kan man ha i en skalningsuppsättning?

En skalningsuppsättning kan innehålla 0 och 1 000 virtuella datorer baserade på plattformsavbildningar eller 0 och 600 virtuella datorer baserade på anpassade avbildningar.

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

Skapa och hämta en datoravbildning av virtuell och sedan använda det som källa för din skalningsuppsättning. En själv studie kurs om hur du skapar och använder en anpassad VM-avbildning kan du använda [Azure CLI](tutorial-use-custom-image-cli.md) eller [Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Vilka virtuella datorer tas bort om jag minskar skalningsuppsättningens kapacitet från 20 till 15?

Virtuella datorer tas bort från skalningsuppsättningen jämnt bland uppgraderingsdomäner och feldomäner för att maximera tillgängligheten. Virtuella datorer med högst ID tas bort först.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Hur blir om det om jag sedan ökar kapaciteten från 15 till 18?

Om du ökar kapaciteten till 18 skapas 3 nya virtuella datorer. Varje gång ökas den virtuella datorinstansens ID från det tidigare högsta värdet (t.ex. 20, 21, 22). Virtuella datorer balanseras mellan feldomäner och uppdateringsdomäner.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan jag framtvinga en körning av sekvensen när jag använder flera tillägg i en skalningsuppsättning?

Ja, du kan använda [tillägg](virtual-machine-scale-sets-extension-sequencing.md)för skalnings uppsättning.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Fungerar skalningsuppsättningar med Azures tillgänglighetsuppsättningar?

En regional (icke-zonindelade) skalnings uppsättning använder *placerings grupper*som fungerar som en implicit tillgänglighets uppsättning med fem fel domäner och fem uppdaterings domäner. Skalningsuppsättningar med mer än 100 virtuella datorer sträcker sig över flera placeringsgrupper. Mer information om placeringsgrupper finns i [Arbeta med stora skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-placement-groups.md). En tillgänglighetsuppsättning för virtuella datorer kan finnas i samma virtuella nätverk som en skalningsuppsättning för virtuella datorer. En vanlig konfiguration är att placera virtuella kontrollnodsdatorer (som ofta kräver unika konfigurationer) i en tillgänglighetsuppsättning och placera datanoder i skalningsuppsättningen.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Skala uppsättningar fungerar med Azure tillgänglighetszoner?

Visst! Mer information finns i avsnittet om [skalnings uppsättnings zon](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatisk skalning

### <a name="what-are-best-practices-for-azure-autoscale"></a>Vad är bästa praxis för Azure-autoskalning?

För bästa praxis för automatisk skalning, se [metod tips för automatisk skalning av virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Var hittar jag tjänstmåttets namn för automatisk skalning som använder värdbaserade mått?

För mått namn för automatisk skalning som använder värdbaserade mått, se [mått som stöds med Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Finns det några exempel på automatisk skalning baserat på en Azure Service Bus-ämne och kön längd?

Ja. Exempel på automatisk skalning baserat på ett Azure Service Bus ämne och Kölängd finns i [Azure Monitor autoskalning av vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

För en Service Bus-kö, använder du följande JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

För en lagringskö, använder du följande JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Ersätt exempelvärden med din resurs Uniform Resource Identifier (URI: er).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Bör jag automatisk skalning med hjälp av värdbaserade mått eller en diagnostiktillägget?

Du kan skapa en autoskalningsinställning på en virtuell dator att använda värdnivå mått eller gäst-OS-baserade mått.

En lista över mått som stöds finns i [Azure Monitor autoskalning av vanliga mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Ett fullständigt exempel för skalnings uppsättningar för virtuella datorer finns i [Avancerad konfiguration av automatisk skalning med hjälp av Resource Manager-mallar för skalnings uppsättningar för virtuella datorer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

Exemplet använder värdnivå CPU måttet och ett antal mått för meddelandet.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hur ställer jag in Varningsregler på en skalningsuppsättning för virtuell dator?

Du kan skapa aviseringar för mått för skalningsuppsättningar för virtuella datorer via PowerShell eller Azure CLI. Mer information finns i [Azure Monitor PowerShell snabb starts exempel](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) och [Azure Monitor cross-platform CLI snabb starts exempel](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

TargetResourceId av virtuella datorns skalningsuppsättning ser ut så här:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Du kan välja valfri VM-prestandaräknaren som mått som ska ställa in en avisering för. Mer information finns i [gäst operativ systemets mått för Resource Manager-baserade virtuella Windows-datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) och [gäst operativ system mått för virtuella Linux-datorer](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) i artikeln [Azure Monitor autoskalning av vanliga mått](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/) .

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hur konfigurerar jag autoskalning i en VM-skalningsuppsättning med PowerShell?

Om du vill konfigurera automatisk skalning på en skalnings uppsättning för virtuella datorer med hjälp av PowerShell läser du skala [automatiskt en skalnings uppsättning för virtuella datorer](tutorial-autoscale-powershell.md). Du kan också konfigurera autoskalning med [Azure CLI](tutorial-autoscale-cli.md) och [Azure-mallar](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Om jag har stoppat (frigjort) är den virtuella datorn igång som en del av en åtgärd för automatisk skalning för en virtuell dator?

Nej. Om regler för automatisk skalning kräver ytterligare VM-instanser som en del av en skalningsuppsättning, skapas en ny VM-instans. VM-instanser som är stoppad (frigjord) startas inte som en del av en händelse med automatisk skalning. Men kan dessa Stoppad (frigjord) virtuella datorer tas bort som en del av en händelse för automatisk skalning som skalas antalet instanser, på samma sätt som att alla VM-instans kan tas bort baserat på ordningen VM-instans-ID.



## <a name="certificates"></a>Certifikat

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hur skickar jag ett certifikat till den virtuella datorn på ett säkert sätt?

Du kan installera ett certifikat för kunden direkt i ett Windows-certifikatarkiv från kundens key vault för att leverera ett certifikat till den virtuella datorn på ett säkert sätt.

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

Koden har stöd för Windows och Linux.

Mer information finns i [skapa eller uppdatera en skalnings uppsättning för virtuella datorer](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hur gör jag för att använda självsignerade certifikat för Azure Service Fabric-kluster?
I det senaste exemplet använder du följande Azure CLI-instruktion i Azure-gränssnittet: läsa Service Fabric CLI module exempel dokumentation, som skrivs ut till stdout:

```bash
az sf cluster create -h
```

Självsignerade certifikat kan inte användas för distribuerat förtroende från en certifikat utfärdare och ska inte användas för Service Fabric kluster som är värdar för företags produktions lösningar. Mer Service Fabric säkerhets rikt linjer finns i [metod tips för Azure Service Fabric säkerhet](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) och [Service Fabric kluster säkerhets scenarier](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan jag ange en SSH-nyckelpar ska användas för SSH-autentisering med en Linux VM-skalningsuppsättning från en mall i Resource Manager?

Ja. REST API för **osProfile** liknar standard REST API för virtuella datorer.

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

Detta JSON-block används i [den här snabb starts mal len för Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Mer information finns i [skapa eller uppdatera en skalnings uppsättning för virtuella datorer](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Hur tar jag bort föråldrad certifikat?

Ta bort inaktuella certifikat genom att ta bort det gamla certifikatet från valvet certifikat. Lämna de certifikat som du vill behålla på datorn i listan. Certifikatet tas inte bort från dina virtuella datorer. Den också lägger inte till certifikatet till nya virtuella datorer som skapas i virtuella datorns skalningsuppsättning.

Ta bort certifikatet från befintliga virtuella datorer genom att använda ett anpassat skript tillägg för att manuellt ta bort certifikaten från certifikat arkivet.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hur jag mata in en befintlig offentlig SSH-nyckel i VM scale set SSH lagret under etableringen?

Om du tillhandahåller de virtuella datorerna endast med en offentlig SSH-nyckel, behöver du inte placera de offentliga nycklarna i Key Vault. Offentliga nycklar är inte hemliga.

Du kan ange offentliga SSH-nycklar i oformaterad text när du skapar en Linux-VM:

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

linuxConfiguration elementnamn | Krävs | Typ | Beskrivning
--- | --- | --- | ---
SSH | Nej | Samling | Anger den viktiga SSH-konfigurationen för en Linux-operativsystem
path | Ja | String | Anger sökvägen till Linux där SSH-nycklar eller certifikat ska vara belägen
nyckeldata | Ja | String | Anger en base64-kodad offentlig SSH-nyckel

Ett exempel finns i [snabb starts mal len 101-VM-sshkey GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>När jag kör `Update-AzVmss` efter att ha lagt till mer än ett certifikat från samma nyckel valv visas följande meddelande:

>Update-AzVmss: lista hemlighet innehåller upprepade instanser av/Subscriptions/\<My-Subscription-ID >/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, vilket inte tillåts.

Detta kan inträffa om du försöker lägga till samma valv istället för att använda ett nytt valv-certifikat för den befintliga källvalv igen. `Add-AzVmssSecret` kommandot fungerar inte korrekt om du lägger till ytterligare hemligheter.

Uppdatera listan över $vmss.properties.osProfile.secrets[0].vaultCertificates för att lägga till fler hemligheter från samma nyckelvalvet.

En förväntad ingående struktur finns i [skapa eller uppdatera en virtuell dator uppsättning](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Hitta hemligheten i VM scale set objektet som är i nyckelvalvet. Lägg sedan till Certifikatreferens (URL: en och det hemliga butiksnamnet) i listan som är associerade med valvet.

> [!NOTE]
> För närvarande kan du ta bort certifikat från virtuella datorer med hjälp av VM scale set-API.
>

Nya virtuella datorer har inte det gamla certifikatet. Virtuella datorer som har certifikatet och som redan har distribuerats har dock det gamla certifikatet.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan jag överföra certifikat till skalningsuppsättning för virtuell dator utan att ange lösenord, när certifikatet i arkivet för hemliga?

Du behöver inte hårdkoda lösenord i skript. Du kan dynamiskt hämta lösenord med de behörigheter som används för att köra skriptet för distribution. Om du har ett skript som flyttar ett certifikat från nyckel valvet för den hemliga lagrings platsen, ger det hemliga arkivet `get certificate`-kommandot även lösen ordet för PFX-filen.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hur ställer arbete i egenskapen hemligheter för virtualMachineProfile.osProfile för en virtuell datorskalning? Varför behöver jag sourceVault värdet när jag behöver ange absolut URI för ett certifikat med hjälp av egenskapen certificateUrl?

En referens för Windows Remote Management (WinRM)-certifikat måste finnas i egenskapen hemligheter för OS-profil.

Syftet med som anger källvalv är att genomdriva åtkomstkontrollistan (ACL) principer för åtkomstkontroll som finns i en användares Azure Cloud Service-modellen. Om käll-valvet har inte angetts skulle användare som inte har behörighet att distribuera eller få åtkomst till hemligheter till ett nyckelvalv kunna via en Compute Resource Provider (CRP). ACL: er finns även för resurser som inte finns.

Om du anger en felaktig källa valv-ID men en giltig key vault-Webbadress rapporteras ett fel när du söka igen.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Om jag lägger till hemligheter till en befintlig VM-skalningsuppsättning ange, finns hemligheter som matas in i befintliga virtuella datorer eller endast i nya?

Certifikat läggs till i alla dina virtuella datorer, även före befintliga. Om den virtuella datorns skalnings uppsättning upgradePolicy-egenskap är inställd på **manuell**, läggs certifikatet till den virtuella datorn när du utför en manuell uppdatering på den virtuella datorn.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Var placera certifikat för Linux-datorer?

Information om hur du distribuerar certifikat för virtuella Linux-datorer finns i [distribuera certifikat till virtuella datorer från ett kundhanterat nyckel valv](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hur lägger jag till ett nytt valv-certifikat till ett nytt certifikatobjekt?

Se följande PowerShell-exempel för att lägga till ett vault-certifikat till en befintlig hemlighet. Använd bara en hemlig objekt.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Vad händer med certifikat om du återavbilda en virtuell dator?

Om du återavbilda en virtuell dator, raderas certifikat. Avbildningen borttagningar hela OS-disk.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Vad händer om du tar bort ett certifikat från key vault?

Om hemligheten tas bort från nyckel valvet och du sedan kör `stop deallocate` för alla virtuella datorer och sedan startar dem igen, uppstår ett problem. Felet inträffar eftersom CRP: N behöver hämta hemligheterna från nyckelvalvet, men den inte kan. I det här scenariot kan du ta bort certifikat från VM-skalningsuppsättningen.

Komponenten CRP sparas inte kunden hemligheter. Om du kör `stop deallocate` för alla virtuella datorer i den virtuella datorns skalnings uppsättning, tas cachen bort. I det här scenariot hämtas hemligheter från nyckelvalvet.

Det uppstår inte problemet vid utskalning eftersom det inte finns en cachelagrad kopia av hemlighet i Azure Service Fabric (i modellen single-fabric-klient).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Varför måste jag ange versionen certifikat när jag använder Key Vault?

Syftet med Key Vault-krav för att ange den certifikat-versionen är att göra det för användarna vilka certifikat som har distribuerats på sina virtuella datorer.

Om du skapar en virtuell dator och uppdatera din hemlighet i nyckelvalvet, och det nya certifikatet hämtas till dina virtuella datorer. Men dina virtuella datorer visas att referera till den och nya virtuella datorer får den nya hemligheten. Om du vill undvika detta krävs att referera till en version av hemlighet.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mitt team fungerar med flera certifikat som har distribuerats till oss som .cer offentliga nycklar. Vad är den rekommenderade metoden för att distribuera dessa certifikat till en virtuell datorskalning inställd?

För att distribuera .cer offentliga nycklar till en virtuell datorskalning ange kan du skapa en .pfx-fil som innehåller endast CER-filer. Använd `X509ContentType = Pfx`för att göra detta. Till exempel läsa in .cer-filen som x509Certificate2 objekt i C# eller PowerShell och sedan anropa metoden.

Mer information finns i [X509Certificate. export-metoden (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Hur gör jag för att skicka in certifikat som base64-strängar?

Du kan extrahera den senaste version URL i en Resource Manager-mall för att emulera skicka i ett certifikat som en base64-sträng. Ta med följande JSON-egenskapen i Resource Manager-mallen:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Måste jag omsluta certifikat i JSON-objekt i nyckelvalv?

Certifikat måste inneslutas i JSON-objekt i skalningsuppsättningar för virtuella datorer och virtuella datorer.

Vi har också stöd för innehållstypen application/x-pkcs12.

För närvarande stöder vi inte CER-filer. Om du vill använda CER-filer, exportera dem till .pfx-behållare.



## <a name="compliance-and-security"></a>Efterlevnad och säkerhet

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Är skalningsuppsättningar för virtuella datorer PCI-kompatibel?

VM-skalningsuppsättningar är ett tunt API-läger ovanpå CRP. Båda komponenterna är en del av beräkningsplattformen i Azure-tjänsteträdet.

Från ett kompatibilitetsperspektiv är VM-skalningsuppsättningar en grundläggande del av Azure Compute-plattformen. De delar ett team, verktyg, processer, distributionsmetodik, säkerhetskontroller, JIT-kompilering (just-in-time), övervakning, aviseringar och så vidare med själva CRP:n. VM-skalningsuppsättningar är PCI-kompatibla (Payment Card Industry) eftersom CRP:n är en del av den aktuella attesteringen för PCI Data Security Standard (DSS).

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Fungerar [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/msi-overview) med skalnings uppsättningar för virtuella datorer?

Ja. Du kan se några exempel på MSI-mallar i Azure snabb starts mallar för [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) och [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Rader 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Kommer de Lås jag ställer in på plats på instanser av skalnings uppsättning för virtuella datorer att respekteras när instanser tas bort?

I Azure-portalen har du möjlighet att ta bort en enskild instans eller Mass borttagning genom att välja flera instanser. Om du försöker ta bort en enskild instans som har ett lås på plats, respekteras låset och du kan inte ta bort instansen. Men om du väljer flera instanser och någon av dessa instanser har ett lås på plats, respekteras inte låset och alla markerade instanser tas bort. 
 
I Azure CLI har du bara möjlighet att ta bort en enskild instans. Om du försöker ta bort en enskild instans som har ett lås på plats, respekteras låset och du kan inte ta bort den instansen. 

## <a name="extensions"></a>Tillägg

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Hur tar jag bort tillägg för virtuell dator scale set?

Använd följande PowerShell-exempel för att ta bort tillägg för virtuell dator scale set:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Du kan hitta värdet tillägg i `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Finns det ett exempel på en mall för skalnings uppsättning för virtuell dator som kan integreras med Azure Monitor loggar?

Ett exempel på en mall för skalnings uppsättning för virtuell dator som integreras med Azure Monitor loggar finns i det andra exemplet i [distribuera ett Azure Service Fabric-kluster och aktivera övervakning med hjälp av Azure Monitor loggar](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hur lägger jag till ett tillägg för alla virtuella datorer i min skalningsuppsättning för virtuella datorer?

Om uppdaterings principen är inställd på **Automatisk**och distribuerar om mallen med de nya tilläggs egenskaperna uppdaterar alla virtuella datorer.

Om uppdaterings principen är inställd på **manuell**måste du först uppdatera tillägget och sedan manuellt uppdatera alla instanser i de virtuella datorerna.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Om de tillägg som är associerade med en befintlig skalningsuppsättning för virtuell dator har uppdaterats, är de befintliga virtuella datorer som påverkas?

Om tilläggs definitionen i den virtuella datorns skal uppsättnings modell uppdateras och upgradePolicy-egenskapen är inställd på **Automatisk**, uppdateras de virtuella datorerna. Om egenskapen upgradePolicy är inställd på **manuell**flaggas tillägg som inte matchar modellen.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Körs tilläggen igen när en befintlig dator är servad eller avbildningad?

Om en befintlig virtuell dator är servad visas den som en omstart och tilläggen körs inte igen. Om en virtuell dator har återställts på avbildningen, liknar processen samma som att ersätta operativ system enheten med käll avbildningen. Alla specialiseringar från den senaste modellen, till exempel tillägg, körs igen.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hur jag för att koppla en VM-skalningsuppsättning i en Active Directory-domän?

Du kan definiera ett tillägg för att ansluta till en VM-skalningsuppsättning i en Active Directory (AD)-domän.

Använd egenskapen JsonADDomainExtension för att definiera ett tillägg:

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Mina tillägg för skalningsuppsättning för virtuell dator försöker installera något som kräver en omstart.

Om dina tillägg för skalningsuppsättning för virtuell dator försöker installera något som kräver en omstart, kan du använda tillägget Azure Automation Desired State Configuration (Automation DSC). Om operativsystemet är Windows Server 2012 R2, Azure hämtar i installationsprogrammet för Windows Management Framework (WMF) 5.0, omstarter, och sedan vidare med konfigurationen.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Hur aktiverar jag program mot skadlig kod i min skalningsuppsättning för virtuella datorer?

Om du vill aktivera program mot skadlig kod på din VM-skalningsuppsättning, använder du följande PowerShell-exempel:

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

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hur gör jag för att köra ett anpassat skript som finns i ett privat lagrings konto?

Ställa in skyddade inställningarna med storage-kontonyckel och namn för att köra ett anpassat skript som finns i ett privat storage-konto. Mer information finns i [tillägg för anpassat skript](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Lösenord

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hur jag återställer lösenordet för virtuella datorer i min skalningsuppsättning för virtuella datorer?

Det finns två huvudsakliga sätt att ändra lösenordet för virtuella datorer i skalningsuppsättningar.

- Ändra VM-skalningsuppsättningen direkt. Tillgängligt med API 2017-12-01 och senare.

    Uppdatera administratörsautentiseringsuppgifter direkt i skalningsuppsättningen (till exempel med Azure Resource Explorer, PowerShell eller CLI). När skalningsuppsättningen är uppdaterade, alla nya har virtuella datorer de nya autentiseringsuppgifterna. Befintliga virtuella datorer har endast nya autentiseringsuppgifter om de avbildning återställs.

- Återställa lösenordet med hjälp av VM access-tillägg.

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

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Är det möjligt att tilldela en Nätverkssäkerhetsgrupp (NSG) till en skalningsuppsättning, så att den gäller för alla Virtuella nätverkskort i uppsättningen?

Ja. En Nätverkssäkerhetsgrupp kan tillämpas direkt på en skalningsuppsättning som refererar till den i networkInterfaceConfigurations-avsnitt av nätverksprofilen. Exempel:

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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hur gör jag en VIP-växling för skalningsuppsättningar för virtuella datorer i samma prenumeration och samma region?

Om du har två VM-skalningsuppsättningar med Azure Load Balancer klientdelar och de finns i samma prenumeration och region, kan du frigöra var och en offentlig IP-adresserna och tilldela till en annan. Se [VIP-växling: blå-grön distribution i Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) till exempel. Detta innebär en fördröjning nivå om resurserna som är frigjord/allokeras på nätverket. En snabbare alternativ är att använda Azure Application Gateway med två serverdelspooler och en regel för vidarebefordran. Alternativt kan du vara värd för ditt program med [Azure App tjänst](https://azure.microsoft.com/services/app-service/) som ger stöd för snabb växling mellan mellanlagrings-och produktions platser.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hur jag för att ange ett intervall med privata IP-adresser som ska användas för statiska privata IP-adressallokering?

IP-adresser har markerats från ett undernät som du anger.

Allokeringsmetoden för IP-adresser för VM scale set är alltid ”dynamiska”, men det betyder inte att du kan ändra dessa IP-adresser. I det här fallet innebär ”dynamiska” endast att du inte anger IP-adressen i en PUT-begäran. Ange den statiska ange med undernätet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hur distribuerar jag en VM-skalningsuppsättning till ett befintligt Azure virtuellt nätverk?

Information om hur du distribuerar en skalnings uppsättning för en virtuell dator till ett befintligt virtuellt Azure-nätverk finns i [distribuera en skalnings uppsättning för virtuella datorer till ett befintligt virtuellt nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan jag använda skalningsuppsättningar med Accelererat nätverk?

Ja. Om du vill använda accelererat nätverk, ange enableAcceleratedNetworking till true i din skalningsuppsättning uppsättningens networkInterfaceConfigurations inställningarna. Exempel
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

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hur kan jag konfigurera DNS-servrar som används av en skalningsuppsättning?

Lägg till en dnsSettings JSON-paket till skalningsuppsättningens networkInterfaceConfigurations-avsnitt om du vill skapa en VM-skalningsuppsättning med en anpassad DNS-konfiguration. Exempel:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hur konfigurerar jag en skalningsuppsättning för att tilldela en offentlig IP-adress till varje virtuell dator?

Om du vill skapa en virtuell dators skalnings uppsättning som tilldelar en offentlig IP-adress till varje virtuell dator kontrollerar du att API-versionen av Microsoft. Compute/virtualMachineScaleSets-resursen är 2017-03-30 och lägger till ett _publicipaddressconfiguration_ JSON-paket i avsnittet för skalnings uppsättning ipConfigurations. Exempel:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan jag konfigurera en skalningsuppsättning för att arbeta med flera Programgatewayer?

Ja. Du kan lägga till resurs-ID: n för flera Application Gateway backend-adresspooler i listan _applicationGatewayBackendAddressPools_ i avsnittet _ipConfigurations_ i nätverks profilen för skalnings uppsättningen.

## <a name="scale"></a>Skala

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>I vilka fall skulle jag skapa en VM-skalningsuppsättning med färre än två virtuella datorer?

Ett skäl till att skapa en VM-skalningsuppsättning med färre än två virtuella datorer är att använda de elastiska egenskaperna för en VM-skalningsuppsättning. Du kan till exempel distribuera en VM-skalningsuppsättning med virtuella datorer att definiera infrastrukturen utan att betala driftskostnader för virtuella datorer. Sedan, när du är redo att distribuera virtuella datorer, öka ”kapaciteten” för VM-skalningsuppsättningen angetts till antalet produktionsinstanser.

En annan orsak som du kan skapa en VM-skalningsuppsättning med färre än två virtuella datorer är om du vill göra mindre med tillgänglighet än i en tillgänglighetsuppsättning med diskreta virtuella datorer. Skalningsuppsättningar för virtuella datorer ger dig ett sätt att arbeta med odifferentierade beräkningsenheter som är utbytbara. Den här enhetligheten är en viktig skillnad mellan VM-skalningsuppsättningar och tillgänglighetsuppsättningar. Många tillståndslösa arbetsbelastningar do not track-enskilda enheter. Om arbetsbelastningen minskar, kan du skala ned till en beräkningsenhet och sedan skala upp till många när arbetsbelastningen ökar.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hur ändrar jag hur många virtuella datorer i en skalningsuppsättning för virtuell dator?

Om du vill ändra hur många virtuella datorer i en VM-skalningsuppsättning i Azure-portalen från VM-skalningsuppsättningen properties-avsnittet, klickar du på bladet ”skalning” och använda skjutreglaget.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hur definierar anpassade aviseringar för när vissa feltrösklarna uppnås?

Har du viss flexibilitet i hur du hanterar aviseringar för angivna tröskelvärden. Du kan till exempel definiera anpassade webhooks. I följande exempel för webhook är från en Resource Manager-mall:

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


## <a name="patching-and-operations"></a>Uppdatering och drift

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan jag skapa en skalnings uppsättning i en befintlig resurs grupp?

Ja, du kan skapa en skalnings uppsättning i en befintlig resurs grupp.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan jag flytta en skalnings uppsättning till en annan resurs grupp?

Ja, kan du flytta scale Sets resurser till en ny prenumeration eller resursgrupp.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hur du uppdaterar jag min skalningsuppsättning virtuell dator till en ny avbildning? Hur hanterar jag korrigeringar?

Om du vill uppdatera skalnings uppsättningen för den virtuella datorn till en ny avbildning och hantera korrigering, se [uppgradera en skalnings uppsättning för virtuella datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan jag använda avbildningsåterställningsåtgärden för att återställa en virtuell dator utan att ändra bilden? (Det vill säga jag vill återställa en virtuell dator till fabriksinställningarna i stället för en ny avbildning.)

Ja, du kan använda avbildningsåterställningsåtgärden för att återställa en virtuell dator utan att ändra bilden. Men om den virtuella datorns skalnings uppsättning refererar till en plattforms avbildning med `version = latest`, kan den virtuella datorn uppdateras till en senare OS-avbildning när du anropar `reimage`.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Är det möjligt att integrera skalnings uppsättningar med Azure Monitor loggar?

Ja, du kan installera tillägget Azure Monitor på de virtuella datorerna i skalnings uppsättningen. Här är ett Azure CLI-exempel:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Du hittar den nödvändiga workspaceId och workspaceKey i Log Analytics-arbetsyta för Azure-portalen. Klicka på panelen inställningar på sidan Översikt. Klicka på fliken anslutna källor högst upp.

> [!NOTE]
> Om din skalnings uppsättnings _upgradePolicy_ är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta _AZ VMSS Update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Hur aktiverar jag startdiagnostik?

Skapa först ett lagringskonto om du vill aktivera startdiagnostik. Lägg sedan till det här JSON-blocket i den virtuella datorns skalnings uppsättning **virtualMachineProfile**och uppdatera skalnings uppsättningen för den virtuella datorn:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

När en ny virtuell dator skapas, visar InstanceView-egenskapen för den virtuella datorn information för skärmbild och så vidare. Här är ett exempel:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hur får jag information om egenskaper för varje virtuell dator utan att göra flera anrop? Till exempel hur kan jag feldomänen för var och en av de 100 virtuella datorerna i min skalningsuppsättning för virtuella datorer?

Om du vill hämta egenskaps information för varje virtuell dator utan att göra flera anrop kan du anropa `ListVMInstanceViews` genom att göra en REST API `GET` på följande resurs-URI:

/subscriptions/ < subscription_id > /resourceGroups/ < resource_group_name > /providers/Microsoft.Compute/virtualMachineScaleSets/ < scaleset_name > / virtualMachines? $expand = instanceView & $select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan jag överföra annat tillägg argument till olika virtuella datorer i en skalningsuppsättning för virtuell dator?

Nej, du kan inte skicka annat tillägg argument till olika virtuella datorer i en skalningsuppsättning för virtuell dator. Tillägg kan dock fungera baserat på unika egenskaper för den virtuella datorn som de körs på, till exempel som på namnet på datorn. Tillägg kan också fråga efter instansen metadata på http://169.254.169.254 för att få mer information om den virtuella datorn.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Varför finns det glapp mellan Mina VM scale set VM datornamn och VM-ID: N? Till exempel: 0, 1, 3...

Det finns luckor mellan den virtuella datorns skalnings uppsättnings dator namn och VM-ID: t eftersom **egenskapen för** skalnings uppsättningen för den virtuella datorn har angetts till standardvärdet **True**. Om överetablering har angetts till **Sant**skapas fler virtuella datorer än vad som begärs. Extra virtuella datorerna tas sedan bort. I detta fall använder du få ökad distributionstillförlitlighet, men på bekostnad av sammanhängande namngivning och sammanhängande Network adress Translation (NAT) regler.

Du kan ställa in den här egenskapen på **false**. För små VM scale sets påverkar detta avsevärt inte distributionstillförlitlighet.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Vad är skillnaden mellan tar bort en virtuell dator i en skalningsuppsättning för virtuell dator och frigörs den virtuella datorn? När ska jag välja ett av alternativen?

Den största skillnaden mellan att ta bort en virtuell dator i en skalnings uppsättning för virtuella datorer och att ta bort den virtuella datorn är att `deallocate` inte tar bort de virtuella hård diskarna (VHD). Det finns lagrings kostnader som är kopplade till att köra `stop deallocate`. Du kan använda en av för någon av följande orsaker:

- Vill du avbryta betala beräkningskostnaderna, men du vill behålla diskstatusen för de virtuella datorerna.
- Du vill starta en uppsättning virtuella datorer snabbare än du kan skala ut en skalningsuppsättning för virtuell dator.
  - Relaterat till det här scenariot, kan du ha skapat en egen motor för automatisk skalning och vill en snabbare slutpunkt till slutpunkt-skala.
- Du har en skalningsuppsättning för virtuella datorer som är ojämnt fördelade mellan feldomäner och uppdateringsdomäner. Det kan vara eftersom du selektivt ta bort virtuella datorer eller virtuella datorer har tagits bort efter överetablering. Om du kör `stop deallocate` följt av `start` på den virtuella datorns skalnings uppsättning distribuerar de virtuella datorerna jämnt över fel domäner eller uppdaterings domäner.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hur gör jag för att ta en ögonblicks bild av en instans av en skalnings uppsättning för virtuella datorer?
Skapa en ögonblicks bild från en instans av en skalnings uppsättning för virtuella datorer.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Skapa en hanterad disk från ögonblicks bilden.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
