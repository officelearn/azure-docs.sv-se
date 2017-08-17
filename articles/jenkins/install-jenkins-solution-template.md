---
title: "Skapa din första Jenkins-huvudserver på en virtuell Linux-dator (Ubuntu) i Azure"
description: "Använd lösningsmallen när du ska distribuera Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: sv-se
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Skapa din första Jenkins-huvudserver på en virtuell Linux-dator (Ubuntu) i Azure

I den här snabbstarten visas hur du installerar den senaste säkra Jenkins-versionen på en virtuell Linux-dator (Ubuntu 14.04 LTS) tillsammans med de verktyg och plugin-program som konfigurerats för att fungera med Azure. Här är några av verktygen:
<ul>
<li>Git för källkontroll</li>
<li>Plugin-program för Azure-inloggning för säkra anslutningar</li>
<li>Plugin-programmet Azure VM Agents för flexibelt skapande, tester och kontinuerlig integration</li>
<li>Plugin-programmet Azure Storage för lagring av artefakter</li>
<li>Azure CLI för distribution av appar med hjälp av skript</li>
</ul>

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett kostnadsfritt Azure-konto.
> * Skapa en Jenkins-huvudserver på en virtuell Azure-dator med en lösningsmall. 
> * Utför inledande konfiguration för Jenkins.
> * Installera föreslagna plugin-program.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Skapa den virtuella datorn i Azure genom att distribuera lösningsmallen för Jenkins

Med Azure-snabbstartsmallar kan du snabbt och tillförlitligt distribuera komplex teknik i Azure.  Med Azure Resource Manager kan du etablera dina program med hjälp av en [deklarativ mall](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins). I samma mall kan du distribuera flera tjänster tillsammans med deras beroenden. Du använder samma mall till att upprepade gånger distribuera ditt program i varje fas av programmets livscykel.

Visa information om [prenumerationsavtal och priser](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) för den här mallen med olika kostnadsalternativ.

Gå till [marketplace-avbildningen för Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) och klicka på **HÄMTA NU**  

Klicka på **Skapa** i Azure Portal.  För den här mallen måste du använda Resource Manager, så listrutan med distributionsmodeller är inaktiverad.
   
![Dialogruta i Azure Portal](./media/install-jenkins-solution-template/ap-create.png)

Gör så här på fliken **Konfigurera grundläggande inställningar**:

![Konfigurera grundläggande inställningar](./media/install-jenkins-solution-template/ap-basic.png)

* Ange ett namn för din Jenkins-instans.
* Välj en typ av VM-disk.  Välj en större VM och SSD om du vill ha bättre prestanda för produktionsarbetsbelastningar.  Du kan läsa mer om olika Azure-disktyper [här](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage).
* Användarnamn: måste uppfylla längdkraven och får inte innehålla reserverade ord eller tecken som inte stöds. Namn som ”admin” är inte tillåtna.  Mer information om krav på användarnamn och lösenord finns [här](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq).
* Autentiseringstyp: skapa en instans som skyddas med lösenord eller en [offentlig SSH-nyckel](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). Om du använder lösenord måste det innehålla minst 3 av följande: en gemen, en versal, en siffra och ett specialtecken.
* Behåll Jenkins-versionstypen som **LTS**
* Välj en prenumeration.
* Skapa en resursgrupp eller använd en befintlig resursgrupp som är tom. 
* Välj en plats.

Gör så här på fliken **Konfigurera ytterligare alternativ**:

![Ställ in ytterligare alternativ](./media/install-jenkins-solution-template/ap-addtional.png)

* Ange en domännamnsetikett som identifierar Jenkins-huvudservern unikt.

Klicka på **OK** för att gå till nästa steg. 

När valideringen lyckats klickar du på **OK** för att ladda ned mallen och parametrarna. 

Välj sedan **Köp** så att resurserna etableras.

## <a name="setup-ssh-port-forwarding"></a>Ställ in portvidarebefordran för SSH

Som standard används http-protokollet och port 8080 för Jenkins-instansen. Användare bör inte autentisera över oskyddade protokoll.
    
Ställ in portvidarebefordran om du vill se Jenkins-gränssnittet på din lokala dator.

### <a name="if-you-are-using-windows"></a>Om du använder Windows:

Installera PuTTY och kör det här kommandot om du använder lösenord som skydd för Jenkins:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Ange lösenordet för att logga in.

![Ange lösenordet för att logga in.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Kör det här kommandot om du använder SSH:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Om du använder Linux eller Mac:

Om du använder ett lösenord som skydd för Jenkins-huvudservern kör du det här kommandot:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Ange lösenordet för att logga in.

Kör det här kommandot om du använder SSH:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Ansluta till Jenkins
När du har startat din tunnel navigerar du till http://localhost:8080/ på den lokala datorn.

Första gången låser du upp Jenkins-instrumentpanelen med det initiala administratörslösenordet.

![Låsa upp Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Du får en token genom att använda SSH till den virtuella datorn och köra `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Låsa upp Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

Du uppmanas att installera de föreslagna plugin-programmen.

Skapa sedan en administratörsanvändare för Jenkins-huvudservern.

Nu kan du börja använda Jenkins-instansen! Du kan se en skrivskyddad vy genom att gå till http://\<det offentliga DNS-namnet på instansen du nyss skapade\>.

![Jenkins är redo!](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>Nästa steg

I den här kursen har du:

> [!div class="checklist"]
> * Skapat en Jenkins-huvudserver med hjälp av lösningsmallen.
> * Utfört en inledande konfiguration av Jenkins.
> * Installerat plugin-program.

Följ den här länken om du vill lära dig att använda virtuella datoragenter i Azure för kontinuerlig integrering med Jenkins.

> [!div class="nextstepaction"]
> [Virtuella Azure-datorer som Jenkins-agenter](jenkins-azure-vm-agents.md)

