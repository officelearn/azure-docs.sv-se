---
title: Anpassade data och virtuella Azure-datorer
description: Information om hur du använder anpassade data och Cloud-Init på virtuella Azure-datorer
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109633"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Anpassade data och Cloud-Init på virtuella Azure-datorer

## <a name="what-is-custom-data"></a>Vad är anpassade data?

Kunder frågar ofta hur de kan injicera ett skript eller andra metadata i en virtuell Microsoft Azure-dator vid etableringstillfället.  I andra moln kallas det här konceptet ofta för användardata.  I Microsoft Azure har vi en liknande funktion som kallas anpassade data. 

Anpassade data görs endast tillgängliga för den virtuella datorn under första start/första installationen, vi kallar detta "etablering". Etablering är den process där VM Skapa parametrar (till exempel värdnamn, användarnamn, lösenord, certifikat, anpassade data, nycklar etc.) görs tillgängliga för den virtuella datorn och en etablering agent bearbetar dem, till exempel [Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) och [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init). 


## <a name="passing-custom-data-to-the-vm"></a>Överföra anpassade data till den virtuella datorn
Om du vill använda anpassade data måste du basera64-koda innehållet först innan du skickar det till API:et, såvida du inte använder ett CLI-verktyg som gör konverteringen åt dig, till exempel AZ CLI. Storleken får inte överstiga 64 kB.

I CLI kan du skicka dina anpassade data som en fil, och den konverteras till base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

I Azure Resource Manager (ARM) finns en [base64-funktion](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Bearbeta anpassade data
De etableringsagenter som är installerade på de virtuella datorerna hanterar gränssnitt med plattformen och placerar den på filsystemet. 

### <a name="windows"></a>Windows
Anpassade data placeras i *%SYSTEMDRIVE%\AzureData\CustomData.bin* som en binär fil, men den bearbetas inte. Om du vill bearbeta den här filen måste du skapa en anpassad avbildning och skriva kod för att bearbeta CustomData.bin.

### <a name="linux"></a>Linux  
På Linux OS skickas anpassade data till den virtuella datorn via filen ovf-env.xml, som kopieras till katalogen */var/lib/waagent* under etableringen.  Nyare versioner av Microsoft Azure Linux Agent kopierar också base64-kodade data till */var/lib/waagent/CustomData* också för enkelhetens skull.

Azure stöder för närvarande två etableringsagenter:
* Linux Agent - Som standard agenten inte kommer att behandla anpassade data, måste du bygga en anpassad avbildning med den aktiverad. De relevanta inställningarna, enligt [dokumentationen](https://github.com/Azure/WALinuxAgent#configuration) är:
    * Provisioning.DecodeCustomData
    * Etablering.ExecuteCustomData

När du aktiverar anpassade data och kör ett skript försenas den virtuella datorns rapportering som är klar eller att etableringen har lyckats tills skriptet har slutförts. Om skriptet överskrider den totala vm-etableringstiden på 40 minuter misslyckas den virtuella datorns skapad. Om skriptet inte körs, eller fel under körningen, anses det inte vara ett allvarligt etableringsfel, måste du skapa en meddelandesökväg för att varna dig för skriptets slutförandetillstånd.

Om du vill felsöka anpassad datakörning granskar du */var/log/waagent.log*

* cloud-init - Som standard kommer att behandla anpassade data som standard, cloud-init accepterar [flera format](https://cloudinit.readthedocs.io/en/latest/topics/format.html) av anpassade data, såsom cloud-init konfiguration, skript etc. Liknar Linux-agenten när cloud-init bearbetar anpassade data. Om det finns fel under körningen av konfigurationsbearbetningen eller skripten anses det inte vara ett allvarligt etableringsfel och du måste skapa en meddelandesökväg för att varna dig för skriptets slutförandetillstånd. Men skiljer sig från Linux-agenten, cloud-init väntar inte på användarens anpassade datakonfigurationer för att slutföra innan du rapporterar till den plattform som den virtuella datorn är klar. Mer information om cloud-init på azure finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Om du vill felsöka anpassad datakörning läser du [felsökningsdokumentationen](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init).


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kan jag uppdatera anpassade data när den virtuella datorn har skapats?
För enskilda virtuella datorer kan anpassade data i VM-modellen inte uppdateras, men för VMSS kan du uppdatera ANPASSADE VMSS-data via REST API (gäller inte för PS- eller AZ CLI-klienter). När du uppdaterar anpassade data i VMSS-modellen:
* Befintliga instanser i VMSS kommer inte att få uppdaterade anpassade data, bara tills de är omgjorda.
* Befintliga instanser i VMSS som uppgraderas får inte de uppdaterade anpassade data.
* Nya instanser får nya anpassade data.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kan jag placera känsliga värden i anpassade data?
Vi rekommenderar att **du inte** lagrar känsliga data i anpassade data. Mer information finns i [metodtips för Azure Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices).


### <a name="is-custom-data-made-available-in-imds"></a>Är anpassade data tillgängliga i IMDS?
Nej, den här funktionen är inte tillgänglig för tillfället.
