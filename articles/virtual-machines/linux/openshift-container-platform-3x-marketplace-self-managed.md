---
title: Distribuera OpenShift Container Platform 3.11 Självhanterad Marketplace-erbjudande i Azure
description: Distribuera OpenShift Container Platform 3.11 Självhanterad Marketplace-erbjudande i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759570"
---
# <a name="configure-prerequisites"></a>Konfigurera förutsättningar

Innan du använder Marketplace-erbjudandet för att distribuera ett självhanterat OpenShift Container Platform 3.11-kluster i Azure måste några förutsättningar konfigureras.  Läs [openshift-förkunskapsartikeln](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) för instruktioner om hur du skapar en ssh-nyckel (utan lösenfras), Azure-nyckelvalv, nyckelvalvshemlighet och ett tjänsthuvudnamn.

 
## <a name="deploy-using-the-marketplace-offer"></a>Distribuera med Marketplace-erbjudandet

Det enklaste sättet att distribuera ett självhanterade OpenShift Container Platform 3.11-kluster till Azure är att använda [Azure Marketplace-erbjudandet](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Det här alternativet är det enklaste, men det har också begränsade anpassningsfunktioner. Marketplace-erbjudandet distribuerar OpenShift Container Platform 3.11.82 och innehåller följande konfigurationsalternativ:

- **Huvudnoder:** Tre (3) huvudnoder med konfigurerbar instanstyp.
- **Infra noder:** Tre (3) Infra noder med konfigurerbar instanstyp.
- **Noder**: Antalet noder (mellan 1 och 9) och instanstypen kan konfigureras.
- **Disktyp:** Hanterade diskar används.
- **Nätverk:** Stöd för nytt eller befintligt nätverk och anpassat CIDR-intervall.
- **CNS**: CNS kan aktiveras.
- **Mått**: Hawkular Metrics kan aktiveras.
- **Loggning**: EFK-loggning kan aktiveras.
- **Azure Cloud Provider:** Aktiverad som standard, kan inaktiveras.

Längst upp till vänster i Azure-portalen klickar du på **Skapa en resurs,** anger "openshift container platform" i sökrutan och trycker på Retur.

   ![Ny resurssökning](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Resultatsidan öppnas med **Red Hat OpenShift Container Platform 3.11 Självhanterad** i listan. 

   ![Nytt resurssökresultat](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klicka på erbjudandet om du vill visa information om erbjudandet. Om du vill distribuera det här erbjudandet klickar du på **Skapa**. Användargränssnittet för att ange nödvändiga parametrar visas. Den första skärmen är **Basics-bladet.**

   ![Titelsida för erbjudande](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Grundläggande inställningar**

Om du vill ha hjälp med någon av indataparametrarna håller du muspekaren över ***i*** bredvid parameternamnet.

Ange värden för indataparametrarna och klicka på **OK**.

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Användarnamn för VM-administratör | Administratörsanvändaren som ska skapas på alla VM-instanser |
| SSH Offentlig nyckel för administratörsanvändare | SSH offentlig nyckel som används för att logga in på VM - får inte ha en lösenfras |
| Prenumeration | Azure-prenumeration för att distribuera kluster till |
| Resursgrupp | Skapa en ny resursgrupp eller välj en befintlig tom resursgrupp för klusterresurser |
| Location | Azure-region för att distribuera kluster till |

   ![Erbjudandet grunderna blad](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Inställningar för infrastruktur**

Ange värden för indataparametrarna och klicka på **OK**.

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Prefix för OCP-klusternamn | Klusterprefix som används för att konfigurera värdnamn för alla noder. Mellan 1 och 20 tecken |
| Storlek på huvudnod | Acceptera standardstorleken för virtuella datorer eller klicka på **Ändra storlek** för att välja en annan vm-storlek.  Välj lämplig VM-storlek för din arbetsbelastning |
| Storlek på infrastrukturnod | Acceptera standardstorleken för virtuella datorer eller klicka på **Ändra storlek** för att välja en annan vm-storlek.  Välj lämplig VM-storlek för din arbetsbelastning |
| Antal programnoder | Acceptera standardstorleken för virtuella datorer eller klicka på **Ändra storlek** för att välja en annan vm-storlek.  Välj lämplig VM-storlek för din arbetsbelastning |
| Storlek på programnod | Acceptera standardstorleken för virtuella datorer eller klicka på **Ändra storlek** för att välja en annan vm-storlek.  Välj lämplig VM-storlek för din arbetsbelastning |
| Bastion värdstorlek | Acceptera standardstorleken för virtuella datorer eller klicka på **Ändra storlek** för att välja en annan vm-storlek.  Välj lämplig VM-storlek för din arbetsbelastning |
| Nytt eller befintligt virtuellt nätverk | Skapa ett nytt vNet (standard) eller använd ett befintligt virtuella nätverk |
| Välj Standardinställningar för CIDR eller anpassa IP Range (CIDR) | Acceptera standard-CIDR-intervall eller Välj **anpassat IP-intervall** och ange anpassad CIDR-information.  Standardinställningar skapar vNet med CIDR på 10.0.0.0/14, huvudundernät med 10.1.0.0/16, infra undernät med 10.2.0.0/16 och beräkna och cns undernät med 10.3.0.0/16 och beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna och cns undernät med 10.3.0.0/16 samt beräkna |
| Namn på resursgrupp för nyckelvalv | Namnet på resursgruppen som innehåller Nyckelvalvet |
| Namn på nyckelvalv | Namnet på key vault som innehåller hemligheten med ssh privat nyckel.  Endast alfanumeriska tecken och streck är tillåtna och mellan 3 och 24 tecken |
| Hemligt namn | Namnet på hemligheten som innehåller den privata nyckeln ssh.  Endast alfanumeriska tecken och streck är tillåtna |

   ![Erbjud infrastrukturblad](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Ändra storlek**

Om du vill välja en annan vm-storlek klickar du på ***Ändra storlek***.  Valet av virtuell dator öppnas.  Välj den vm-storlek du vill använda och klicka på **Välj**.

   ![Välj VM-storlek](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Befintligt virtuellt nätverk**

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Befintligt namn för virtuellt nätverk | Namn på det befintliga virtuella nätverket |
| Undernätsnamn för huvudnoder | Namn på befintligt undernät för huvudnoder.  Måste innehålla minst 16 IP-adresser och följa RFC 1918 |
| Undernätsnamn för infranoder | Namn på befintligt undernät för infranoder.  Måste innehålla minst 32 IP-adresser och följa RFC 1918 |
| Undernätsnamn för beräknings- och cns-noder | Namn på befintliga undernät för beräknings- och cns-noder.  Måste innehålla minst 32 IP-adresser och följa RFC 1918 |
| Resursgrupp för det befintliga virtuella nätverket | Namn på resursgrupp som innehåller det befintliga virtuella nätverket |

   ![Erbjud befintligt internet för infrastruktur](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Anpassat IP-intervall**

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Adressintervall för det virtuella nätverket | Anpassad CIDR för det virtuella nätverket |
| Adressintervall för undernätet som innehåller huvudnoderna | Anpassad CIDR för huvudundernät |
| Adressintervall för undernätet som innehåller infrastrukturnoderna | Anpassad CIDR för infrastrukturundernät |
| Adressintervall för undernät som innehåller beräknings- och cns-noder | Anpassad CIDR för beräknings- och cns-noder |

   ![Erbjud anpassat IP-intervall för infrastruktur](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Ange värden för indataparametrarna och klicka på **OK**

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Användarnamn för OpenShift-administratör | Lösenord för den ursprungliga OpenShift-användaren.  Den här användaren kommer också att vara klusteradministratör |
| Bekräfta openshift-administratörsanvändarlösenord | Skriv in openshift-administratörslösenordet på nytt |
| Användarnamn för Red Hat-prenumerationshanteraren | Användarnamn för att komma åt din Red Hat-prenumeration eller organisations-ID.  Den här autentiseringsenheten används för att registrera RHEL-instansen i din prenumeration och kommer inte att lagras av Microsoft eller Red Hat |
| Användarlösenord för Red Hat-prenumerationshanteraren | Lösenord för att komma åt din Red Hat-prenumeration eller aktiveringsnyckel.  Den här autentiseringsenheten används för att registrera RHEL-instansen i din prenumeration och kommer inte att lagras av Microsoft eller Red Hat |
| OpenShift-pool-ID för Red Hat-prenumerationshanteraren | Pool-ID som innehåller openshift-behållarens plattformsberättigande. Se till att du har tillräckligt med rättigheter för OpenShift Container Platform för installation av klustret |
| Red Hat Prenumeration Manager OpenShift Pool ID för mäklare / Master Noder | Pool-ID som innehåller OpenShift Container Platform-berättiganden för broker/master-noder. Se till att du har tillräckligt med rättigheter för OpenShift Container Platform för installation av klustret. Om du inte använder mäklare / huvudpool-ID anger du pool-ID för programnoder |
| Konfigurera Azure Cloud Provider | Konfigurera OpenShift för att använda Azure Cloud Provider. Nödvändigt om du använder Azure-disken bifoga för beständiga volymer.  Standard är Ja |
| AZURE AD-tjänsthuvudnamn klient-ID GUID | Azure AD-tjänsthuvuden klient-ID GUID - även känd som AppID. Behövs endast om Konfigurera Azure Cloud Provider inställd på **Ja** |
| Klient-ID för Azure AD-tjänsts huvudnamn | Klient-ID för Azure AD-tjänsts huvudklient-ID. Behövs endast om Konfigurera Azure Cloud Provider inställd på **Ja** |
 
   ![Erbjud OpenShift-blad](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Ytterligare inställningar**

Med bladet Ytterligare inställningar kan du konfigurera CNS för glusterfslagring, loggning, mått och routerunderdomän.  Standardinställningen kommer inte att installera något av dessa alternativ och kommer att använda nip.io som routerns underdomän för testning. Om du aktiverar CNS installeras ytterligare tre beräkningsnoder med ytterligare tre anslutna diskar som ska vara värd för glusterfs-poddar.  

Ange värden för indataparametrarna och klicka på **OK**

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Konfigurera inbyggd lagring av behållare (CNS) | Installerar CNS i OpenShift-klustret och aktiverar det som lagring. Standard om Azure Provider är inaktiverat |
| Konfigurera klusterloggning | Installerar EFK-loggningsfunktioner i klustret.  Storlek infra noder på lämpligt sätt för att vara värd för EFK pods |
| Konfigurera mått för klustret | Installerar Hawkular-mått i OpenShift-klustret.  Storlek infra noder på lämpligt sätt för att vara värd Hawkular mått pods |
| Standarddomän för routerunderdomän | Välj nipio för testning eller anpassad för att ange din egen underdomän för produktion |
 
   ![Erbjud ytterligare blad](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Ytterligare inställningar - Extra parametrar**

| Parameter för indata | Beskrivning av parametern |
|-----------------------|-----------------|
| Jag är mycket av den här lyssa av dessa. Nodstorlek | Acceptera standardnodstorleken eller välj **Ändra storlek** för att välja en ny vm-storlek |
| Ange din anpassade underdomän | Den anpassade routningsdomän som ska användas för att exponera program via routern i OpenShift-klustret.  Var noga med att skapa lämplig DNS-post för jokertecken] |
 
   ![Erbjud ytterligare cns Installera](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Sammanfattning**

Valideringen sker i det här skedet för att kontrollera kärnkvoten är tillräcklig för att distribuera det totala antalet virtuella datorer som valts för klustret.  Granska alla parametrar som har angetts.  Om ingångarna är acceptabla klickar du på **OK** för att fortsätta.

   ![Sammanfattningsblad för erbjudande](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Köp**

Bekräfta kontaktinformation på sidan Köp och klicka på **Köp** för att acceptera användarvillkoren och starta distributionen av OpenShift Container Platform-klustret.

   ![Köpblad för erbjudande](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Ansluta till OpenShift-klustret

När distributionen är klar hämtar du anslutningen från utdataavsnittet i distributionen. Anslut till OpenShift-konsolen med webbläsaren med hjälp av **Url:en för OpenShift-konsolen**. du kan också SSH till Bastion värd. Följande är ett exempel där administratören användarnamn är clusteradmin och bastion offentliga IP DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Komma igång med OpenShift Container Platform](https://docs.openshift.com)
- 