---
title: Distribuera OpenShift Container Platform självhanterade Marketplace-erbjudandet i Azure | Microsoft Docs
description: Distribuera OpenShift Container Platform självhanterade Marketplace-erbjudandet i Azure.
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
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 1228c770799de37c85b8a48b1dc923ac8294eeca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773611"
---
# <a name="configure-prerequisites"></a>Konfigurera krav

Innan du använder Marketplace-erbjudande för att distribuera en självhanterad OpenShift Container Platform-kluster i Azure, måste några förutsättningar konfigureras.  Läs den [krav för OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) artikeln anvisningar att skapa en ssh-nyckel (utan en lösenfras), Azure-nyckelvalv, key vault-hemlighet och ett huvudnamn för tjänsten.

 
## <a name="deploy-using-the-marketplace-offer"></a>Distribuera med hjälp av Marketplace-erbjudande

Det enklaste sättet att distribuera en självhanterad OpenShift Container Platform-kluster till Azure är att använda den [Azure Marketplace-erbjudande](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Det här alternativet är den enklaste, men det kan också har begränsade funktioner för anpassning. Marketplace-erbjudande distribuerar OpenShift Container Platform 3.11.82 och innehåller följande konfigurationsalternativ:

- **Master noder**: Tre (3) Master-noder med konfigurerbara instans skriver.
- **Infra noder**: Tre (3) Infra noder med konfigurerbara instans skriver.
- **Noder**: Antalet noder (mellan 1 och 9) och Instanstypen kan konfigureras.
- **Disktyp**: Hanterade diskar används.
- **Nätverk**: Stöd för nya eller befintliga nätverk och anpassade CIDR-intervall.
- **CNS**: CNS kan aktiveras.
- **Mått**: Mått kan aktiveras.
- **Loggning**: Du kan aktivera loggning.
- **Azure Cloud Provider**: Aktiverad som standard kan inaktiveras.

I det övre vänstra hörnet i Azure Portal klickar du på **skapa en resurs**anger 'openshift container platform ”i sökrutan och tryck på RETUR.

   ![Ny resurs-sökning](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Resultatsidan öppnas med **Red Hat OpenShift Container Platform Self-Managed** i listan. 

   ![Ny resurs-sökresultat](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klicka på erbjudandet att visa information om erbjudandet. Om du vill distribuera det här erbjudandet, klickar du på **skapa**. Gränssnittet för att ange nödvändiga parametrar visas. Den första skärmen är den **grunderna** bladet.

   ![Sidan för erbjudande titel](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Grundläggande inställningar**

För att få hjälp på någon av indataparametrarna som kan hovra över den ***jag*** bredvid parameternamnet på.

Ange värden för indataparametrarna och klicka på **OK**.

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Namn på virtuell dator administratörsanvändare | Administratörsanvändare som ska skapas på alla VM-instanser |
| SSH offentlig nyckel för administratörsanvändare | Offentlig SSH-nyckel används för att logga in på VM - får inte ha en lösenfras |
| Prenumeration | Azure-prenumeration att distribuera kluster till |
| Resursgrupp | Skapa en ny resursgrupp eller välj en befintlig tom resursgrupp för klusterresurser |
| Location | Att distribuera kluster till Azure-region |

   ![Erbjud bladet grundläggande inställningar](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Inställningar för infrastruktur**

Ange värden för indataparametrarna och klicka på **OK**.

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Namnprefix för OCP-kluster | Administratörsanvändare som ska skapas på alla VM-instanser |
| Master nodstorlek | Acceptera standardstorleken för virtuella datorer eller klicka på **ändra storleken på** att välja en annan VM-storlek.  Välj lämplig virtuell datorstorlek för din arbetsbelastning |
| Nodstorlek för infrastruktur | Acceptera standardstorleken för virtuella datorer eller klicka på **ändra storleken på** att välja en annan VM-storlek.  Välj lämplig virtuell datorstorlek för din arbetsbelastning |
| Antalet noder som programmet | Acceptera standardstorleken för virtuella datorer eller klicka på **ändra storleken på** att välja en annan VM-storlek.  Välj lämplig virtuell datorstorlek för din arbetsbelastning |
| Storlek för noden | Acceptera standardstorleken för virtuella datorer eller klicka på **ändra storleken på** att välja en annan VM-storlek.  Välj lämplig virtuell datorstorlek för din arbetsbelastning |
| Skyddsmiljö-värd storlek | Acceptera standardstorleken för virtuella datorer eller klicka på **ändra storleken på** att välja en annan VM-storlek.  Välj lämplig virtuell datorstorlek för din arbetsbelastning |
| Nytt eller befintligt virtuellt nätverk | Skapa ett nytt virtuellt nätverk (standard) eller använda ett befintligt vNet |
| Välj standardinställningar för CIDR eller anpassa IP-adressintervall (CIDR) | Acceptera standardvärdet CIDR-intervall eller välj **anpassade IP-adressintervall** och ange anpassade CIDR-information.  Standardinställningarna ska skapa vNet med CIDR av 10.0.0.0/14, master undernätet med 10.1.0.0/16 infra undernätet med 10.2.0.0/16 och beräknings- och cns undernät med 10.3.0.0/16 |
| Namn på Key Vault-resursgrupp | Namnet på resursgruppen som innehåller Nyckelvalvet |
| Namn på Key Vault | Namnet på det Nyckelvalv som innehåller hemligheten med det ssh privat nyckel.  Endast alfanumeriska tecken och tankstreck tillåts och innehålla mellan 3 och 24 tecken |
| Hemligt namn | Namnet på den hemlighet som innehåller den ssh privat nyckel.  Endast alfanumeriska tecken och tankstreck tillåts |

   ![Bladet infrastruktur för erbjudandet](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Ändra storlek**

Klicka för att välja en annan virtuell storlek ***ändra storleken på***.  Urvalsfönster för virtuell dator öppnas.  Välj VM-storlek och på **Välj**.

   ![Välj VM-storlek](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Befintligt virtuellt nätverk**

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Namn på befintliga virtuellt nätverk | Namnet på det befintliga virtuella nätverket |
| Namn på undernät för överordnade noder | Namnet på befintliga undernätet för överordnade noder.  Måste innehålla minst 16 IP-adresser och följer RFC 1918 |
| Namnet på undernätet för infrastruktur noder | Namnet på befintliga undernätet för infrastruktur noder.  Måste innehålla minst 32 IP-adresser och följer RFC 1918 |
| Namn på undernät för beräknings- och cns noder | Namnet på befintliga undernätet för beräknings- och cns noder.  Måste innehålla minst 32 IP-adresser och följer RFC 1918 |
| Resursgruppen för det befintliga virtuella nätverket | Namnet på resursgruppen som innehåller det befintliga virtuella nätverket |

   ![Erbjud infrastruktur befintligt virtuellt nätverk](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Anpassade IP-intervall**

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Adressintervallet för det virtuella nätverket | Anpassad CIDR för det virtuella nätverket |
| Adressintervall för undernätet som innehåller de överordnade noderna | Anpassad CIDR för master-undernät |
| Adressintervall för undernätet som innehåller infrastrukturnoder | Anpassad CIDR för undernätet för infrastruktur |
| Adressintervall för undernätet som innehåller beräknings- och cns noder | Anpassade CIDR för beräknings- och cns noder |

   ![Erbjud infrastruktur anpassade IP-adressintervall](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Ange värden för parametrar för indata och klicka på **OK**

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Administratörslösenord för OpenShift | Lösenordet för det ursprungliga OpenShift-användarkontot.  Den här användaren ska också vara administratör för kluster |
| Bekräfta administratörslösenord för OpenShift | Skriv administratörslösenord för OpenShift |
| Red Hat prenumeration Manager-användarnamn | Användarnamn för att komma åt dina Red Hat-prenumeration eller organisation-ID.  Den här autentiseringsuppgiften används för att registrera RHEL-instans till din prenumeration och lagras inte av Microsoft eller Red Hat |
| Red Hat prenumeration Manager användarlösenord | Lösenord för åtkomst till dina Red Hat-prenumeration eller aktiveringsnyckeln.  Den här autentiseringsuppgiften används för att registrera RHEL-instans till din prenumeration och lagras inte av Microsoft eller Red Hat |
| Red Hat prenumeration Manager OpenShift Pool-ID | Pool-ID som innehåller OpenShift Container Platform rättigheten. Kontrollera att du har tillräckligt med rättigheter för OpenShift Container Platform för installation av klustret |
| Red Hat prenumeration Manager OpenShift Pool-ID för Broker / Master-noder | Pool-ID som innehåller OpenShift Container Platform rättigheter för Broker / Master-noder. Kontrollera att du har tillräckligt med rättigheter för OpenShift Container Platform för installation av klustret. Om du inte använder broker / master-pool-ID, ange pool-ID för programmet noder |
| Konfigurera Azure-molnet Provider | Konfigurera OpenShift för att använda Azure Cloud-providern. Nödvändigt om du använder Azure disk bifoga för beständiga volymer.  Standardinställningen är Ja |
| Azure AD Service Principal Client ID GUID | Azure AD Service Principal Client ID GUID - kallas även AppID. Behövs bara om konfigurera Azure Molnleverantör **Ja** |
| Azure AD-tjänstens huvudnamn ID Klienthemlighet | Azure AD-tjänstens huvudnamn ID Klienthemlighet. Behövs bara om konfigurera Azure Molnleverantör **Ja** |
 
   ![Erbjudandet OpenShift bladet](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Ytterligare inställningar**

Ytterligare inställningar tillåter konfiguration av CNS för glusterfs lagring, bland annat loggning och mått Router Sub domän.  Standard går inte att installera något av dessa alternativ och använder nip.io som underdomän router i testsyfte. Aktivera CNS installerar tre extra beräkningsnoder med tre ytterligare anslutna diskar som är värd för glusterfs poddar.  

Ange värden för parametrar för indata och klicka på **OK**

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| Konfigurera intern lagring för behållaren (CN) | Installerar CNS i OpenShift kluster och aktivera det som lagring. Är standard om Azure-providern är inaktiverad |
| Konfigurera kluster-loggning | Installerar EFK loggningsfunktionen till klustret.  Ändra storlek på infra noder korrekt till värd EFK poddar |
| Konfigurera mått för klustret | Installerar Hawkular mått till OpenShift-klustret.  Ändra storlek på infra noder korrekt till värd Hawkular mått poddar |
| Standard-Router-underdomän | Välj nipio för att testa eller anpassade att ange en egen underdomän för produktion |
 
   ![Erbjud ytterligare ett blad](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Ytterligare inställningar – Extra parametrar**

| Indataparameter | Parameterbeskrivning |
|-----------------------|-----------------|
| (CNS) Nodstorlek | Acceptera standardstorlek för noden eller välj **ändra storleken på** att välja en ny VM-storlek |
| Ange din anpassade underdomän | Den anpassade routningsdomän som ska användas för att exponera program via router i OpenShift-klustret.  Var noga med att skapa DNS-post lämpliga jokertecken] |
 
   ![Erbjuder ytterligare cns installera](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Sammanfattning**

Verifieringen sker i det här skedet att kontrollera kärnkvoten är tillräckliga för att distribuera det totala antalet virtuella datorer som valts för klustret.  Granska de parametrar som angivits.  Om indata är godkända klickar du på **OK** att fortsätta.

   ![Sammanfattningsbladet för erbjudandet](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Köp**

Bekräfta kontaktinformation på sidan Köp och klicka på **köp** att acceptera användningsvillkoren och starta distribution av OpenShift Container Platform-klustret.

   ![Erbjudandet köp bladet](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Anslut till klustret för OpenShift

När distributionen är klar kan du hämta anslutningen från utdataavsnittet i distributionen. Ansluta till konsolen OpenShift med din webbläsare med hjälp av den **OpenShift-konsolens URL**. Du kan också SSH till Skyddsmiljö-värd. Följande är ett exempel där administratörsanvändarnamnet är clusteradmin och den offentliga IP för skyddsmiljö DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd den [az group delete](/cli/azure/group) att ta bort resursgruppen, OpenShift klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-troubleshooting.md)
- [Komma igång med OpenShift Container Platform](https://docs.openshift.com/container-platform)

